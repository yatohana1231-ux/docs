# 概要
- VPC Origin を利用した構成がうまくいかなった

## 上手くいかなかった構成
```
Client
  ↓
CloudFront
  ├ /ws → VPC Origin (ALB) → EC2（@Private Subnet）
  ├ /api → VPC Origin (ALB) → EC2（@Private Subnet）
  └ / → S3
```

## 原因
- VPC Origin が WebSocket をサポートしていなかった
- 参照
![VPC Origin のサポート対象外機能](../assets/aws/ws/VPC%20Origin%20NOT%20Sipprot%20functions.png)
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html

- 他にも、gRPC trafficとLambda@Edge の origin request / origin response triggersもサポートされていない。
- gRPC通信とはGoogleが開発した**高速なAPI通信方式（RPCフレームワーク）**です。
| 項目      | gRPC traffic   | REST traffic |
| ------- | -------------- | ------------ |
| 通信方式    | HTTP/2         | HTTP/1.1（主流） |
| データ形式   | バイナリ（Protobuf） | JSON         |
| 速度      | 高速             | やや遅い         |
| ストリーミング | あり（強力）         | 基本なし         |
| 可読性     | 人間には読みにくい      | 読みやすい        |

## 改善案
- 方針は、api / wsサーバーはVPC Originを利用しない構成にする
- APIサーバーは 通常の HTTP API のため、 VPC Origin のサポート対象である
- だが、Dense Deck Poker の構成として、APIサーバーとwsサーバーが同じEC2上に存在するため、CloudFront → EC2 の接続方法は統一する方が運用が簡単

### 構成案
- ALB を internet-facing にするのは許容
- EC2は Private Subnet に配置する
- 本番環境構築時に冗長化が可能な構成とする
    - ALB は複数AZの Public Subnet に配置
    - EC2 は複数AZの Private Subnet に配置
```
Client
  ↓
CloudFront
  ├ /ws → ALB@Public Subnet（internet-facing） → EC2@Private Subnet
  ├ /api → ALB@Public Subnet（internet-facing） → EC2@Private Subnet
  └ / → S3（静的ホスティング）
```