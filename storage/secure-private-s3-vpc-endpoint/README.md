# Secure Private S3 Access Using a Gateway VPC Endpoint

**Implementation Note:**
During the initial implementation, I encountered an AWS Region configuration issue and made adjustments as the project evolved.
Some screenshots and terminal snippets therefore reflect earlier naming or configuration states. The final documented architecture uses the Frankfurt Region (`eu-central-1`), with the S3 bucket `my-secure-s3-226`.
These differences are retained to preserve the original implementation evidence; the final configuration and validation results are described in the sections below.

---

## Overview

This project demonstrates how to securely access Amazon S3 from an EC2 instance in a private subnet using an S3 Gateway VPC Endpoint, without requiring an Internet Gateway or NAT Gateway.

## Architecture

- **AWS Region:** Europe (Frankfurt) — `eu-central-1`
- **VPC:** `secure-s3-vpc`
- **VPC CIDR:** `10.10.0.0/16`
- **Application subnet:** `10.10.1.0/24`
- **S3 bucket:** `my-secure-s3-226`

## Security

- Private EC2 instance with no public IP
- S3 Gateway VPC Endpoint
- IAM permissions for S3 object access
- S3 Block Public Access
- Bucket versioning enabled

## Validation

Successfully uploaded `test.txt` from the EC2 instance to S3 and downloaded it back to verify private S3 connectivity.

## Key Learning

-Cost Optimization: S3 Gateway VPC Endpoints have no additional endpoint usage charges, making them a cost-effective solution for private S3 access.

-Private Connectivity: Access Amazon S3 from a private subnet without requiring an Internet Gateway or NAT Gateway for S3 traffic.

-Secure Access: Combine IAM least-privilege permissions, S3 Block Public Access, and bucket policies to control access.

-Route-Based Traffic Management: The VPC route table directs S3-bound traffic through the Gateway Endpoint using an AWS-managed prefix list.

-Important Consideration: Although the endpoint itself is free, standard charges for Amazon S3 storage, requests, EC2, and other applicable services still apply.

## Screenshots

Screenshots documenting the architecture, configuration, and connectivity tests will be added here.
