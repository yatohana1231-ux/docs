# 概要
- EC2 + Docker で管理していたAPIサーバーを、ECS + fargate のサーバー管理が不要な構成へと移管する

## 用語
- ECS（Elastic Container Service）
- ECR（Elastic Container Registry）

- ECS でのコンテナ運用は起動タイプが2種類存在する
    - Fargate
        - AWS に管理を任せる
        - 運用がラク（オペレーションエクセレンス）
    - EC2
        - 自前で制御する
        - コスト最適化ができる

# 環境
- terraform でリソース操作を実施する

# 操作ログ
- `main.tf` を作成し、ターミナルで `terraform init` `terraform plan` `terraform apply` を実行する