# Terraform-AWS-Oracle-RDS

AWS Oracle DB Cluster & Instance(s) Terraform Module.

This IaC spawns the following:

 - A DB subnet group
 - An Oracle DB cluster
 - An Oracle DB instance + 'n' number of additional instances
 - Optionally RDS 'Enhanced Monitoring' + associated required IAM role/policy (by simply setting the `monitoring_interval` param to > `0`
 - Optionally sensible alarms to SNS (high CPU, high connections, slow replication)
 - Optionally configure autoscaling for read replicas (MySQL clusters only)
 - Optionally exporting DB logs to CloudWatch Logs

## Terraform version compatibility

| Module version | Terraform version |
|----------------|-------------------|
| 4.x.x          | 0.12.x            |
| 3.x.x          | 0.11.x            |

## Known issues

AWS doesn't automatically remove RDS instances created from autoscaling when you remove the autoscaling rules and this can cause issues when using Terraform to destroy the cluster.  To work around this, you should make sure there are no automatically created RDS instances running before attempting to destroy a cluster.

## Breaking changes

* Version 4.0.0 onwards will only support Terraform 0.12 and above.
* As of version 3.0.0 of the module the rds-enhanced-monitoring role is now named using a name\_prefix instead of a name. This will result in the role being recreated with a new name when you update to it.

## Usage examples

*It is recommended you always create a parameter group, even if it exactly matches the defaults.*

Changing the parameter group in use requires a restart of the DB cluster, modifying parameters within a group  
may not (depending on the parameter being altered)


<!--
The Inputs and Outputs sections below are automatically generated in the master branch,
so don't bother manually changing them.
-->
<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:-----:|
| aws\_region | n/a | `string` | n/a | yes |
| azs | List of AZs to use | `list(string)` | n/a | yes |
| envname | Environment name (eg,test, stage or prod) | `string` | n/a | yes |
| envtype | Environment type (eg,prod or nonprod) | `string` | n/a | yes |
| name | Name given to DB subnet group | `string` | n/a | yes |
| password | Master DB password | `string` | n/a | yes |
| security\_groups | VPC Security Group IDs | `list(string)` | n/a | yes |
| subnets | List of subnet IDs to use | `list(string)` | n/a | yes |
| apply\_immediately | Determines whether or not any DB modifications are applied immediately, or during the maintenance window | `string` | `"false"` | no |
| auto\_minor\_version\_upgrade | Determines whether minor engine upgrades will be performed automatically in the maintenance window | `string` | `"true"` | no |
| backup\_retention\_period | How long to keep backups for (in days) | `string` | `"7"` | no |
| cw\_alarms | Whether to enable CloudWatch alarms - requires `cw_sns_topic` is specified | `string` | `false` | no |
| cw\_eval\_period\_connections | Evaluation period for the DB connections alarms | `string` | `"1"` | no |
| cw\_eval\_period\_cpu | Evaluation period for the DB CPU alarms | `string` | `"2"` | no |
| cw\_eval\_period\_replica\_lag | Evaluation period for the DB replica lag alarm | `string` | `"5"` | no |
| cw\_max\_conns | Connection count beyond which to trigger a CloudWatch alarm | `string` | `"500"` | no |
| cw\_max\_cpu | CPU threshold above which to alarm | `string` | `"85"` | no |
| cw\_max\_replica\_lag | Maximum Oracle replica lag in milliseconds above which to alarm | `string` | `"2000"` | no |
| cw\_sns\_topic | An SNS topic to publish CloudWatch alarms to | `string` | `"false"` | no |
| db\_cluster\_parameter\_group\_name | The name of a DB Cluster parameter group to use | `string` | `"default.aurora5.6"` | no |
| db\_parameter\_group\_name | The name of a DB parameter group to use | `string` | `"default.aurora5.6"` | no |
| enabled | Whether the database resources should be created | `string` | `true` | no |
| engine | Aurora database engine type, currently aurora, aurora-mysql or aurora-postgresql | `string` | `"aurora"` | no |
| engine-version | Aurora database engine version. | `string` | `"5.6.10a"` | no |
| final\_snapshot\_identifier | The name to use when creating a final snapshot on cluster destroy, appends a random 8 digits to name to ensure it's unique too. | `string` | `"final"` | no |
| iam\_database\_authentication\_enabled | Whether to enable IAM database authentication for the RDS Cluster | `string` | `false` | no |
| identifier\_prefix | Prefix for cluster and instance identifier | `string` | `""` | no |
| instance\_type | Instance type to use | `string` | `"db.t2.small"` | no |
| kms\_key\_id | Specify the KMS Key to use for encryption | `string` | `""` | no |
| monitoring\_interval | The interval (seconds) between points when Enhanced Monitoring metrics are collected | `string` | `0` | no |
| performance\_insights\_enabled | Whether to enable Performance Insights | `string` | `false` | no |
| port | The port on which to accept connections | `string` | `"1521"` | no |
| preferred\_backup\_window | When to perform DB backups | `string` | `"02:00-03:00"` | no |
| preferred\_maintenance\_window | When to perform DB maintenance | `string` | `"sun:05:00-sun:06:00"` | no |
| publicly\_accessible | Whether the DB should have a public IP address | `string` | `"false"` | no |
| replica\_count | Number of reader nodes to create.  If `replica_scale_enable` is `true`, the value of `replica_scale_min` is used instead. | `string` | `"3"` | no |
| replica\_scale\_cpu | CPU usage to trigger autoscaling at | `string` | `"70"` | no |
| replica\_scale\_enabled | Whether to enable autoscaling for RDS Aurora (MySQL) read replicas | `string` | `false` | no |
| replica\_scale\_in\_cooldown | Cooldown in seconds before allowing further scaling operations after a scale in | `string` | `"300"` | no |
| replica\_scale\_max | Maximum number of replicas to allow scaling for | `string` | `"0"` | no |
| replica\_scale\_min | Maximum number of replicas to allow scaling for | `string` | `"2"` | no |
| replica\_scale\_out\_cooldown | Cooldown in seconds before allowing further scaling operations after a scale out | `string` | `"300"` | no |
| skip\_final\_snapshot | Should a final snapshot be created on cluster destroy | `string` | `"false"` | no |
| snapshot\_identifier | DB snapshot to create this database from | `string` | `""` | no |
| storage\_encrypted | Specifies whether the underlying storage layer should be encrypted | `string` | `"true"` | no |
| username | Master DB username | `string` | `"root"` | no |

## Outputs

| Name | Description |
|------|-------------|
| all\_instance\_endpoints\_list | List of all DB instance endpoints running in cluster |
| cluster\_endpoint | The 'writer' endpoint for the cluster |
| cluster\_identifier | The ID of the RDS Cluster |
| reader\_endpoint | A read-only endpoint for the Aurora cluster, automatically load-balanced across replicas |

## Contributing

Ensure any variables you add have a type and description.

Ensure your code is neat by running `terraform fmt` and fixing any missing or unnecessary whitespace.
