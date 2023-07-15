- Aurora is a proprietary technology from AWS (not open sourced)
- Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
- Aurora is "AWS cloud optimized" and claims 5x performance improvement and MySQL or RDS, over 3x the performance of Postgres on RDS
- Aurora storage automatically grows in increments of 10GB, up to 128 TB
- Aurora can have up to 15 replicas and the replication process is faster than MySQL (sub 10 ms replica lag)
- Failover is Aurora is instantaneous. It's High Availability native.
- Aurora costs more than RDS (20% more) - but is more efficient.

### Aurora High Availability and Read Scaling
- 6 copes of your data across 3 AZ:
	- 4 copies out of 6 needed for writes
	- 3 copies out of 6 needs for reads
	- Self healing with peer-to-peer replication
	- Storage is striped across 100s of volumes
- One Aurora Instance takes writes (master)
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross Region Replication![[Screenshot 2023-06-15 at 1.11.29 PM.png]]

### Aurora DB Cluster![[Screenshot 2023-06-15 at 1.13.22 PM.png]]


### Features of Aurora
- Automatic fail-over
- Backup and Recovery
- Isolation and Security
- Industry compliance
- Push-button scaling
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups

### Hands on
- Create Database
- Standard Create
- Select `Amazon Aurora` from Engine type
- Edition
	- Amazon Aurora MySQL-Compatible Edition
	- Amazon Aurora PostgreSQL-Compatible Edition
- Engine Version, select as per project compatibility.
- Template, select production
- Availability & Durability
	- Don't create an Aurora Replica
	- Create an Aurora Replica or Reader node in different AZ (recommended for scaled availability) (select)
- Select
	- Don't connect an EC2 compute resource
	- Yes for Public access
	- Select existing VPC security group or create one
	- Disable enhanced monitoring
- Other useful information we select when creating [[RDS#Hands on|RDS]]
- Create
- Once created, you can see you'll have one regional cluster with one write instance and one read instance
- if you click on cluster, you can see the reader endpoint and writer endpoint
- These endpoints will always be same and will not change.
- The child instances will have their own endpoints to connect
- You can do following from `Action` button
	- Add AWS region
	- Add reader
	- Create cross-region read-replica
	- Create clone
	- Restore to point in time
	- Add replica auto-scaling
- Click on `Add replica auto-scaling`
	- A new form will open
	- Policy details
		- Policy name
		- IAM role (auto filled)
		- Target metric
			- Average CPU utilization of aurora replicas
			- Average connections of Aurora replicas
		- Target value (60%)
	-  Cluster capacity details
		- Minimum capacity
		- Maximum capacity
	- Add policy
