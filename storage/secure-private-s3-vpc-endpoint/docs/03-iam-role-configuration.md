# 03 - EC2 IAM Role Configuration

## 1. Objective
Grant the EC2 application instance permission to read and upload objects in the intended S3 bucket without storing long-term AWS access keys on the instance.

## 2. Configuration
- IAM role: `EC2-S3-Access`
- IAM policy shown in the console: `EC2-S3-AccessPolicy`
- Trusted service: Amazon EC2
- Intended bucket used in the successful test: `my-secure-s3-226`

## 3. Create the Role
1. Open **IAM > Roles**.
2. Select **Create role**.
3. Choose **AWS service** as the trusted entity.
4. Choose **EC2** as the use case.
5. Create a role named `EC2-S3-Access`.
6. Add a least-privilege policy for the required S3 object actions.

## 4. Example Object-Access Policy
For the test bucket `my-secure-s3-226`, the policy should be scoped to its objects:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadWriteSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-secure-s3-226/*"
    }
  ]
}
```

This policy grants object upload and download permissions. It does not grant permission to list the bucket. Add `s3:ListBucket` on the bucket ARN only if listing objects is required.

## 5. Attach the Role to EC2
1. Open **EC2 > Instances**.
2. Select the application instance.
3. Choose **Actions > Security > Modify IAM role**.
4. Select `EC2-S3-Access`.
5. Update the role and verify it appears in the instance details.

## 6. Security Considerations
- Do not attach `AdministratorAccess` or `AmazonS3FullAccess` for this test.
- Do not store access keys in scripts or on the instance.
- Keep permissions scoped to the intended bucket and required actions.
- Ensure the bucket policy and IAM policy refer to the same bucket name.

## 7. Validation
From the EC2 session, run:

```bash
aws sts get-caller-identity
```

This should identify the temporary role credentials being used. Then test an authorized object upload and download as documented in `06-connectivity-testing.md`.
