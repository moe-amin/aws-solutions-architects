# 06 - S3 Connectivity Testing

## 1. Objective
Verify that the private EC2 instance can upload and download objects from S3 using the configured IAM role and S3 Gateway VPC Endpoint.

## 2. Test Environment
- Region: `eu-central-1`
- EC2 instance: `app-server`
- Private IP observed: `10.10.1.206`
- S3 test bucket: `my-secure-s3-226`
- Gateway endpoint: `vpce-06d3e795c9608e69b`
- Test object: `test.txt`

## 3. Confirm the Instance Is Private
In the EC2 console, verify that the instance has no public IPv4 address.

The following command was used to inspect the interface:

```bash
ip addr
```

The observed private address was `10.10.1.206`.

## 4. Internet Reachability Check
The following test was attempted:

```bash
ping -c 4 8.8.8.8
```

The captured result showed 100% packet loss.

This is consistent with an instance that has no internet route, but it is not conclusive proof of the absence of all internet connectivity because ICMP may be blocked independently.

## 5. Create a Test File
Run on the EC2 instance:

```bash
echo "Gateway Endpoint Test" > test.txt
cat test.txt
```

Expected content:

```text
Gateway Endpoint Test
```

## 6. Upload the Object to S3
Run:

```bash
aws s3 cp test.txt s3://my-secure-s3-226/test.txt
```

The captured test completed successfully and reported that `test.txt` was uploaded.

## 7. Verify the Object in S3
Open the S3 console, select `my-secure-s3-226`, and confirm that `test.txt` appears in the object list.

## 8. Download and Verify
Remove the local test file and download it again:

```bash
rm -f test.txt
aws s3 cp s3://my-secure-s3-226/test.txt download.txt
cat download.txt
```

Expected output:

```text
Gateway Endpoint Test
```

The captured test successfully downloaded the object and displayed its content.

## 9. Confirm Endpoint Routing
In **VPC > Route tables**, inspect the route table associated with the EC2 application subnet.

Confirm that the AWS-managed S3 prefix-list route targets:

`vpce-06d3e795c9608e69b`

This route-table check is the primary configuration evidence that S3 traffic is directed through the gateway endpoint.

For an additional controlled test, temporarily remove the endpoint association from the application route table only if it is safe to do so. Repeat the S3 request, observe the result, and restore the association immediately. With no alternate route to S3, the request should fail. Do not perform this test if other workloads depend on that route table.

## 10. Results
| Test | Observed result |
|---|---|
| Private EC2 address inspected | `10.10.1.206` |
| Ping to `8.8.8.8` | 100% packet loss |
| Upload `test.txt` to S3 | Successful |
| Download object from S3 | Successful |
| Downloaded file content | `Gateway Endpoint Test` |
| Route-table endpoint target | Verify in the AWS console |

## 11. Conclusion
The captured tests demonstrate successful S3 object upload and download from a private EC2 instance without a public IPv4 address. Confirming the application route table's S3 prefix-list route to the gateway endpoint provides the corresponding routing evidence.

The ping result alone does not prove endpoint usage. Keep screenshots of the route table, endpoint status, successful upload, S3 object listing, and successful download as portfolio evidence.
