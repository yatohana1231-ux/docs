# 概要
- EC2　から Fargate へ移管するにあたり、 ALB の接続先を変更する

# 操作ログ
1. ALB の情報を取得
```bash
aws elbv2 describe-load-balancers \
  --region ap-northeast-1 \
  --profile terraform-role \
  --no-cli-pager
```
```json
{
    "LoadBalancers": [
        {
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:ap-northeast-1:071471126926:loadbalancer/app/alb-densedeckpoker-dev/001d9d0dca125a48",
            "DNSName": "alb-densedeckpoker-dev-2053493010.ap-northeast-1.elb.amazonaws.com",
            "CanonicalHostedZoneId": "Z14GRHDCWA56QT",
            "CreatedTime": "2026-03-20T05:24:34.797000+00:00",
            "LoadBalancerName": "alb-densedeckpoker-dev",
            "Scheme": "internet-facing",
            "VpcId": "vpc-0e8ce1ae531725324",
            "State": {
                "Code": "active"
            },
            "Type": "application",
            "AvailabilityZones": [
                {
                    "ZoneName": "ap-northeast-1a",
                    "SubnetId": "subnet-0390ed9e54f68b181",
                    "LoadBalancerAddresses": []
                },
                {
                    "ZoneName": "ap-northeast-1c",
                    "SubnetId": "subnet-0859e9bc8f1467ae9",
                    "LoadBalancerAddresses": []
                },
                {
                    "ZoneName": "ap-northeast-1d",
                    "SubnetId": "subnet-0a056d37a7adfced4",
                    "LoadBalancerAddresses": []
                }
            ],
            "SecurityGroups": [
                "sg-07c57482663f844f3"
            ],
            "IpAddressType": "ipv4",
            "EnablePrefixForIpv6SourceNat": "off"
        }
    ]
}
```

```bash
aws elbv2 describe-listeners \
  --load-balancer-arn arn:aws:elasticloadbalancing:ap-northeast-1:071471126926:loadbalancer/app/alb-densedeckpoker-dev/001d9d0dca125a48 \
  --region ap-northeast-1 \
  --profile terraform-role \
  --no-cli-pager
```
```json
{
    "Listeners": [
        {
            "ListenerArn": "arn:aws:elasticloadbalancing:ap-northeast-1:071471126926:listener/app/alb-densedeckpoker-dev/001d9d0dca125a48/2b2f8df4ff1b1573",
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:ap-northeast-1:071471126926:loadbalancer/app/alb-densedeckpoker-dev/001d9d0dca125a48",
            "Port": 443,
            "Protocol": "HTTPS",
            "Certificates": [
                {
                    "CertificateArn": "arn:aws:acm:ap-northeast-1:071471126926:certificate/72379780-b198-4839-b25e-9199682e79a3"
                }
            ],
            "SslPolicy": "ELBSecurityPolicy-TLS13-1-2-Res-PQ-2025-09",
            "DefaultActions": [
                {
                    "Type": "fixed-response",
                    "Order": 1,
                    "FixedResponseConfig": {
                        "StatusCode": "403",
                        "ContentType": "text/plain"
                    }
                }
            ],
            "MutualAuthentication": {
                "Mode": "off"
            }
        }
    ]
}
```

- やりたいことの全体像は以下
```text
Route53（既存）
↓
ALB（既存）
↓
【新規】Listener Rule
↓
【新規】Target Group（ECS用）
↓
ECS Service
```

- TODO
1. ECS用の target group を作成する
2. Listener Rule を追加する
3. ECS Service に紐づける

- tf ファイルを書き換えて、plan → apply を実行
- plan ログ
```bash
Plan: 3 to add, 1 to change, 1 to destroy.
```