# 操作ログ
- 対象リソースを参照
```bash
it071@MSI:~/work$ aws ec2 describe-instances --region ap-northeast-1 --instance-ids i-0b9a0dec65b3bffc3 --profile terraform-role --no-cli-pager
```
```json
{
    "Reservations": [
        {
            "ReservationId": "r-058c9723337619ed4",
            "OwnerId": "071471126926",
            "Groups": [],
            "Instances": [
                {
                    "Architecture": "x86_64",
                    "BlockDeviceMappings": [
                        {
                            "DeviceName": "/dev/xvda",
                            "Ebs": {
                                "AttachTime": "2025-12-20T07:51:11+00:00",
                                "DeleteOnTermination": true,
                                "Status": "attached",
                                "VolumeId": "vol-09e37c21baab18647",
                                "EbsCardIndex": 0
                            }
                        }
                    ],
                    "ClientToken": "50186aff-5433-4402-a187-6f68d9f147de",
                    "EbsOptimized": true,
                    "EnaSupport": true,
                    "Hypervisor": "xen",
                    "NetworkInterfaces": [
                        {
                            "Association": {
                                "IpOwnerId": "071471126926",
                                "PublicDnsName": "ec2-18-178-135-252.ap-northeast-1.compute.amazonaws.com",
                                "PublicIp": "18.178.135.252"
                            },
                            "Attachment": {
                                "AttachTime": "2025-12-20T07:51:10+00:00",
                                "AttachmentId": "eni-attach-0bf0e15fb16914daf",
                                "DeleteOnTermination": true,
                                "DeviceIndex": 0,
                                "Status": "attached",
                                "NetworkCardIndex": 0
                            },
                            "Description": "",
                            "Groups": [
                                {
                                    "GroupId": "sg-0bb729ab2b49c2975",
                                    "GroupName": "dense-deck-sg"
                                }
                            ],
                            "Ipv6Addresses": [],
                            "MacAddress": "0a:35:d0:bf:f8:cf",
                            "NetworkInterfaceId": "eni-0efebc755256911e8",
                            "OwnerId": "071471126926",
                            "PrivateDnsName": "ip-172-31-9-154.ap-northeast-1.compute.internal",
                            "PrivateIpAddress": "172.31.9.154",
                            "PrivateIpAddresses": [
                                {
                                    "Association": {
                                        "IpOwnerId": "071471126926",
                                        "PublicDnsName": "ec2-18-178-135-252.ap-northeast-1.compute.amazonaws.com",
                                        "PublicIp": "18.178.135.252"
                                    },
                                    "Primary": true,
                                    "PrivateDnsName": "ip-172-31-9-154.ap-northeast-1.compute.internal",
                                    "PrivateIpAddress": "172.31.9.154"
                                }
                            ],
                            "SourceDestCheck": true,
                            "Status": "in-use",
                            "SubnetId": "subnet-0859e9bc8f1467ae9",
                            "VpcId": "vpc-0e8ce1ae531725324",
                            "InterfaceType": "interface",
                            "Operator": {
                                "Managed": false
                            }
                        }
                    ],
                    "RootDeviceName": "/dev/xvda",
                    "RootDeviceType": "ebs",
                    "SecurityGroups": [
                        {
                            "GroupId": "sg-0bb729ab2b49c2975",
                            "GroupName": "dense-deck-sg"
                        }
                    ],
                    "SourceDestCheck": true,
                    "Tags": [
                        {
                            "Key": "Name",
                            "Value": "dense-deck-api-dev"
                        }
                    ],
                    "VirtualizationType": "hvm",
                    "CpuOptions": {
                        "CoreCount": 1,
                        "ThreadsPerCore": 2
                    },
                    "CapacityReservationSpecification": {
                        "CapacityReservationPreference": "open"
                    },
                    "HibernationOptions": {
                        "Configured": false
                    },
                    "MetadataOptions": {
                        "State": "applied",
                        "HttpTokens": "required",
                        "HttpPutResponseHopLimit": 2,
                        "HttpEndpoint": "enabled",
                        "HttpProtocolIpv6": "disabled",
                        "InstanceMetadataTags": "disabled"
                    },
                    "EnclaveOptions": {
                        "Enabled": false
                    },
                    "BootMode": "uefi-preferred",
                    "PlatformDetails": "Linux/UNIX",
                    "UsageOperation": "RunInstances",
                    "UsageOperationUpdateTime": "2025-12-20T07:51:10+00:00",
                    "PrivateDnsNameOptions": {
                        "HostnameType": "ip-name",
                        "EnableResourceNameDnsARecord": true,
                        "EnableResourceNameDnsAAAARecord": false
                    },
                    "MaintenanceOptions": {
                        "AutoRecovery": "default",
                        "RebootMigration": "default"
                    },
                    "CurrentInstanceBootMode": "uefi",
                    "NetworkPerformanceOptions": {
                        "BandwidthWeighting": "default"
                    },
                    "Operator": {
                        "Managed": false
                    },
                    "InstanceId": "i-0b9a0dec65b3bffc3",
                    "ImageId": "ami-09cd9fdbf26acc6b4",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    },
                    "PrivateDnsName": "ip-172-31-9-154.ap-northeast-1.compute.internal",
                    "PublicDnsName": "ec2-18-178-135-252.ap-northeast-1.compute.amazonaws.com",
                    "StateTransitionReason": "",
                    "KeyName": "dense-deck-dev",
                    "AmiLaunchIndex": 0,
                    "ProductCodes": [],
                    "InstanceType": "t3.micro",
                    "LaunchTime": "2025-12-21T00:20:41+00:00",
                    "Placement": {
                        "AvailabilityZoneId": "apne1-az1",
                        "GroupName": "",
                        "Tenancy": "default",
                        "AvailabilityZone": "ap-northeast-1c"
                    },
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "SubnetId": "subnet-0859e9bc8f1467ae9",
                    "VpcId": "vpc-0e8ce1ae531725324",
                    "PrivateIpAddress": "172.31.9.154",
                    "PublicIpAddress": "18.178.135.252"
                }
            ]
        }
    ]
}
```