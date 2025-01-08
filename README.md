# Azure による RAG システム構築におけるデータストアの選定
本ドキュメントは Azure 上で生成 AI による RAG ( Retrieve Augmented Generation )システムを構築する際に使用するデータストアの選定についてまとめています。  
ここで取り上げた製品・サービスの詳細および最新情報は[製品ドキュメント](https://docs.microsoft.com)をご参照ください。

## [はじめに](./chapter00.md)
RAG システムを構築する上でのデータストアの選択の重要性と、本ドキュメントの対象読者を記載しています。

## [1章 RAG システムの基礎](./chapter01.md)
RAG システムの基本構成と、 RAG システムでデータの検索に使用される Azure のサービスを説明します。  

## [2章 データストアの検索クエリ機能のアップデート](./chapter02.md)
元々 RAG システムのデータの検索には検索サービスが使用されていましたが、昨今のデータストアの検索機能の追加により、選定候補に挙がるようになりました。そのため、 Azure のデータストアサービスの検索機能のアップデートを説明します。

## [3章 RAG システムにおける各種データストアの使い分け](./chapter03.md)
データストアの検索クエリ機能の拡充によって、 RAG のデータストアとして検索サービスを介さないアーキテクチャが選択肢として入るようになりました。
検索サービスおよびデータストアとして紹介した各種リソースの特徴をを比較した上で、どのような要件・ユースケースで使用されるかまとめます。

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft 
trademarks or logos is subject to and must follow 
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.