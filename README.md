# AWS KMS APK Signer and Verifier

This repository contains scripts to sign and verify an APK using `apksigner` and AWS KMS.

## Table of Contents

- [AWS KMS APK Signer and Verifier](#aws-kms-apk-signer-and-verifier)
  - [Table of Contents](#table-of-contents)
  - [Prerequisites](#prerequisites)
  - [IAM Permissions](#iam-permissions)
    - [sign\_apk IAM Permissions](#sign_apk-iam-permissions)
    - [verify\_apk IAM Permissions](#verify_apk-iam-permissions)
  - [Installation Instructions](#installation-instructions)
    - [macOS](#macos)
    - [Ubuntu/Debian](#ubuntudebian)
  - [sign\_apk](#sign_apk)
    - [Usage](#usage)
    - [Options](#options)
    - [Examples](#examples)
    - [Environment Variables](#environment-variables)
  - [verify\_apk](#verify_apk)
    - [Usage](#usage-1)
    - [Options](#options-1)
    - [Examples](#examples-1)
    - [Environment Variables](#environment-variables-1)
  - [License](#license)

## Prerequisites

Ensure the following tools are installed on your system:

- AWS CLI
- jq
- xxd (only for `sign_apk`)
- apksigner
- keytool (part of the JDK)
- openssl

## IAM Permissions

### sign_apk IAM Permissions

The IAM user or role that you use to run this script needs the following permissions:

- `kms:GetPublicKey`
- `kms:Sign`

Example IAM policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "kms:GetPublicKey",
        "kms:Sign"
      ],
      "Resource": "arn:aws:kms:YOUR_REGION:YOUR_ACCOUNT_ID:key/YOUR_KEY_ID"
    }
  ]
}
```

### verify_apk IAM Permissions

The IAM user or role that you use to run this script needs the following permissions:

- `kms:GetPublicKey`

Example IAM policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "kms:GetPublicKey"
      ],
      "Resource": "arn:aws:kms:YOUR_REGION:YOUR_ACCOUNT_ID:key/YOUR_KEY_ID"
    }
  ]
}
```

## Installation Instructions

### macOS

Install the necessary tools using Homebrew:

```bash
brew install awscli jq xxd openjdk openssl
```

To install `apksigner`, install the Android SDK Build-Tools.

### Ubuntu/Debian

Install the necessary tools using apt:

```bash
sudo apt update
sudo apt install awscli jq xxd openjdk-11-jdk openssl
```

To install `apksigner`, install the Android SDK Build-Tools.

## sign_apk

### Usage

Run the script with the following options:

```bash
./sign_apk -k <aws-kms-key-id> -i <input-apk> -o <output-signed-apk> [-v1] [-v2] [-v3]
```

### Options

- `-k, --kms-key-id`: AWS KMS Key ID (can be Key ID, Key ARN, Alias name, or Alias ARN)
- `-i, --input-apk`: Path to the APK to be signed
- `-o, --output-apk`: Output path for the signed APK
- `-v1, --v1-signing`: Enable APK Signature Scheme v1
- `-v2, --v2-signing`: Enable APK Signature Scheme v2
- `-v3, --v3-signing`: Enable APK Signature Scheme v3 (default)
- `-h, --help`: Display the help message

### Examples

```bash
./sign_apk -k "1234abcd-12ab-34cd-56ef-1234567890ab" -i "path/to/your-app.apk" -o "signed-app.apk" -v1 -v2 -v3
./sign_apk -k "arn:aws:kms:us-west-2:123456789012:key/abcd1234-abcd-1234-abcd-1234567890ab" -i "path/to/your-app.apk" -o "signed-app.apk" -v1 -v2
./sign_apk -k "alias/my-key-alias" -i "path/to/your-app.apk" -o "signed-app.apk" -v1 -v3
./sign_apk -k "arn:aws:kms:us-west-2:123456789012:alias/my-key-alias" -i "path/to/your-app.apk" -o "signed-app.apk" -v2
```

### Environment Variables

To specify paths for the tools, you can set the following environment variables:

```bash
export APKSIGNER_PATH="/path/to/apksigner"
export KEYTOOL_PATH="/path/to/keytool"
export OPENSSL_PATH="/path/to/openssl"
```

## verify_apk

### Usage

Run the script with the following options:

```bash
./verify_apk -k <aws-kms-key-id> -i <input-apk>
```

### Options

- `-k, --kms-key-id`: AWS KMS Key ID (can be Key ID, Key ARN, Alias name, or Alias ARN)
- `-i, --input-apk`: Path to the APK to be verified
- `-h, --help`: Display the help message

### Examples

```bash
./verify_apk -k "1234abcd-12ab-34cd-56ef-1234567890ab" -i "path/to/your-app.apk"
./verify_apk -k "arn:aws:kms:us-west-2:123456789012:key/abcd1234-abcd-1234-abcd-1234567890ab" -i "path/to/your-app.apk"
./verify_apk -k "alias/my-key-alias" -i "path/to/your-app.apk"
./verify_apk -k "arn:aws:kms:us-west-2:123456789012:alias/my-key-alias" -i "path/to/your-app.apk"
```

### Environment Variables

To specify paths for the tools, you can set the following environment variables:

```bash
export APKSIGNER_PATH="/path/to/apksigner"
export KEYTOOL_PATH="/path/to/keytool"
export OPENSSL_PATH="/path/to/openssl"
```

## License

This repository is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
