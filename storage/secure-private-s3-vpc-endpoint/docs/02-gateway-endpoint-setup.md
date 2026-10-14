# 02 - S3 Gateway VPC Endpoint Setup

## 1. Objective
Allow a private EC2 instance to access Amazon S3 without requiring an Internet Gateway or NAT Gateway.

## 2. Architecture
- VPC: `secure-s3-vpc`
- VPC CIDR: `10.10.0.0/16`
- Application subnet: `10.10.1.0/24`
- S3 Gateway VPC Endpoint: `vpce-06d3e795c9608e69b`
- Region: `eu-central-1`

The EC2 instance uses the application subnet's route table to reach S3 through the gateway endpoint.

## 3. Create the Endpoint
1. Open **VPC > Endpoints**.
2. Select **Create endpoint**.
3. Enter a descriptive name, such as `s3-gateway-endpoint`.
4. Select the AWS services category.
5. Choose the S3 service for `eu-central-1`:
   `com.amazonaws.eu-central-1.s3`
6. Select the gateway endpoint type.
7. Select `secure-s3-vpc`.
8. Associate the route table used by the application subnet.
9. Keep the endpoint policy at full access for initial setup only if needed; prefer a restricted policy for production.
10. Create the endpoint and confirm its status is **Available**.

## 4. Verify the Route Table
Open **VPC > Route tables** and select the route table associated with the application subnet.

Verify that it contains:
- Destination `10.10.0.0/16` -> `local`
- The AWS-managed S3 prefix list for the Region -> `vpce-06d3e795c9608e69b`

Use the actual prefix-list ID displayed in your route table; do not guess it.

The S3 gateway endpoint does not have its own subnet or endpoint network interface. AWS installs the S3 prefix-list route in the associated route table.

## 5. Traffic Flow
1. Connect privately to the EC2 instance using EC2 Instance Connect Endpoint.
2. The EC2 instance makes an S3 API request.
3. The application subnet route table matches the destination to the S3 prefix list.
4. AWS routes the request through the S3 Gateway VPC Endpoint.
5. S3 evaluates IAM permissions, bucket policy, and other applicable controls.

## 6. Validation
- Endpoint status is **Available**.
- The application route table is associated with the endpoint.
- The S3 prefix-list route targets the gateway endpoint.
- An authorized object upload and download succeeds from EC2.

## 7. Important Notes
- A gateway endpoint is not an ENI and has no private endpoint IP to traceroute to.
- A failed ping to `8.8.8.8` is consistent with a private instance without internet routing, but ping alone does not prove S3 endpoint use.
- VPC Flow Logs can provide supporting network evidence, but route-table inspection and controlled connectivity tests are stronger proof of endpoint routing.
