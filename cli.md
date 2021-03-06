---

copyright:
  years: 2017, 2018, 2019
lastupdated: "2018-08-22"

subcollection: cloud-object-storage-infrastructure

---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Using a CLI
{: #cli-iaas}

## AWS CLI
{: #cli-iaas-aws}

The official command-line interface for AWS is compatible with the {{site.data.keyword.cos_full_notm}} S3 API. Written in Python, it can be installed from the Python Package Index through `pip install awscli`. By default, access keys are sourced from `~/.aws/credentials`, but can also be set as environment variables.

These examples were generated by using version 1.10.66 of the CLI. To check the version installed, run `aws --version`.

Minimum information that is required in the `~/.aws/credentials` file is as follows.

```
[default]
aws_access_key_id = {Access Key ID}
aws_secret_access_key = {Secret Access Key}
```

You can obtain your Access Key ID and Secret Access Key from the [IBM Cloud Console ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com) through **Classic Infrastructure** > **Storage** > **Object Storage**. Click the account name. In the account details page, click **Access & Permissions** to see your credentials and access keys.
{:tip}

The {{site.data.keyword.cos_full_notm}} endpoint must be sourced by using the `--endpoint-url` option, and can't be set in the credentials file.

Simple use cases can be accomplished by using `aws --endpoint-url={endpoint} s3 <command>`. Objects are managed with familiar shell commands, such as `ls`, `mv`, `cp`, and `rm`.  Buckets can be deleted by using `rb`.

 Buckets can't be created by using this method. Instead, use the `s3api` method.
 {:tip}

### Basic operations
{: #cli-iaas-aws-basic}

**Listing buckets**

```shell
$ aws --endpoint-url=https://{endpoint} s3 ls
```

```shell
2016-09-09 12:48  s3://bucket-1
2016-09-16 21:29  s3://bucket-2
```

**Listing objects within a bucket**

```bash
$ aws --endpoint-url=https://{endpoint} s3 ls s3://bucket-1/
```

```terminal
2016-09-28 15:36       837   s3://bucket-1/c1ca2-filename-00001
2016-09-09 12:49       533   s3://bucket-1/c9872-filename-00002
2016-09-28 15:36     14476   s3://bucket-1/98837-filename-00003
2016-09-29 16:24     20950   s3://bucket-1/abfc4-filename-00004
```

**Uploading a new object to a bucket**

```bash
$ aws --endpoint-url=https://{endpoint} s3 cp /tmp/new-file s3://bucket-1/
upload: /tmp/new-file to s3://bucket-1/new-file
```

**Copying an object from one bucket to another**

```bash
$ aws --endpoint-url=https://{endpoint} s3 cp s3://bucket-1/new-file s3://bucket-2/
copy: s3://bucket-1/new-file to s3://bucket-2/new-file
```

More complex operations require leveraging the api directly with `aws --endpoint-url={endpoint} s3api <command>`, which provides a standard JSON response.

Listing buckets:

```bash
$ aws --endpoint-url=https://{endpoint} s3api list-buckets
{
    "Owner": {
        "DisplayName": "{storage-account-uuid}",
        "ID": "{storage-account-uuid}"
    },
    "Buckets": [
        {
            "CreationDate": "2016-09-09T12:48:52.442Z",
            "Name": "bucket-1"
        },
        {
            "CreationDate": "2016-09-16T21:29:00.912Z",
            "Name": "bucket-2"
        }
    ]
}
```

Creating a bucket

Valid provisioning codes for `LocationConstraint` are as follows. <br>
&emsp;&emsp;  `us-standard` / `us-vault` / `us-cold` / `us-flex` <br>
&emsp;&emsp;  `us-east-standard` / `us-east-vault`  / `us-east-cold` / `us-east-flex` <br>
&emsp;&emsp;  `us-south-standard` / `us-south-vault`  / `us-south-cold` / `us-south-flex` <br>
&emsp;&emsp;  `eu-standard` / `eu-vault` / `eu-cold` / `eu-flex` <br>
&emsp;&emsp;  `eu-de-standard` / `eu-de-vault` / `eu-de-cold` / `eu-de-flex` <br>
&emsp;&emsp;  `eu-gb-standard` / `eu-gb-vault` / `eu-gb-cold` / `eu-gb-flex` <br>
&emsp;&emsp;  `ap-standard` / `ap-vault` / `ap-cold` / `ap-flex` <br>
&emsp;&emsp;  `ams03-standard` / `ams03-vault` / `ams03-cold` / `ams03-flex` <br>
&emsp;&emsp;  `che01-standard` / `che01-vault` / `che01-cold` / `che01-flex` <br>
&emsp;&emsp;  `mel01-standard` / `mel01-vault` / `mel01-cold` / `mel01-flex` <br>
&emsp;&emsp;  `tor01-standard` / `tor01-vault` / `tor01-cold` / `tor01-flex` <br>

```bash
$ aws --endpoint-url=https://{endpoint} s3api create-bucket --bucket {bucket-name} [--region provisioning-code]
```

Listing objects within a bucket

```bash
$ aws --endpoint-url=https://{endpoint} s3api list-objects --bucket bucket1
{
    "Contents": [
        {
            "LastModified": "2016-09-28T15:36:56.807Z",
            "ETag": "\"13d567d518c650414c50a81805fff7f2\"",
            "StorageClass": "STANDARD",
            "Key": "c1ca2-filename-00001",
            "Owner": {
                "DisplayName": "{storage-account-uuid}",
                "ID": "{storage-account-uuid}"
            },
            "Size": 837
        },
        {
            "LastModified": "2016-09-09T12:49:58.018Z",
            "ETag": "\"3ca744fa96cb95e92081708887f63de5\"",
            "StorageClass": "STANDARD",
            "Key": "c9872-filename-00002",
            "Owner": {
                "DisplayName": "{storage-account-uuid}",
                "ID": "{storage-account-uuid}"
            },
            "Size": 533
        },
        {
            "LastModified": "2016-09-28T15:36:17.573Z",
            "ETag": "\"a54ed08bcb07c28f89f4b14ff54ce5b7\"",
            "StorageClass": "STANDARD",
            "Key": "98837-filename-00003",
            "Owner": {
                "DisplayName": "{storage-account-uuid}",
                "ID": "{storage-account-uuid}"
            },
            "Size": 14476
        },
        {
            "LastModified": "2016-10-06T14:46:26.923Z",
            "ETag": "\"2bcc8ee6bc1e4b8cd2f9a1d61d817ed2\"",
            "StorageClass": "STANDARD",
            "Key": "abfc4-filename-00004",
            "Owner": {
                "DisplayName": "{storage-account-uuid}",
                "ID": "{storage-account-uuid}"
            },
            "Size": 20950
        }
    ]
}
```

#### Pre-signed URLs
The CLI is also capable of creating pre-signed URLs. These URLs allow for public consumption of objects without the need to provide authentication, or the need to change the access control settings of an object.

```bash
$ aws --endpoint-url=https://{endpoint} s3 presign s3://bucket-1/new-file
```

It is also possible to set an expiration time for the URL in seconds (default is 3600).

```bash
$ aws --endpoint-url=https://{endpoint} s3 presign s3://bucket-1/new-file --expires-in 600
```
