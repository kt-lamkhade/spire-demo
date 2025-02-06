


## Step 1: **Enable IAM Authentication on Your RDS Instance**

To enable or disable IAM database authentication for an existing DB instance
Open the Amazon RDS console at https://console.aws.amazon.com/rds/.

In the navigation pane, choose Databases.

Choose the DB instance that you want to modify.


Note
Make sure that the DB instance is compatible with IAM authentication. Check the compatibility requirements in Region and version availability.

Choose ```Modify```.


In the Database authentication section, choose Password and IAM database authentication to enable IAM database authentication. Choose Password authentication or Password and Kerberos authentication to disable IAM authentication.

![DB](../main/DB_IAM.png)

Choose ```Continue```.

To apply the changes immediately, choose Immediately in the Scheduling of modifications section.

Choose Modify DB instance .

## Step 2: **Create an IAM Role and Policy**

### 1: Create an IAM Policy:

Go to the AWS IAM console.

Create a new policy with JSON permissions like this:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "rds-db:connect",
      "Resource": "arn:aws:rds-db:your-region:your-account-id:dbuser:your-db-cluster-id/your-db-username"
    }
  ]
}
```
Replace placeholders with your actual AWS region, account ID, DB cluster ID, and DB username.

### 2: Create an IAM Role:

Create a new IAM role for EKS with the following trust relationship:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "eks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Attach the previously created policy to this role.

============================================


## Step 3: Enable IAM Authentication on Your RDS Instance.

#### Prerequisites: 
- AWS RDS Postgres Instance: Ensure you have an RDS Postgres instance set up.
- IAM Role: Create an IAM role with permissions to connect to the RDS instance.
- SPIRE Server: Installed spire server with and running in your environment.


This is the complete list of configuration options under the database_type setting when aws_postgres is set:

| Configuration      | Description                                                  | Required                                               | Default                                                 |
|--------------------|--------------------------------------------------------------|--------------------------------------------------------|---------------------------------------------------------|
| access_key_id      | AWS access key id.                                           | Required only if AWS_ACCESS_KEY_ID environment variable is not set. | Value of AWS_ACCESS_KEY_ID environment variable.         |
| secret_access_key  | AWS secret access key.                                       | Required only if AWS_SECRET_ACCESSKEY environment variable is not set. | Value of AWS_SECRETACCESSKEY environment variable.       |
| region             | AWS region of the database.                                  | Yes.                                                   |                                                         |



#### Sample configuration:

```
    DataStore "sql" {
        plugin_data {
            database_type "aws_postgres" {
                region = "us-east-2"
            }
            connection_string = "dbname=spire user=test_user host=spire-test.example.us-east-2.rds.amazonaws.com port=5432 sslmode=require"
        }
   }
```























