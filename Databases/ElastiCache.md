- The same way RDS is to get managed Relational Databases... ElastiCache is to get managed Redis or Memcached
- Caches are in-memory databases with really high performance, low-latency
- Helps reduce load off of databases for read intensive workloads
- Helps make your application stateless
- AWS takes care of OS maintenance / patching, optimization, setup, configuration, monitoring, failure recovery and backups
- DB Cache
	- Using ElastiCache involves heavy application code changes
	- Application queries ElastiCache, if not available, get from RDS and store in ElastiCache.
	- Helps relieve load in RDS
	- Cache must have an invalidation strategy to make sure only the most current data is used in there.![[Screenshot 2023-06-15 at 6.43.57 PM.png]]
- User Session Store
	- User logs into any of the application
	- The application writes the session data into ElastiCache
	- The user hits another instance of our application
	- The instance retrieve the data and the user is already logged in![[Screenshot 2023-06-15 at 6.47.14 PM.png]]
### [[Redis]] vs [[Memcached]]

redis | Memcached
--- |--- 
Multi AZ with Auto-failover | Multi-Node for partitioning of data (sharding)
Read Replicas to scale reads and have high availability | No high availability (replication)
Data Durability using AOF persistence | No persistence
Backup and restore features | No backup and restore
Supports Sets and Sorted Sets | Multi-threaded architecture

### Hands on
- Go to ElastiCache dashboard
- Click on `Get Started` button
- Get started with ElastiCache
	- Initial Setup
	- Create a Cluster
		- Click on create cluster
		- Select `Create Redis cluster`
		- A form page will open
		- Cluster settings
			- Choose a cluster creation method
				- Configure and create a new cluster (select)
				- Restore from backups
			- Cluster mode
				- Enabled
				- Disabled (select), this will have one write node and can have up to 5 read replicas
			- Cluster Info
				- Name
				- Description
			- Location
				- AWS Cloud (select)
				- On premises
				- Multi AZ (disable)
				- Auto failover (select)
			- Cluster settings
				-  Engine version
				- Port
				- Parameter groups
				- Node type
					- cache.t2.micro
				- Number of replicas
					- 0
			- Subnet group settings
				- Choose existing subnet group
				- Create a new subnet group (select)
				- Name
				- description
				- VPC ID
				- selected subnets
			- Availability zone preferences (none)
			- Next
		- Advanced settings
			- Security
				- Encryption at rest (disable it for now)
				- Encryption at transit (disable it for now)
			- Backup
				- Disable it for now
			- Maintenance
				- select `Auto upgrade minor versions`
		- Review other settings and create

### ElastiCache strategies
- [Caching strategies](https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html)
- [Best Practices](https://aws.amazon.com/caching/best-practices/)
- Is it safe to cache data ?
	- Data may be out of date, eventually consistent
- Is caching effective for that data ?
	- Pattern: data changing slowly, few keys are frequently needed
	- Anti patterns: data changing rapidly, all large key space frequently needed
- Is data structured well for caching ?
	- Example: key value caching, or caching of aggregation results
- Which design pattern is the most appropriate ?
	1. Lazy loading / Cache aside / Lazy population![[Screenshot 2023-06-15 at 7.24.34 PM.png]]
		- Pros
			- Only requested data is cached (the data isn't filled up with unused data)
			- Node failures are not fatal (just increased latency to warn the cache)
		- Cons
			- Cache miss penalty that results in 3 round tripes, noticeable delay for that request
			- Stale data: data can be updated in the database and outdated in the cache
	2.  Write through - Add or Update cache when database is updated![[Screenshot 2023-06-15 at 7.22.17 PM.png]]
		- Pros
			- Data is cache is never stale, reads are quick
			- Write penalty vs Read Penalty (each write requires 2 calls)
		- Cons
			- Missing data until it is added/updated in the DB. Mitigation is to implement Lazy Loading strategy as well
			- Cache Churn - a lot of the data will never be read
- Cache Eviction and Time to Live (TTL)
	- Cache eviction can occur in three ways:
		- You delete the item explicitly in the cache
		- Item is evicted because the memory is full and it's not recently used (LRU)
		- You set an item time-to-live (TTL)
	- TTL are helpful for any kind of data
		- Leaderboards
		- Comments
		- Activity streams
	- TTL can range from few seconds to hours or days
	- If too many evictions happen due to memory, you should scale up or out

### Final words of Wisdom
- Lazy loading / Cache aside is easy to implement and works for many situations as a foundation, especially on the read side
- Write-through is usually combined with Lazy loading as targeted for the queries or workloads that benefit from optimization
- Setting a TTL is usually not a bad idea, except when you're using Write-through. Set it to a sensible value for your application
- Only cache the data that makes sense (user profiles, blogs, etc)



