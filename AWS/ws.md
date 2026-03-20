# 構成

## 上手くいかなかった構成
```
Client
  ↓
CloudFront
  ├ / → S3
  ├ /api → VPC Origin (ALB) → EC2（@Private Subnet）
  └ /ws → VPC Origin (ALB) → EC2（@Private Subnet）
```

## 原因
- VPC Origin が WebSocket をサポートしていなかった
- 参照
"[VPC Origin のサポート対象外機能](../assets/aws/ws/VPC%20Origin%20NOT%20Sipprot%20functions.png)
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html
