# AWS構成
Client
  ↓
CloudFront
  ├ / → S3
  └ /api → VPC Origin (ALB) → EC2（@Private Subnet）

# 概要
CloudFront → EC2（@Public Subnet）
という構成から、EC2をPrivate Subnetへ変更したい
そのため、
CloudFront → ALB → EC2（@Private Subnet）
という構成へ変更する

# その他の構成案
CloudFront → API Gateway → VPC Link → NLB → EC2(private)
今回は、CloudFrontから接続するAPIは一般へ公開しない想定のため、ALBを利用した構成を採用する。
Webアプリのバックエンド構成であればALBが適切と思われる。
※APIをプロダクトとして公開するのであればAPI Gateway が向いていそう。

# CloudFront → ALB
1. ALBを作成する
- AWSコンソールからEC2へアクセスし、サイドナビゲーションからロードバランサーを選択
- 「ロードバランサーの作成」から新規作成
- タイプは、今回はALB（Application Load Balancer）を選択
- スキームは Internal
    - 他の選択肢は Internet-facedで、こちらはインターネットからアクセスできる
2. VPCオリジンを作成する
- CloudFront のサイドナビゲーションから「VPCオリジン」を選択する
- NAME、オリジンARN、プロトコルを設定
    - NAMEは何でもOK
    - オリジンARNに接続先のロードバランサーARNを指定
        ARN：Amazan Resource Name
- CloudFrontのディストリビューションのオリジンに、作成したVPCオリジンを指定する
    ※20260307：CloudFrontの無料プランではVPCオリジンが利用できないため、従量課金かビジネスモデル（$200/月）にする必要がある
- 作成したVPCオリジンに向けたビヘイビアを作成する

# 残りアクション
- ALBのInboundルールをCloudFrontに限定する
- NAT Gateway を新しいPublic Subnetに設置し、EC2と接続する
- EC2が紐づいているSubnetのルートテーブルから「0.0.0.0/0」を削除する

# まとめ
CloudFrontのVPCオリジンを利用することで、Private Subnet上に存在するリソース（EC2, ALB, NLBなど）からコンテンツ配信を実施できる
Private Origin Architecture というらしい
ALBなどのオリジンサーバーがPrivate Subnetに配置され外部に公開されないため、セキュリティ面が強い
CloudFrontやALBにWAFを導入する、などでさらにセキュリティを強固にできる

# 参考
- AWSドキュメント
https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/introduction.html
https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html

- 分かりやすかった
https://qiita.com/j2-yano/items/3aba0f546820927b70c7