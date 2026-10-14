# 01 - S3 Bucket Setup

## 1. Objective
Create a private Amazon S3 general purpose bucket for testing secure object storage access from a private EC2 instance.

## 2. AWS Configuration
- AWS Region: Europe (Frankfurt), `eu-central-1`
- Bucket used in the connectivity test: `my-secure-s3-226`
- Bucket type: General purpose
- Object Ownership: ACLs disabled (Bucket owner enforced)
- Block Public Access: Enabled
- Bucket Versioning: Enabled

> Important: Earlier configuration screenshots show `my-secure-s3-2026`, while the successful upload/download test uses `my-secure-s3-226`. Confirm which bucket is intended and keep the IAM policy and bucket policy consistent with it.

## 3. Create the Bucket
1. Open the Amazon S3 console.
2. Select **Create bucket**.
3. Choose the Frankfurt Region (`eu-central-1`).
4. Select **General purpose**.
5. Enter the intended globally unique bucket name.
6. Keep ACLs disabled.
7. Keep all Block Public Access settings enabled.
8. Enable bucket versioning.
9. Create the bucket.

## 4. Security Considerations
- Do not make the bucket or its objects public.
- Use IAM permissions to grant the EC2 workload only the required S3 actions.
- Use a bucket policy to control access through the intended VPC endpoint.
- Use versioning to help recover previous object versions after accidental changes or deletion.

## 5. Validation
Confirm that:
- The bucket exists in `eu-central-1`.
- Block Public Access is enabled.
- Versioning is enabled.
- The bucket contains no unintended public access grants.

## 6. Evidence
See the screenshots directory for the bucket configuration and test results.
