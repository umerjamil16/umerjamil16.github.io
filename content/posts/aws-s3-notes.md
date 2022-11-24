---
title: "AWS S3 - Notes"
date: 2022-11-23T11:30:03+00:00
tags: ["AWS"]
author: "Umer Jamil"
cover:
    image: https://miro.medium.com/max/580/1*DHe24MbDHtbkOeIJzxrfdA.png
    alt: "AWS S3" # alt text
    caption: "AWS S3" # display caption under cover
---

![AWS S3](https://miro.medium.com/max/580/1*DHe24MbDHtbkOeIJzxrfdA.png)

# AWS S3
S3 is the most famous and earliest product by AWS. Following here are my notes about it.

## Storage Types
There are there main storage types:
- Block Storage
- File Storage
- Object Storage

### Block Storage

 - Block storage is an architectural design that enables the storage of
   data onto media such as a hard disk, in fixed-sized chunks.
- On AWS, block storage options are available as Elastic Block Store
   (EBS).   
- These can be configured as volumes attached to your Elastic Compute
   Cloud (EC2) instances and offer ultra-low latency required for
   high-performance workloads.
- EBS volumes are connected via high-speed network links. This allows
   you to detach an EBS volume from one EC2 instance and attach it to
   another if, for example, the first EC2 instance experiences some sort
   of failure.
   
- Typical use cases include:
	- running and managing system files such as those used by your
   operating system,
   - large databases,
   - or for applications such as enterprise resource planning (ERP)
   solutions.
   
 - It can be HDD or SSD


### File Storage
- Hierarchical structure to store the data
- Amazon offers three different file storage systems, outlined as follows:
- Elastic File System (EFS)—This is a managed elastic filesystem designed to let you share file data without provisioning or managing storage as you would with EBS. Your filesystem will grow and shrink as you add and remove data, and mount points can only be created on Linux EC2 instances.
- FSx for Lustre—A high-performance filesystem designed for applications that require fast storage and can scale to hundreds of gigabytes (GB) of throughput and millions of input/output operations per second (IOPS). FSx for Lustre is also designed for a wide range of Linux-based EC2 instances.
- FSx for Windows File Server—Designed for Microsoft Windows EC2 instances and offers a fully managed file-share solution, natively supporting the Windows file system such as the industry-standard Server Message Block (SMB) protocol. Typical use cases include file-sharing services, local archiving, application data sharing, and data protection.”
- EFS works with Linux with multi AZ

### Object Storage
- Object storage involves storing complete files as individual objects. Object storage presents a flat file structure
- for data analytics far more easily than a file-based storage solution.
- With object storage, however, you can potentially scale to having limitless amounts of data. Whereas file system has liimtation due to its hierarchical structure
- Typical use cases for object storage include storing digital assets for your websites and applications (documents, images, video), the ability to perform analytics on your objects, and offering storage solutions to cutting-edge technologies such as Internet of Things (IoT).

S3 is an object storage. Let's dive more into details.

## S3 Bucket
- A container to hold object
- Must have unique name
- a given bucket and any objects it holds are stored in one specific Region alone. ”
- Your choice of the Region to create a given bucket is going to depend on several factors, including the following ones:
  - Optimizing latency by creating buckets closer to end users who need access to them
  - Minimizing costs
  - Addressing any regulatory requirements such as data-sovereignty laws”

## S3 Security
- User based  
  - using IAM to allow specific user access
 - Resource based  
    - Bucket policy – allow cross account  
    - Object Access Control List (ACL) - finer grain  
    - Bucket Acceess Control list (ACL)
 - Encryption
   - User is responsible
- S3 bucket policy can be used to force objects to be encrypted at upload

## S3 use cases

 - Backup and storage
 - Disaster Recovery across multi Region
 - Archive
 - Hybrid cloud storage
 - Application hosting
 - Media Hosting
 - Data lakes and big data analytics
 - Software delivery
- Static Website

## Amazon S3 charges are based around six cost components.

 - The storage itself (comprising the amount of storage, duration, and
   storage class your objects are placed in)
 - Requests and data retrievals
 - Data transfers
 - Use of transfer acceleration
 - Data management
 - Analytics


## S3 Access Logs

- For audit purpose
- Log all access to S3 buckets
- Request data analysis
- The logs are stored in another S3 bucket
- Many AWS Services use S3 as an integration phase as well

## S3 storage classes
1.  Amazon S3 Standard – General Purpose – 99.99% Availability
2.  Amazon S3 Standard – Infrequent Access – 99.90% Availability – use as a data store for disaster recovery, backup – less costly but retrival fee 
3.  Amazon S3 Intelligent Tiering – 99.99% Availability – resilience against events that impacts AZs – cost optimized option
4.  Amazon S3 One Zone – Infrequent Access [u don’t risk losing the files] - 99.5%
5.  Amazon S3 Glacier Instant Retrieval – 99.90% availbility – rearely access data
6.  Amazon Glacier – 99.99% Availability – rarely access data
7.  Amazon Galcier Deep Archive – 99.99% Availability – rarely access data


## 3 levels of data access:
1.   Standard
2.  Infrequent
3.  Rarely

## Durability vs Availbility

 - Durability: How often you lose a data, 11 9’s for all Storage classes
 - Availability: Measures how readily available a service is

## Amazon S3 Standard 

- Frequent Access 
- Default storage class 

## Amazon S3 Standard-Infrequent Access (S3 Standard-IA) 

- Infrequent Access 
- storage class 
- S3 Standard-IA is designed for data that is just as critical as with the Standard storage class but is infrequently accessed and is therefore ideal for long-term storage, such as for backups, and to act as a data store for DR purposes. 

## Amazon S3 One Zone-Infrequent Access  (S3 One Zone-IA)  

- Infrequent Access 
- Data stored in this storage class is restricted to one AZ only within the Region you upload it to.  
- availability of the data to 99.5% but is also much cheaper  
- Amazon recommends this class for data that can act as a secondary backup or that can be recreated.

## Amazon S3 Glacier Instant Retrieval 

- Data retrieval in milliseconds 
- 68% storage compared to S3 – IA 
- 99.90% availability 

## Amazon Glacier - Amazon S3 Glacier Flexible Retrieval 

This storage class is designed for long-term archiving of data that may need to be accessed infrequently and within a few hours.

- Archive Storage 
- Retrieval options: 
  - Expedited: 1-5 minutes 
  - Standard: 3-5 hours 
  - Bulk: 5-12 hours 

## Amazon Glacier Deep Archive 
- Archive Storage 
- Data Retrieval options:  
  - Standard – 12 hours 
  - Bulk – 48 hours 

Such data may be required to be kept for 7 to 10 years. Retrieval times can take 12 hours or more, depending on the retrieval option chosen.

Storing data in a DEEP glacier, data can be accessed in 12+ hours 

It is the most cheapest one. 

## Intelligent-Tiering storage class
- This storage class offers automated tiering of data depending on your access pattern. Objects are automatically transitioned across four different tiers, two of which are latency access tiers designed to move objects between frequently accessed and infrequently accessed tiers, and the other two being optional archive access tiers: 
  - Frequent and infrequent tiers
  - Optional archive access tiers

## Amazon Outposts - S3 outposts 

- A fully managed service 
- On premise infrastructure 
- When making this decision, you need to consider durability and availability, as well as the minimum size of your objects, and ascertain whether you require instant access to those objects.

## S3 Versioning 

- To protect against accidental deletions and overwriting 
- It is enabled at bucket level 

## S3 Replication
### Amazon Cross-Region Replication (CRR)

- Reduce latency 
- Increase operational efficiency 
- Meet regulatory and compliance requirements
- Must enable versioning 

### Same-Region Replication (SRR)  
- Replicating between development and production accounts
- Compliance requirements 
- Must enable versioning 

## Amazon S3 Life cycle Management

- These can be applied to the entire bucket or a subset of data by defining a prefix
- Transition Actions (SC transition)
- Expiration Actions
- Above rules can also be version specifics

  
## S3 encryption 
- All data uploaded to Amazon S3 is encrypted in transit using the HTTPS protocol. However, data stored on S3 is not encypted
- This is known as encryption at rest. ” : “To add an additional layer of security, you can encrypt the data before storing it in S3.  
- Server-side encryption—When you upload (create) an object, Amazon S3 encrypts it before saving it to disk, and when you download/request an object, it is automatically decrypted by the S3 service.


## Amazon S3TA - S3 Transfer Acceleration 

- Amazon S3 Transfer Acceleration can speed up content transfers to and from Amazon S3 by as much as 50-500% for long-distance transfer of larger objects on AWS backbone network 
- Amazon S3 Transfer Acceleration is a feature of Amazon S3 that allows you to upload your content to AWS buckets via these edge locations.
 

## Amazon Storage Gateway 

- For online data transfer  
- an on-premises solution that enables you to connect your on-premises servers and storage systems to the Amazon S3 cloud environment.
- It is just a bridge between on-prem infra and AWS 

### Use cases: 
- DR 
- Backup and restore 
- Hybrid 
- S3 tiering 

 ## AWS Snow Family 

Two use cases: 
- Collect and process data at the edge 
Snowball, Snowcone 
- Migrate data in and out of AWS through physical devices 
Snowball, snowcone, snowmobile 

 ## AWS Snowball 
- Small, edge ball 
- For offline data transfer
- customers will use Snowball edge devices for migrations, data collections, and processing with or without internet connectivity.
- Two flavours 
  - Snowball Edge Compute Optimized
  - Snowball Edge Storage Optimized 

## AWS Snowcone 
 
- The smallest member of the AWS Snow Family, these devices are the smallest ever and weigh just 4.5 pounds (lb) (2.1 kilograms (kg)). Snowcone devices come with 8 TB of usable storage and are designed for outside use in areas of low network connectivity. Examples include IoT, vehicular, and drone use cases.

## Amazon Snowmobile 
- If you need to transfer exabyte-scale data to the cloud, then you are going need an extremely large 45-foot-long rugged shipping container. Amazon Snowmobile can transfer up to 100 PB of data to the cloud and assist in all your data center migration efforts.

# Conclusion
So these were my notes which covered majority of topics related to S3. Hope they were useful.


## References

 - AWS Certified Cloud Practitioner Exam Guide:  Build your cloud computing knowledge and build your skills as an AWS Certified Cloud Practitioner (CLF-C01)
 - AWS S3 Documentation
