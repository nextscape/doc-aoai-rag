# データストアの検索クエリ機能のアップデート

- [データストアの検索クエリ機能のアップデート](#データストアの検索クエリ機能のアップデート)
  - [概要](#概要)
  - [データストアサービスの新機能](#データストアサービスの新機能)
  - [各種サービスにおける検索クエリの実行例](#各種サービスにおける検索クエリの実行例)
    - [Azure Cosmos DBにおけるハイブリッド検索](#azure-cosmos-dbにおけるハイブリッド検索)
    - [Azure SQL Database におけるベクトル検索](#azure-sql-database-におけるベクトル検索)
   

## 概要
昨今の Azure のアップデートで、各種データストアサービスの検索クエリ機能が拡充されました。Azure Cosmos DB と Azure SQL Database のみを取り上げます。

本章では、 RAG システムの検索に利用できるアップデートを説明します。

## データストアサービスの新機能
2024 年に公開された Azure Cosmos DB および Azure SQL Database の新機能を紹介します。特に、コンテンツの意味を考慮したベクトル検索や、検索の際に高速で検索可能なアルゴリズムである DiskANN の採用といったアップデートがあります。

- [ベクトル検索](https://learn.microsoft.com/ja-jp/azure/cosmos-db/nosql/vector-search): データをベクトル形式で検索し、類似度に基づいて結果を返す機能です。高度な類似性検索を実現し、生成AIが関連性の高い情報を効率的に取得できます
- [ハイブリッド検索](https://learn.microsoft.com/ja-jp/azure/cosmos-db/gen-ai/hybrid-search): キーワード検索とベクトル検索を組み合わせた検索機能を提供し、より包括的な検索結果を提供します。
- [DiskANN](https://devblogs.microsoft.com/cosmosdb/build2024-cosmos-roundup/) : 高速な近似最近傍検索（ Approximate Nearest Neighbor Search ）を実現するためのアルゴリズムで、大規模なデータセットに対しても効率的な検索が可能です。
- [セマンティック検索](https://learn.microsoft.com/ja-jp/sql/relational-databases/search/semantic-search-sql-server?view=sql-server-ver16): Azure SQL Database のテーブルや列に対して意味を考慮したインデックスを作成することで、文脈や意味を理解した検索結果を取得できます。

各サービスにおける上記の機能の対応は以下の通りです。

| サービス名 | Azure Cosmos DB| Azure SQL Database | 
| --- | --- | --- |
| ベクトル検索 | 〇 | 〇 | 
| ハイブリッド検索 | 〇 | × | 
| DiskANN | 〇 | × | 
| セマンティック検索 | × | 〇 | 


## 各種サービスにおける検索クエリの実行例
前述の検索クエリ機能についてユースケースをイメージするために、代表的な例として Azure Cosmos DB および Azure SQL Database での検索クエリ機能の実行例を説明します。

### Azure Cosmos DBにおけるハイブリッド検索
ここでは、 Azure Cosmos DB のハイブリッド検索を用いて、カスタマーサポートでユーザーの質問に類似したサポートチケットを検索するクエリを示します。

まず、サポートチケットのデータスキーマを以下のように定義します。
```json
{
  "ticketId": "string", // チケットID
  "customerId": "string", // 顧客ID
  "issueDescription": "string", // 問題の詳細
  "issueCategory": "string", // 問題のカテゴリ
  "resolution": "string", // 解決策の詳細
  "status": "string", // チケットのステータス
  "createdDate": "string", // チケットが作成された日
  "resolvedDate": "string", // チケットが解決された日付
  "issueVector": "array of float" // 問題の内容をベクトル化したデータ
}
```

次に、作成したコンテナに対してデータを追加します。

```json
[
  {
    "ticketId": "TCKT001",
    "customerId": "CUST001",
    "issueDescription": "Cannot log in",
    "issueCategory": "Authentication",
    "resolution": "Reset password and guide to reconfigure",
    "status": "Resolved",
    "createdDate": "2024-12-20",
    "resolvedDate": "2024-12-21",
    "issueVector": [0.1, 0.3, 0.5, 0.7]
  },
  {
    "ticketId": "TCKT002",
    "customerId": "CUST002",
    "issueDescription": "Page not displaying",
    "issueCategory": "UI/UX",
    "resolution": "Clear cache and restart browser",
    "status": "Resolved",
    "createdDate": "2024-12-22",
    "resolvedDate": "2024-12-23",
    "issueVector": [0.2, 0.4, 0.6, 0.8]
  },
  {
    "ticketId": "TCKT003",
    "customerId": "CUST003",
    "issueDescription": "Network failure causing connectivity issues",
    "issueCategory": "Network",
    "resolution": "Restart router and check network settings",
    "status": "Resolved",
    "createdDate": "2024-12-23",
    "resolvedDate": "2024-12-24",
    "issueVector": [0.3, 0.5, 0.7, 0.9]
  },
  {
    "ticketId": "TCKT004",
    "customerId": "CUST004",
    "issueDescription": "Account locked",
    "issueCategory": "Security",
    "resolution": "Unlock account after identity verification",
    "status": "Resolved",
    "createdDate": "2024-12-24",
    "resolvedDate": "2024-12-25",
    "issueVector": [0.4, 0.6, 0.8, 1.0]
  },
  {
    "ticketId": "TCKT005",
    "customerId": "CUST005",
    "issueDescription": "Email not received",
    "issueCategory": "Communication",
    "resolution": "Check mail server settings and resend",
    "status": "Resolved",
    "createdDate": "2024-12-25",
    "resolvedDate": "2024-12-26",
    "issueVector": [0.5, 0.7, 0.9, 1.1]
  }
]
```

ここでネットワークに関する質問をユーザーがした場合、それに類似するサポートチケットを検索するには以下のクエリを実行します。
```sql
SELECT TOP 3 * 
FROM c 
ORDER BY RANK RRF(
    VectorDistance(c.issueVector, [0.5, 0.3, 0.2, 0.1]),　-- 第二引数はユーザーの issueDescription をベクター化した値
    FullTextScore(c.issueDescription, ["error", "network", "failure"])
)
```
このクエリによって、ユーザーの質問に対するベクトルの類似度に加えて、指定したキーワードのスコアが高いサポートチケットの上位3件を検索できます。

### Azure SQL Database におけるベクトル検索
ここでは Azure SQL Database のベクトル検索機能を用いて、特定のレビューと類似する製品レビューを検索するクエリを示します。

以下に、製品レビューのデータスキーマをSQLで定義し、ベクター検索を実行するためのクエリを示します。まず、製品レビューのテーブルを作成します。

```SQL
DROP TABLE REVIEWS

CREATE TABLE Reviews (
    reviewId NVARCHAR(50) PRIMARY KEY, -- レビューID
    productId NVARCHAR(50),            -- 製品ID
    reviewText NVARCHAR(MAX),          -- レビューの内容
    rating FLOAT,                      -- 評価
    reviewDate DATE,                   -- レビューの日付
    reviewVector NVARCHAR(MAX)         -- ベクトルデータ
);
```

次に、検索対象となるデータを追加します。

```sql
INSERT INTO Reviews (reviewId, productId, reviewText, rating, reviewDate, reviewVector) VALUES
('1', 'P1', 'Great product!', 4.5, '2024-01-01', '[0.1, 0.2, 0.3]'),
('2', 'P2', 'Not bad', 3.0, '2024-01-02', '[0.4, 0.5, 0.6]'),
('3', 'P3', 'Excellent!', 5.0, '2024-01-03', '[0.7, 0.8, 0.9]'),
('4', 'P4', 'Could be better', 2.5, '2024-01-04', '[0.2, 0.3, 0.4]'),
('5', 'P5', 'Loved it', 4.8, '2024-01-05', '[0.5, 0.6, 0.7]');
```

最後に、ベクトル検索を用いて類似するレビューを検索します。ここでは、データに対するレビューとして、ベクトルデータを用意し、追加したデータとのコサイン類似度を計算します。

```SQL
-- ベクトルデータの定義
DECLARE @dummyVector NVARCHAR(MAX) = '[0.5, 0.6, 0.7]';

-- コサイン類似度の計算と上位3件の取得
WITH VectorCTE AS (
    SELECT reviewId, productId, value AS vector_value
    FROM Reviews
    CROSS APPLY OPENJSON(reviewVector) WITH (value FLOAT '$')
),
DummyVectorCTE AS (
    SELECT value AS vector_value
    FROM OPENJSON(@dummyVector) WITH (value FLOAT '$')
),
SimilarityCTE AS (
    SELECT a.productId AS productId,
           SUM(a.vector_value * b.vector_value) / (SQRT(SUM(a.vector_value * a.vector_value)) * SQRT(SUM(b.vector_value * b.vector_value))) AS cosine_similarity
    FROM VectorCTE a
    CROSS JOIN DummyVectorCTE b
    GROUP BY a.productId
)
SELECT TOP 3 productId AS ProductID, cosine_similarity AS Cosine_Similarity
FROM SimilarityCTE
ORDER BY cosine_similarity DESC;
```

このクエリを実行することで、類似度の高い上位3件のデータが取得できます。

| ProductID | Cosine_Similarity |  
| --- | --- | 
| P3 | 0.9857465926353596 | 
| P5 | 0.981818181818182 | 
| P2 | 0.9779143167281406 | 
