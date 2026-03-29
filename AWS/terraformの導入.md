# 概要
- terraform を初めて利用するにあたっての知識の整理

# terraform とは
- AWS の設計・実装をコードで行うための IaC ツール。
- cmd などの CLI から実行する

# 環境構築
- WSL（Windows Subsystem for Linux）

## vscode で編集する
- 作業フォルダへ移動する
```bash
cd ~/work/terraform-practice
```

- vscode を起動する
```bash
code .
```

# WSL設定
- 誘導に乗ったらうまくいかなかったため、WSLの削除と再インストールを実施
```bash
Microsoft Windows [Version 10.0.26200.8039]
(c) Microsoft Corporation. All rights reserved.

C:\Users\it071>wsl --unregister Ubuntu
登録解除。
この操作を正しく終了しました。


C:\Users\it071>wsl --install -d Ubuntu
ダウンロードしています: Ubuntu
インストールしています: Ubuntu
ディストリビューションが正常にインストールされました。'wsl.exe -d Ubuntu' を使用して起動できます
Ubuntu を起動しています...
Provisioning the new WSL instance Ubuntu
This might take a while...
Create a default Unix user account: it071
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

it071@MSI:/mnt/c/Users/it071$
```

- パッケージ更新
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y unzip curl git
```

- terraform インストール
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install terraform -y
```
- インストール確認
```bash
terraform version
```

- aws cli インストール
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
- インストール確認
```bash
aws --version
```

- aws 認証設定
- 作業用フォルダを作成
```bash
it071@MSI:/mnt/c/Users/it071$ cd ~
it071@MSI:~$ mkdir -p work
it071@MSI:~$ cd ~/work
it071@MSI:~/work$ pwd
/home/it071/work
```
- aws 認証設定
```bash
aws configure --profile terraform-user
```
- 以下を聞かれるため入力
```bash
AWS Access Key ID [None]: 
AWS Secret Access Key [None]:
Default region name [None]: ap-northeast-1
Default output format [None]: json
```
- 設定確認
```bash
aws sts get-caller-identity --profile terraform-user
```

- terraform が入っているか確認
```bash
terraform version
```

- 作業フォルダを作成
```bash
mkdir -p ~/work/terraform-practice
cd ~/work/terraform-practice
```

- テスト実行 
- 作業フォルダに `main.tf` を作成
```json
terraform {
  required_version = ">= 1.14.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region  = "ap-northeast-1"
  profile = "terraform-user"
}

resource "aws_s3_bucket" "test" {
  bucket = "tf-practice-071471126926-apne1"
}

output "bucket_name" {
  value = aws_s3_bucket.test.bucket
}
```
- ターミナルで以下を実行
```bash
terraform init
```
- 実行結果
    - 作業フォルダ内に各種ファイルが生成され、以下のログが出力される
```bash
it071@MSI:~/work/terraform-practice$ terraform init
Initializing the backend...
Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v6.38.0...
- Installed hashicorp/aws v6.38.0 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

- ターミナルで以下を実行
```bash
terraform plan
```
- 実行結果は以下
```bash
it071@MSI:~/work/terraform-practice$ terraform plan

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.test will be created
  + resource "aws_s3_bucket" "test" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "tf-practice-071471126926-apne1"
      + bucket_domain_name          = (known after apply)
      + bucket_namespace            = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_region               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = "ap-northeast-1"
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + bucket_name = "tf-practice-071471126926-apne1"

────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't
guarantee to take exactly these actions if you run "terraform apply" now.
```

- terraform を実行
```bash
terraform apply
```

- terraform でリソースを削除する
```bash
terraform destory
```


# 参考
- aws 公式ドキュメント
https://docs.aws.amazon.com/ja_jp/whitepapers/latest/cicd_for_5g_networks_on_aws/terraform.html