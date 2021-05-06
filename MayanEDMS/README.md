Mayan EDMS
===

Docker-compose project for localhost setup of Mayan EDMS.

Consists of:

- MayanEDMS App
- PostgreSQL
- Redis
- **TODO:** PostgreSQL Periodic backup to S3

## Configure

An `.ENV` file must be placed to this directory for custom configuration. Personal `.ENV` file is kept in my password manager, as it contains credentials to an Amazon S3 bucket and configuration values for the Django storage backend.

The `.ENV` file looks somewhat like this:

```
MAYAN_PIP_INSTALLS='django-storages boto3'
MAYAN_DOCUMENTS_STORAGE_BACKEND=storages.backends.s3boto3.S3Boto3Storage
MAYAN_DOCUMENTS_STORAGE_BACKEND_ARGUMENTS="{'access_key':'[ACCESS_KEY]','secret_key':'[SECRET_KEY]','bucket_name':'[BUCKET_NAME]','default_acl':'private'}"
```

See the chapters below to find out how to get the `access_key`, `secret_key` and `bucket_name`.

## Run

	$ cd [THIS_DIR]
	$ docker-compose up -d

Go to [http://localhost:8088](http://localhost:8088)

## Set up an Amazon S3

We will set up:

- **Bucket** `mayanedms`
- **Policy** `MayanEDMSAdministrator`
- **User Group** `MayanEDMSAdministators`
- **Application User** `appl_mayanedms`

### Create a bucket

1. In Amazon Console go to

		Services -> Storage -> S3 -> Buckets
2. Click **Create bucket**
3. **Bucket name**: `mayanedms`
	**Server-side encryption**: `Enable`; **Encryption type**: `Amazon S3 key (SSE-S3)`
4. Click **Create bucket**

### Create a policy

1. In Amazon Console go to

		Services -> Security, Identity, & Compliance -> IAM -> Access Management -> Policies

2. Click **Add policy**
3. Select the ***JSON* tab** and put the following in the editor:

	```
	{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Sid": "0",
	            "Effect": "Allow",
	            "Action": [
	                "s3:PutObject",
	                "s3:GetObject",
	                "s3:ListBucket"
	            ],
	            "Resource": [
	                "arn:aws:s3:::mayanedms",
	                "arn:aws:s3:::mayanedms/*"
	            ]
	        }
	    ]
	}
	```

4. Click **Next: Tags**
5. Click **Next: Review**
6. Insert into field **Name**: `MayanEDMSAdministrator`
7. Click **Create policy**

### Create a user group

1. In Amazon Console go to

		Services -> Security, Identity, & Compliance -> IAM -> Access Management -> User Groups

2. Click **Create group**
3. **User group name**: `MayanEDMSAdministators` 
	**Attach permissions policies**: `MayanEDMSAdministrator`
4. Click **Create group**


### Create a user

1. In Amazon Console go to

		Services -> Security, Identity, & Compliance -> IAM -> Access Management -> Users

2. Click **Add user**

3. **User name**: `appl_mayanedms` 
	**Access Type**: `Programmatic access`
4. Click **Next: Permissions**
5. **Add user to group**: `MayanEDMSAdministators`
4. Click **Next: Tags**
5. Click **Next: Review**
6. Click **Create user**
7. Save the credentials:
	- Save the **Access key ID** (`access_key`)
	- Save the **Secret access key** (`secret_key`)
