# 04 - S3 Bucket Policy Configuration

## 1. Objective
Configure the bucket policy to permit the intended S3 object operations through the designated S3 Gateway VPC Endpoint.

## 2. Resource Values
- Test bucket: `my-secure-s3-226`
- Gateway endpoint: `vpce-06d3e795c9608e69b`
- Region: `eu-central-1`

## 3. Example Endpoint-Conditioned Allow
Use the following as an example after confirming the actual bucket name and endpoint ID:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowObjectAccessThroughSpecifiedEndpoint",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-secure-s3-226/*",
      "Condition": {
        "StringEquals": {
          "aws:SourceVpce": "vpce-06d3e795c9608e69b"
        }
      }
    }
  ]
}
```

This statement grants the listed object actions only when the request satisfies the endpoint condition. It does not, by itself, explicitly deny every request that does not use the endpoint; other applicable policies must also be considered.

## 4. Apply the Policy
1. Open **Amazon S3 > Buckets**.
2. Select `my-secure-s3-226`.
3. Open **Permissions**.
4. Under **Bucket policy**, select **Edit**.
5. Confirm the bucket ARN and endpoint ID in the JSON.
6. Save the policy and resolve any validation errors.

## 5. Important Consistency Check
One earlier screenshot showed the resource ARN for `my-secure-s3-2026`, while the successful CLI test used `my-secure-s3-226`.

Do not leave a bucket policy pointing at a different bucket. The bucket policy resource ARN must match the bucket being tested.

## 6. Security Notes
- Keep S3 Block Public Access enabled.
- Keep the EC2 role's permissions least-privilege.
- If the requirement is to enforce that all applicable requests must use this endpoint, design and test an explicit-deny policy carefully. Consider any AWS services or administrative workflows that also need access before applying it.
- Do not assume a successful upload alone proves the bucket policy is enforcing every intended restriction.

## 7. Validation
Test object upload and download from the private EC2 instance. Confirm the bucket name, endpoint ID, IAM role, and bucket policy before recording the results.
