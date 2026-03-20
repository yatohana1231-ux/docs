# 概要
- CloudFrton → ALB の通信をHTTPSで実行したい
- ACM で証明書を取得するためには、CloudFrontは証明書のCN/SANと一致するドメインで接続する必要がある
- 以下のような構成になる
```
CloudFront
   ↓（HTTPS）
origin.example.com
   ↓（Route53）
ALB（Aレコード Alias）
   ↓
ターゲット
```