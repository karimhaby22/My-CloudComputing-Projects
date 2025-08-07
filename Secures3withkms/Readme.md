# SecureS3 with KMS 

This project provides step-by-step instructions for securely storing data in an Amazon S3 bucket using AWS Key Management Service (KMS) for encryption. All actions are performed via the *AWS Management Console*.

## Prerequisites

- An AWS account with necessary permissions to create:
  - S3 buckets
  - KMS customer managed keys
  - IAM policies 
- Internet access and browser to open AWS Console

## Objective

Create a secure S3 bucket that uses server-side encryption with a KMS key (SSE-KMS) for all uploaded objects, ensuring data-at-rest protection.

---

## Steps

### 1. Create a Customer Managed Key (CMK) in KMS

1. Go to the [AWS KMS Console](https://console.aws.amazon.com/kms/home).
2. Click *Create key*.
3. Choose:
   - *Symmetric* key type
   - *Encrypt and decrypt* key usage
4. Enter an alias (e.g., secureS3Key)
5. Optionally add a description and tags.
6. Set key administrative and usage permissions (allow necessary IAM users or roles).
7. Click *Finish*.


### 2. Create an S3 Bucket with Encryption

1. Go to the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Click *Create bucket*.
3. Set a *unique bucket name* (e.g., my-secure-bucket-kms).
4. Choose region.
5. Under *Default encryption*, select:
   - *Enable*
   - *AWS Key Management Service key (SSE-KMS)*
   - Choose your CMK (e.g., secureS3Key)
6. Click *Create bucket*.

### 3. Upload Objects to the Encrypted Bucket

1. Go to your bucket in the S3 Console.
2. Click *Upload*, then select files.
3. In the *Properties* section, verify:
   - *Encryption* is set to *AWS-KMS*
   - *AWS KMS key* is your customer-managed key
4. Click *Upload*.

---
