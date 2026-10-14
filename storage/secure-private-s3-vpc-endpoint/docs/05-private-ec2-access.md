# 05 - Private EC2 Access

## 1. Objective
Manage the EC2 instance privately through EC2 Instance Connect Endpoint without assigning the instance a public IPv4 address.

## 2. Observed Instance Details
- Instance name: `app-server`
- Private IPv4 address: `10.10.1.206`
- Instance type: `t3.micro`
- Availability Zone: `eu-central-1a`
- VPC: `secure-s3-vpc`
- IAM role: `EC2-S3-Access`
- Public IPv4 address: none

## 3. Access Method
EC2 Instance Connect Endpoint provides private connectivity for supported SSH connections to an instance without requiring a public IP address on that instance.

## 4. Connect Using the AWS Console
1. Open **EC2 > Instances**.
2. Select `app-server`.
3. Select **Connect**.
4. Choose the EC2 Instance Connect option and select the configured EC2 Instance Connect Endpoint when prompted.
5. Use the appropriate operating-system username and connect.

The endpoint itself must be available, and its security group, the instance security group, subnet routing, and network ACLs must permit the connection.

## 5. Network Security
Review the following:
- The instance has no public IPv4 address.
- The instance security group permits SSH only from the intended management path.
- The EC2 Instance Connect Endpoint security group allows the required connection to the instance.
- Network ACLs and routes allow the connection.
- The instance has the `EC2-S3-Access` IAM role attached.

## 6. Validation
After connecting, run:

```bash
ip addr
```

Confirm the private address is `10.10.1.206` (or the current address shown in the EC2 console).

Then verify the AWS CLI identity:

```bash
aws sts get-caller-identity
```

## 7. Expected Design
The instance remains private and uses the S3 Gateway VPC Endpoint for S3 access. EC2 Instance Connect Endpoint is for management access; it is not the route used for S3 traffic.
