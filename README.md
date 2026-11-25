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


