


Step 1: **Enable IAM Authentication on Your RDS Instance**

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

Step 2: **Create an IAM Role and Policy**

1: Create an IAM Policy:

Go to the AWS IAM console.

Create a new policy with JSON permissions like this:

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

Replace placeholders with your actual AWS region, account ID, DB cluster ID, and DB username.

2: Create an IAM Role:

Create a new IAM role for EKS with the following trust relationship:

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


Attach the previously created policy to this role.


