# vishal-cloud-audio-github-readme

Understand Amazon Polly
Amazon Polly is an AWS service that converts written text into natural-sounding speech using machine learning.

It is a fully managed service, meaning you don't need to provision servers, install software or train a machine learning model.

Understanding Amazon S3
2A.1 — What is Amazon S3?
Amazon S3 stands for Amazon Simple Storage Service.

It is a cloud-based object storage service that allows you to store and retrieve files over the internet without having to manage physical storage infrastructure or servers.

You can use S3 to store:
- Images, videos and audio files.
- Datasets for machine learning.
- Database backups.
- Application logs.
- Static website assets.
- Documents and reports.
In our case, we're storing the 562 KB MP3 file we generated using Amazon Polly.
2A.2 — What is object storage?
There are three major types of storage in cloud computing.
1. File storage: Organizes files into a hierarchy of folders and subfolders, like your computer.
AWS example: Amazon EFS
2. Block Storage: Stores data in individual blocks, similar to how a computer's hard drive works.
AWS example: Amazon EBS
3. Object Storage: Stores data as independent objects, each with a unique identifier and metadata.
AWS example: Amazon S3 — our choice
Concept 1 — Bucket
A bucket is a container that holds objects in Amazon S3.

Think of it as a dedicated storage container for our project.

Our chosen bucket name is:

vishal-cloud-audio-github-readme

S3 general-purpose bucket names must be globally unique within an AWS partition, contain no uppercase letters and follow AWS naming restrictions.
Concept 2 — Object
An object is an individual piece of data stored inside a bucket, together with its associated metadata.

Our object is intro.mp3.
Concept 3 — Object key
An object key is the unique identifier of an object within its bucket.

For example:
File location	Object key
At the bucket root	intro.mp3
Inside an audio prefix	audio/intro.mp3
Inside a portfolio prefix	portfolio/audio/intro.mp3

Interestingly, S3 doesn't have traditional folders. It uses prefixes in object keys to simulate a folder structure.

Since we're storing only one audio file, we'll keep it at the bucket root.
Concept 4 — Metadata
Metadata is information describing an object.

For our MP3, this includes:
Metadata	Value
Content-Type	audio/mpeg
Content-Length	Actual file size in bytes
Last-Modified	Upload or modification timestamp
Server-side encryption	To be configured

The content type is particularly important because it helps browsers recognize that our file is playable audio.
2A.4 — S3 storage classes
AWS offers different storage classes depending on how frequently you need to access your data.
Storage class	Intended use
S3 Standard	Frequently accessed files
S3 Intelligent-Tiering	Unpredictable access patterns
S3 Standard-IA	Infrequently accessed files
S3 One Zone-IA	Infrequent, reproducible data
S3 Glacier Instant Retrieval	Rarely accessed archives
S3 Glacier Flexible Retrieval	Long-term archives
S3 Glacier Deep Archive	Very long-term archives

We're choosing S3 Standard.

Our audio needs to be immediately available whenever a recruiter visits our GitHub profile. Given its tiny file size, there is little reason to introduce archival storage or retrieval complexity.
2A.5 — Public vs. private buckets
This is one of the most important security concepts in our project.

There are two possible approaches to delivering our audio.
We're using Origin Access Control (OAC) in Phase 3 to authorize CloudFront to retrieve our audio from S3.

This means:
- Visitors cannot directly access our private S3 object anonymously.
- CloudFront is authorized to retrieve the MP3.
- Visitors can listen through the public CloudFront HTTPS URL.
- We can keep S3 Block Public Access enabled.
This demonstrates the cloud security principle of least privilege: granting only the permissions necessary to perform a particular task.

What is a Content Delivery Network (CDN)?
A Content Delivery Network is a geographically distributed network of servers that delivers content to users efficiently by serving it from locations closer to them.

Amazon CloudFront is AWS's CDN service.

Imagine three recruiters visiting your GitHub profile from Toronto, London and Sydney.

Without a CDN, all three would retrieve the audio from our S3 bucket in Ohio, assuming direct access were permitted.

With a CDN, CloudFront can serve cached copies from edge locations closer to each recruiter.

The main benefits of a CDN are:
- Lower latency: Reduced waiting time for users.
- Caching: Fewer repeated requests to the original server.
- Scalability: Ability to handle large numbers of visitors.
- Security: Controlled access to the original content.
- Global availability: Efficient content delivery across geographic locations.
For our relatively small audio file, the biggest benefits are secure delivery and learning production-style cloud architecture.

Five fundamental CloudFront concepts
Concept 1 — Origin
An origin is the original source of the content that CloudFront delivers.

CloudFront can retrieve content from various origins, including S3 buckets, EC2 instances, load balancers and external web servers.

Our origin is:

vishal-cloud-audio-github-readme

This is where our original audio file lives.
Concept 2 — Distribution
A distribution is a CloudFront resource that defines how content is retrieved, cached, secured and delivered to visitors.

Think of it as the configuration controlling our CDN.

Our planned distribution name is:

vishal-audio-github-readme-cdn

Once created, CloudFront will assign us a domain resembling:

dxxxxxxxxxxxxx.cloudfront.net

We don't need to purchase a domain name.
Concept 3 — Edge location
An edge location is a network location where CloudFront can receive viewer requests and cache content.

Unlike our S3 bucket, which resides in a specific AWS region, CloudFront operates through a global network.
Amazon S3	Amazon CloudFront
Regional storage resource	Global content delivery service
Stores the original file	Can cache copies of the file
Located in us-east-2	Uses distributed edge locations
Private in our architecture	Public-facing in our architecture

Concept 4 — Cache
A cache is temporary storage containing previously retrieved content.

Instead of retrieving intro.mp3 from S3 every time someone requests it, CloudFront can reuse a cached copy.

There are two important scenarios: cache hits and cache misses.

Cache behavior
A cache behavior defines how CloudFront handles requests.

It controls settings such as which origin to use, which HTTP methods to allow, HTTPS requirements and caching policies.

For our project, one default cache behavior should be sufficient because we're delivering only one audio file.
3A.3 — Time to Live (TTL)
CloudFront doesn't necessarily keep cached content forever.

TTL stands for Time to Live. It determines how long cached content can remain fresh before CloudFront needs to check or retrieve it again.

What happens if we update our audio?
Suppose you generate a new narration and replace intro.mp3 in S3.

CloudFront may continue delivering the old cached version until it expires.

We can address this using cache invalidation, which tells CloudFront to remove a cached object so subsequent requests retrieve the updated content.

Origin Access Control (OAC)
This is arguably the most important technical concept in our entire project.

Remember our previous security test?

When you opened the direct S3 Object URL in incognito, you received Access Denied.

So how can CloudFront retrieve the same file?

The answer is Origin Access Control, or OAC.

OAC allows CloudFront to send authenticated, cryptographically signed requests to our private S3 bucket.

However, OAC alone is not enough. Our S3 bucket policy must also authorize those requests.

Component	Responsibility
CloudFront	Receives public requests and delivers content
OAC	Signs requests sent to S3
S3 bucket policy	Authorizes our specific CloudFront distribution
Block Public Access	Prevents public access grants to S3

This is how we maintain a private origin while delivering publicly accessible content.
3A.5 — HTTP methods
HTTP methods describe what a client wants to do with a resource.
Method	Purpose	Needed by visitors?
GET	Retrieve content	Yes
HEAD	Retrieve response headers without the body	Yes
POST	Submit data	No
PUT	Create or replace data	No
PATCH	Modify data	No
DELETE	Delete data	No
OPTIONS	Discover communication options	Not currently

We'll aim to allow only GET and HEAD through our CloudFront distribution.

Visitors should be able to retrieve our audio, but they shouldn't be able to modify or delete it.

This is another application of the principle of least privilege.
3A.6 — HTTP vs. HTTPS
HTTPS encrypts communication between a client and a server using TLS.

We've already configured encryption at rest through SSE-S3. Now we're introducing encryption in transit.



