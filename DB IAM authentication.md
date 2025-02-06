


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

============================================


# Enable IAM Authentication on Your RDS Instance.

#### Prerequisites: 
- AWS RDS Postgres Instance: Ensure you have an RDS Postgres instance set up.
- IAM Role: Create an IAM role with permissions to connect to the RDS instance.
- SPIRE Server: Installed spire server with and running in your environment.


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

In this deployments we have upgraded the SPIRE server, SPIRE agent, Tornjak versions has been updated to the latest available version.
- SPIRE Server & Agent version: 1.11.1
- spiffe-csi-driver:0.2.6
- Tornjak: 1.9.0 

#### AWS Postgres database:
- We have used AWS Postgres RDS which is deployed via AWS console insted of ordering from Allianz managed service.
- All the credentials to connect to the database has been masked in the Gitrepo.
- The Postgres RDS is deployed in the same transitional zone as the cluster 2 is deployed.
- Updated the security group to allow the traffic to the DB port 5432.

#### Changes in helm to use the external AWS Postgres database.
Updated the helm chart which is in spire/charts/spire-server/values.yaml

```
dataStore:
  sql:
    ## @param dataStore.sql.databaseType Other supported databases are ["postgres", "mysql", "aws_postgresql", "aws_mysql"]. Note: aws type databases are still experimental
    databaseType: postgres
    ## @param dataStore.sql.databaseName Only used when type != "sqlite3"
    databaseName: spire
    ## @param dataStore.sql.host Only used when type != "sqlite3"
    host: "spire-db-cl2.clk5awknlkwi.eu-central-1.rds.amazonaws.com"
    ## @param dataStore.sql.port If 0 (default), it will auto set to 5432 for postgres and 3306 for mysql. Only used by those databases.
    port: 5432
    ## @param dataStore.sql.username Only used when type != "sqlite3"
    username: postgres
    ## @param dataStore.sql.password Only used when type != "sqlite3"
    password: "******"
    ## @param dataStore.sql.options [array] Only used when type != "sqlite3"
    options: []
```

#### Update the DB configs in the below files.
- spire/charts/spire-server/values.yaml
- spire/ci/external-postgres-values.yaml

Ref:

https://spiffe.io/docs/latest/spire-about/spire-concepts/

https://spiffe.io/docs/latest/deploying/configuring/#configure-sqlite-as-a-spire-data-store


#### Other changes.
Since this is a new cluster (Cluster 2), the names and VPC names specified in Helm has been changed to reflect the new cluster/vpc names."

### Configurations for SPIRE: 
Following configurations are made in the existing installation:
- Controller Manager in SPIRE server set to true - This is auto register the workloads.
- Enabled sds in  SPIRE Agent for Envoy SDS configuration for istio.

### Install & Configure Istio:
The following steps are same as Stage-2 for enabling Istio in the cluster.
Configurations made for enabling Istio with SPIRE server.

Note: Since CRP2.0 Istio incorporates custom features and configurations, To support SPIRE configurations, we deployed a new Istio controller.

- installed istio using istioctl and refer the file istio/istio.yaml in this repo.

```istioctl install --skip-confirmation -f istio/istio.yaml```
- SPIRE server sockets mount in istio gateway as a PV
- Deployed Kind: CLUSTERSPIFFEID for Istio gateway and sidecar for Auto-registration using the SPIRE Controller Manager

Ref: https://istio.io/latest/docs/ops/integrations/spire/

### Workload:
- Added annoation to workload  (Bookinfo application) for injecting Istio sidecar and related configurations. 
``` 
annotations:
inject.istio.io/templates: sidecar,spire
spiffe.io/spire-managed-identity: "true" 
```

#### SPIRE Server DB plugin details in ConfigMap & Secret.

```k get cm spire-server -n spire-server -o yaml```

![stage-2-spire-db-cm](asset/stage-3-spire-db-cm.png)

```k get secrets spire-server-dbpw -n spire-server -o yaml```

![stage-3-spire-db-secret](asset/stage-3-spire-db-secret.png)


## Demo
In this demo, the agenda is to demonstrate that the SPIRE server is using the external Postgres database for keeping the workload and other SPIRE related entries.

#### SPIRE Server pods running status.

```k get pods -n spire-server```

![stage-3-spire-pods-running](asset/stage-3-spire-pods-running.png)


#### Workloads pods running status.

```k -n ns01 get pods```

```k -n ns02 get pods```

```k -n ns03 get pods```

![stage-3-echo-applications-running](asset/stage-3-echo-applications-running.png)


#### DB view - Node registration entries.

![stage-3-db-view-node-entries](asset/stage-3-db-view-node-entries.png)


#### DB view - Workload registration entries.

![stage-3-db-view-reg-entries](asset/stage-3-db-view-reg-entries.png)


#### DB view - Workload registration entries details view.

![stage-3-db-view-reg-entries-details](asset/stage-3-db-view-reg-entries-details.png)

