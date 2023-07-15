- CloudFront is Content Delivery Network (CDN)
- Improve read performance, content is cached at the edge
- Improves user experience
- 450+ points of presence globally (edge locations)
- 13 regional edge cache in 90+ cities across 49 countries
- DDoS protection (because worldwide), integration with shield, AWS Web Application Firewall

### Origins
- S3 bucket
	- For distributing files and caching them at the edge
	- Enhancing security with CloudFront Origin Access Control (OAC)
	- OAC is replacing Origin Access Identity (OAI)
	- CloudFront can be used as an ingress (to upload files to S3)
- Custom Origin (HTTP)
	- Application load balancer
	- EC2 instance
	- S3 website (must first enable the bucket as a static S3 website)
	- Any HTTP backend you want

### [[CloudFront]] vs [[S3#Replication|S3 Cross Region Replication]]

CloudFront | S3 Cross Region Replication
--- |---
Global Edge Network | Must be setup for each region you want replication to happen
Files are cached for a TTL (maybe a day) | Files are updated in near real-time
Great for static content that must be available everywhere | Great for dynamic content that needs to be available at low-latency in few regions
-- | Read only

### Hands on
- Create S3 bucket for file distribution
- Open CloudFront dashboard
- CloudFront is Global service
- Create CloudFront distribution, a form will open
- Choose origin domain, here we will choose our S3 bucket site domain
- Name the CloudFront distribution
- Origin access
	- Public
	- Origin access control settings (select)
		- Create or select `Origin access control`
	- Legacy access identities
- Enable Origin Shield
	- Yes
	- No
- Skip most of the settings
- Enter `index.html` in default root object
- Create
- At the top, there will be a header to edit S3 policy and there will be a button to `Copy Policy`, this policy is provided by Amazon as per need
- Edit S3 bucket policy to be accessed by CloudFront
- Paste the copied policy
- Save
- Now check the CloudFront distribution domain, copy and paste in browser
- it should work

## Caching & Caching Policies
- The cache lives at each CloudFront Edge Location
- CloudFront identifies each object in the cache using the Cache Key (see next slide)
- You want to maximize the Cache Hit ratio to minimize requests to the origin
- You can invalidate part of the cache using the CreateInvalidation API![[Screenshot 2023-07-02 at 10.52.30 PM.png]]

### CloudFront CacheKey![[Screenshot 2023-07-02 at 10.54.17 PM.png]]
- A unique identifier for every object in the cache
- By default, consists of hostname + resource portion of the URL
- If you have an application that serves up content that varies based on user, device, language, location...You can add other elements (HTTP headers, cookies, query strings) to the Cache Key using CloudFront Cache Policies

### Cache Policy
- Cache based on:
	- HTTP Headers: 
		- None
			- Don't include any headers in the Cache Key (except default)
			- Headers are not forwarded (except default)
			- Best caching performance
		- Whitelist
			- Only specified headers included in the Cache key
			- Specified headers are also forwarded to Origin
	- Cookies: 
		- None
		- Whitelist
		- Include All-Except
		- All
	- Query Strings
		- None
			- Don't include any query strings in the Cache Key
			- Query strings are not forwarded
		- Whitelist
			- Only specified query strings included in the Cache Key
			- Only specified query strings are forwarded
		- Include All-Except
			- Include all query strings in the Cache Key except the specified list
			- All query strings are forwarded except the specified list
		- All
			- Include all query strings in the Cache Key
			- All query strings are forwarded
			- Worst caching performance
- Control the TTL(0 seconds to 1 year), can be set by the origin using the Cache-Control headers, Expires header...
- Create your own policy or use predefined Managed Policies
- All HTTP headers, cookies, and query strings that you include in the Cache Key are automatically included in origin requests
- Origin Request Policy
	- Specify values that you want to include in origin requests without including them in the cache key (no duplicated cached content)
	- You can include
		- HTTP headers
			- None
			- Whitelist
			- All viewer headers options
		- Cookies
			- None
			- Whitelist
			- All
		- Query Strings
			- None
			- Whitelist
			- All
	- Ability to add CloudFront HTTP headers and Custom Headers to an origin request that were not included in the viewer request
	- Create your own policy or use Predefined Managed Policies

### Cache Invalidation
- In case you update the backend origin, CloudFront doesn't know about it and will only get the refreshed content after the TTL has expired
- However, you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront Invalidation
- You can invalidate all files (\*) or a specific path (/images/\*)

### Cache Behaviours
- Configure different settings for a give URL path pattern
- Example: one specific cache behaviour to images/\*.jpg files on your origin web server
- Route to different kind of origins/origin groups based on the content type or path pattern
	- /images/\*
	- /api/\*
	- /\* (default cache behaviour)
- When adding additional Cache Behaviours, the Default Cache Behaviour is always the last to be processed and is always /\*

### ALB or EC2 as an origin
- It is possible for CloudFront to connect with any backend cluster
- EC2 instance must be public
- There is no private VPC connectivity in CloudFront![[Screenshot 2023-07-02 at 11.38.49 PM.png]]


### Geo Restriction
- You can restrict who can access your distribution
	- Allowlist: Allow your users to access your content only if they're in one of the countries on a list of approved countries
	- Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.
- The "Country" is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

### Signed URL / Signed Cookies
- You want to distribute paid shared content to premium users over the world
- We can use CloudFront Signed URL / Cookie. We attach a policy with:
	- Include URL expiration
	- Include IP ranges to access the data from
	- Trusted signers (which AWS accounts can create signed URLs)
- How long should the URL be valid for ?
	- Shared content (movie, music): make it short (a few minutes)
	- Private content (private to the user): you can make it last for years
- Signed URL = access to individual files (one signed URL per file)
- Signed Cookies = access to multiple files (one signed cookie for many files)

### CloudFront Signed URL vs [[S3#S3 Pre-Signed URLS|S3 Pre-Signed URL]]

CloudFront Signed URL | S3 Pre-Signed URL
--- |---
Allow access to a path, no matter the origin | Issue a request as the person who pre-signed the URL
Account wide key-pair, only the root can manage it | Uses the IAM key of the signing IAM principal
Can filter by IP, path, date, expiration | Limited lifetime
Can leverage caching features | --

- Two types of signers
	- Either a trusted key group (recommended)
		- Can leverage APIs to create and rotate keys (and IAM for API security)
	- An AWS Account that contains a CloudFront Key Pair
		- Need to manage keys using the root account and the AWS console
		- Not recommended because you shouldn't use the root account for this
	- In your CloudFront distribution, create one or more trusted key groups
	- You generate your own public/private key
		- The private key is used by your applications (eg: EC2) to sign URLs
		- The public key (uploaded) is used by CloudFront to verify URLs

### Pricing
- CloudFront Edge locations are all around the world
- The cost of data out per edge location varies
- Price classes
	- You can reduce the number of edge locations for cost reduction
	- Three price classes
		- Price Class All: All regions - best performance
		- Price Class 200: most regions, but exclude the most expensive regions
		- Price Class 100: only the least expensive regions  ![[Screenshot 2023-07-03 at 12.11.34 AM.png]]

### Multiple Origin
- To route to different kind of origins based on the content type
- Based on path pattern
	- /images/\*
	- /api/\*
	- /\*

### Origin Groups
- To increase high-availability and do failover
- Origin Group: one primary and one secondary origin
- If the primary origin fails, the second one is used

### Field Level Encryption
- Protect user sensitive information through application stack
- Adds an additional layer of security along with HTTPS
- Sensitive information encrypted at the edge close to user
- Uses asymmetric encryption
- Usage
	- Specify set of fields in POST requests that you want to be encrypted (up to 10 fields)
	- Specify the public key to encrypt them![[Screenshot 2023-07-03 at 12.24.30 AM.png]]

### Real Time Logs
- Get real-time requests received by CloudFront sent to Kinesis Data Streams
- Monitor, analyze, and take actions based on content delivery performance![[Screenshot 2023-07-03 at 12.26.06 AM.png]]


