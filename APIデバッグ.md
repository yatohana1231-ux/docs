# 概要
- 開発中の Dense Deck Poker にて、フロントから呼び出しているAPIが500エラーとなった
- ヘルスチェックは問題なく通っている

# 調査
現在エラーになっているAPI
- `/api/auth/me`
- `/api/auth/guest`
- `/api/auth/login`
- `POST /api/rooms`

## 作業ログ
```bash
aws ecs list-clusters --profile cdk-dev
```
```JSON
{
    "clusterArns": [
        "arn:aws:ecs:ap-northeast-1:071471126926:cluster/densedeck-cluster"
    ]
}
```
```bash
aws ecs list-services --cluster densedeck-cluster --profile cdk-dev
```
```JSON
{
    "serviceArns": [
        "arn:aws:ecs:ap-northeast-1:071471126926:service/densedeck-cluster/densedeck-api-service"
    ]
}
```
```bash
aws ecs list-tasks --cluster densedeck-cluster --service densedeck-api-service --profile cdk-dev
```
```JSON
{
    "taskArns": [
        "arn:aws:ecs:ap-northeast-1:071471126926:task/densedeck-cluster/b7f1b5bb564846aab86439aee0302234"
    ]
}
```
```bash
aws ecs describe-tasks \
  --cluster densedeck-cluster \
  --tasks arn:aws:ecs:ap-northeast-1:071471126926:task/densedeck-cluster/b7f1b5bb564846aab86439aee0302234 \
  --profile cdk-dev \
  --query 'tasks[0].taskDefinitionArn' \
  --output text
```
```text
arn:aws:ecs:ap-northeast-1:071471126926:task-definition/densedeck-api:1
```
```bash
aws ecs describe-task-definition \
  --task-definition densedeck-api:1 \
  --profile cdk-dev \
  --no-cli-pager \
  --query 'taskDefinition.containerDefinitions[].{name:name,logConfiguration:logConfiguration,environment:environment}'
```
```JSON
[
    {
        "name": "densedeck-api",
        "logConfiguration": {
            "logDriver": "awslogs",
            "options": {
                "awslogs-group": "/ecs/densedeck-api",
                "awslogs-region": "ap-northeast-1",
                "awslogs-stream-prefix": "ecs"
            }
        },
        "environment": [
            {
                "name": "PORT",
                "value": "3000"
            },
            {
                "name": "HOST",
                "value": "0.0.0.0"
            },
            {
                "name": "NODE_ENV",
                "value": "production"
            }
        ]
    }
]
```
```bash
aws logs tail /ecs/densedeck-api \
  --since 15m \
  --follow \
  --profile cdk-dev \
  --format short
```
- 上記を実行したうえで、エラーになる操作を実施
```JSON
 {"level":50,"time":1775777695593,"pid":19,"hostname":"ip-172-31-25-40.ap-northeast-1.compute.internal","reqId":"req-d78","req":{"method":"POST","url":"/api/rooms","hostname":"d2biqgqideb6ae.cloudfront.net","remoteAddress":"172.31.34.203","remotePort":54464},"res":{"statusCode":500},"err":{"type":"PrismaClientInitializationError","message":"\nInvalid `prisma.sessions.findUnique()` invocation:\n\n\nerror: Environment variable not found: DATABASE_URL.\n  -->  schema.prisma:7\n   | \n 6 |   provider = \"postgresql\"\n 7 |   url      = env(\"DATABASE_URL\")\n   | \n\nValidation Error Count: 1","stack":"PrismaClientInitializationError: \nInvalid `prisma.sessions.findUnique()` invocation:\n\n\nerror: Environment variable not found: DATABASE_URL.\n  -->  schema.prisma:7\n   | \n 6 |   provider = \"postgresql\"\n 7 |   url      = env(\"DATABASE_URL\")\n   | \n\nValidation Error Count: 1\n    at ei.handleRequestError (/app/node_modules/@prisma/client/runtime/library.js:121:7568)\n    at ei.handleAndLogRequestError (/app/node_modules/@prisma/client/runtime/library.js:121:6593)\n    at ei.request (/app/node_modules/@prisma/client/runtime/library.js:121:6300)\n    at async a (/app/node_modules/@prisma/client/runtime/library.js:130:9551)\n    at async loadUserFromSession (file:///app/dist/utils/auth.js:45:21)\n    at async requireAuth (file:///app/dist/routes/rooms.js:8:18)\n    at async Object.<anonymous> (file:///app/dist/routes/rooms.js:26:22)","clientVersion":"6.19.1","name":"PrismaClientInitializationError"},"msg":"\nInvalid `prisma.sessions.findUnique()` invocation:\n\n\nerror: Environment variable not found: DATABASE_URL.\n  -->  schema.prisma:7\n   | \n 6 |   provider = \"postgresql\"\n 7 |   url      = env(\"DATABASE_URL\")\n   | \n\nValidation Error Count: 1"}
```
- `DATABASE_URL` が見つからない、というエラーログ
- エラーの原因は、ECSのタスク定義に `DATABASE_URL` などの必要項目がセットできていなかったこと
- 対策として、 ECS の設定を見直して再度デプロイする。