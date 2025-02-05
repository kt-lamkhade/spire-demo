



To enable or disable IAM database authentication for an existing DB instance
Open the Amazon RDS console at https://console.aws.amazon.com/rds/.

In the navigation pane, choose Databases.

Choose the DB instance that you want to modify.


Note
Make sure that the DB instance is compatible with IAM authentication. Check the compatibility requirements in Region and version availability.

Choose ```Modify```.

![DB](../main/DB_IAM.png)
In the Database authentication section, choose Password and IAM database authentication to enable IAM database authentication. Choose Password authentication or Password and Kerberos authentication to disable IAM authentication.

Choose ```Continue```.

To apply the changes immediately, choose Immediately in the Scheduling of modifications section.

Choose Modify DB instance .
