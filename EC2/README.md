# EC2 101

## EC2 Options

**On Demand**:  Fixed rate by the hour or second with no commitments.
  * Usage scenarios:  
    * Flexibility; no long term commitments;
    * Short term applications; spiky or unpredictable workloads that cannot be interrupted
    * Applications being developed or tested for the first time; dev/text
      environments

      Use on demand instances to supplement reserved instances during usage spikes


**Reserved**:  reserve capacity  1 or 3 year terms.
  * Usage scenarios:
    * Applications that are in steady state or predictable usage
    * Users are able to make up front payments for 1 or 3 years
    * E.g. You have 10K "regular" users requiring 2 large instances but require extra instances (on demand) for 
    * Standard Reserve Instances, you cannot change size
    * Convertable Reserve Instances, you can convert to instance type of equal or greater value
    * Scheduled Reserve Instances, launch within a time window that you specify, fraction of a day, week or month
    Black Friday traffic

**Spot**:  bid whatever price you want for instance capacity; if the market price
   goes above your bid, you are given an hour notice and Amazon terminates the
   instance.
   * Usage scenarios:
     * Applications that have flexible start and end times
     * Applications that are only feasible at very low compute prices
     * Users with urgent computing needs for large amounts of additional capacity
     * Most commercially feasible
     * **Spot instances terminated by AWS, no charge for the hour**
     * **Spot instances terminated by you, you are charged for the hour**

**Dedicated hosts**:  Dedicated physical EC2 instances
  * Usage scenarios:
    * Dedicated servers needed for existing per-server license
    * Regulatory requirements that forbid multi-tenant virtualization
    * Can be purchased On Demand (hourly) or Reserved 

**Savings Plan** 
  * Save up to 72% regardless of instance type or region
  * Commit to 1 or 3 years of specific amount of compute power measured in $/hour
  * Not just EC2.  Includes serverless like Lambda or Fargate



## EC2 Instance Families

### General Purpose

General purpose instances provide a balance of compute, memory and networking resources, and can be used for a variety of diverse workloads. These instances are ideal for applications that use these resources in equal proportions such as web servers and code repositories. 

### Compute Optimized

Compute Optimized instances are ideal for compute bound applications that benefit from high performance processors. Instances belonging to this family are well suited for batch processing workloads, media transcoding, high performance web servers, high performance computing (HPC), scientific modeling, dedicated gaming servers and ad server engines, machine learning inference and other compute intensive applications.

### Memory Optimized

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory.

### Accelerated Computing

Accelerated computing instances use hardware accelerators, or co-processors, to perform functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs.

### Storage Optimized

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage. They are optimized to deliver tens of thousands of low-latency, random I/O operations per second (IOPS) to applications.

## EBS Elastic Block storage
  1. S3 is object storage.  
  1. EBS is block storage for creating **storage volumes** that
    can be attached to EC2 instances at the OS level
  1. Once the EBS-based storage vol attached to an EC2 instance, you can
    create a file system on top of these volumes, run a database or use them in
    any way you would use a storage volume block device.   
  1. EBS volumes are placed in specific availability zones where they are automatically
    replicated within the AZ.
  1. Think: Disk in the cloud, located in a specific AZ
  1. One EC2 can attach multiple EBS volumes
  1. An EBS volume can only be mounted by one single EC2 instance; you cannot mount one EBS volume to 
  multiple EC2 instances; use EFS instead
  1. Dynamically scalable.  Increase capacity and/or change volume type with no downtime or performance impact to live systems. 

**EBS Disk Types**
  1. GP2 - General Purpose SSD
    * Designed for 99.9 availability
    * Ratio of 3 IOPS per GB, up to 16,000 IOPS, burst to 3000 for short 
    periods for volumes under 1TB
    * **Bootable volume**
  1. IO1 - Provisioned IOPS
    * For I/O intensive applications, large relational or NoSQL databases,
    * For more than 64,000 IOPS per volume, 50 IOPS per GiB 
    * **Bootable volume**
    * 99.9% durability
  1. IO2 - Latest Generation of Provisioned IOPS (SSD)
    * More durable and performant
    * Same cost as IO1
    * 500 IOPS per GiB
    * 99.999% durability
  1. ST1 Magnetic HDD
    * Large amounts of sequential data (data written in squence)
    * Big data
    * Data warehouse
    * Log processing
    * Not for randomly stored data requiring disk seeks
    * Sequential throughput optimized
    * **Cannot be a boot volume**
    * Frequently accessed large data sets, throughput intensive workloads (ETL)
    * 99.9 durability
  1. SC1 Cold HDD
    * Lower cost than ST1
    * For infrequently accessed workloads
    * Workloads in which throughput performance is not a concern
    * File server
    * **Cannot be a boot volume**
    * 99.9% durability
  1. Magnetic Standard
    * Lowest cost of all EBS volume types that are *bootable*
    * **Bootable volume**
    * Infrequently accessed data

![EBS Volumes](https://github.com/MathewT/aws-certified-developer/blob/master/EC2/ebs-volumes.PNG)

## Commands:
*List block devices:*
```bash
lsblk
```

*check if file has data:*
```bash
file -s /dev/xvdf
```

*format the disk:*
```bash
mkfs -t ext4 /dev/xvdf
```

*mount the file system to a location:*
```bash
mount /dev/xvdf /file_path
```


## Volumes and Snapshots
1. An EBS volume is a virtual hard disk
1. A created EBS volume must be in the same availability zone as the EC2 instance to which it will be attached
1. Root volume is the virtual hard disk on which the OS is installed
1. Snapshots are point-in-time copies
1. Snapshots physically exist on S3
1. Snapshots are incremental.  Only changed blocks are copied to S3 on subsequent snapshots

## RAID, Volumes and Snapshots
1. RAID 0 - Striped, no redundancy, 
  - **when you need a greater level of IOPS performance for a file system than you can provision on a single Amazon EBS volume**
  - **when I/O performance is more important than fault tolerance**
  - Example: Two 500 GB Amazon EBS volumes with 4,000 provisioned IOPS each will create a 1000 GB RAID 0 array with an available bandwidth of 8,000 IOPS and 640 MB/s of throughput or a 500 GB RAID 1 array with an available bandwidth of 4,000 IOPS and 320 MB/s of throughput.
1. RAID 1 - Mirrored, redundancy
  - When fault tolerance is more important than I/O performance
1. RAID 5, 6 are NOT recommended by AWS because they are parity writes

## Exam Tips:
  1. By default, the root volume cannot be encrypted when creating an EC2 instance via the console.
  1. The root volume can be encrypted via 3rd party tool (e.g. Bit Locker), or can be done when creating 
  an AMI via console or API.
  1. Additional volumes can be encrypted on creation via the console.
  1. Termination Protection is turned off by default.  You must turn it on explicitly.
  1. On an EBS-backed EC2 instance, the default action is to delete the root EBS volume on instance termination.
  1. IAM Roles can only be assigned to EC2 instances when the instance is created.  **You cannot assign an IAM Role 
  to an EC2 instance *after* the instance is already provisioned (created)**. 
  1. Permissions of an IAM role **can be modified** after the IAM role is attached to an instance 
  and the permission changes will apply immediately
  1. IAM Roles are universal to the account.  They can be used within any region.

## Security Groups
1. Security groups are a virtual firewall that determine inbound and outbound TCP/IP traffic rules
1. One EC2 instance can have multiple security groups
1. All inbound traffic is blocked by default
1. All outbound traffic is allowed by default
1. **Exam Tip:** Changes to security group rules take effect immediately
1. **Exam Tip:** Security group rules are **stateful**. If you create an inbound rule for ssh, http, https then corresponding 
outbound rules for those same ports/protocols are also created *even if not present or visible in the conosle*
1. SG rules cannot deny traffic, only allow traffic.  Denying specific traffic, IPs, ports, protocols is done via 
*Network Access Control Lists* (VPC)
1. NACLs are stateless (inbound and outbound rules must be explicitly specified, no magic)

## Create AMI
1. An AMI is a template for the root volume of an EC2 instance
1. Snapshot of a root device volume, stored in S3
1. **Exam Tip:** AMIs are regional.  They can only be launched from the region in which they are stored
1. AMIs can be copied to other regions using the console, AWS command line or EC2 API

## EBS Root Volumes vs Instance
1. Instance store - ephemeral storage
  - **EC2 instances using instance store root volumes cannot be stopped.  Only rebooted or terminated.**
  - If the underlying host fails, you will lose your data
  - Can provision more slowly than EBS
  - You can only add 1 additional instance store volume
  - By default, both Instance store or EBS root volumes will be deleted on instance termination but with EBS backed volumes, you can tell AWS to keep the root volume.  Not with Instance Store root volumes.
  
## Elastic Load Balancers (ELB)
1. Application Load Balancers (HTTP or HTTPS)
  * Load balance at Layer 7, application layer, application aware
  * **AWS preferred load balance method for HTTP/HTTPS**
  * Route requests to a specific web server based on request type
1. Classic Load Balancers (HTTP/HTTPS or TCP)
  * Layer 4 load balancer 
  * Transport layer (TCP/SSL) or the application layer (HTTP/HTTPS)
  * Also does HTTP/HTTPS based load balancing
1. Network Load Balancer (TCP)
1. Use X-Forwarded-For header to send the originating IP address to the web server
1. 504 Gateway timeout error.   The web application is timing out.  Troubleshoot the web server or database.
1. Instances monitored by ELB are either InService or OutOfService
1. Depends on healthy threshold or unhealthy threshold and how many times the check has passed or failed consecutively
1. Health Checks check instance health by simply calling a specified health check URI on the instances over HTTP/HTTPS
1. ELBs have their own domain name *always*.  You are never given the IP of the ELB, ever.
1. Read the FAQ for ELB Classic.  Classic is featured on the exam!

## CloudWatch
1. Standard Monitoring is turned on by default with duration/interval of 5 minutes to poll EC2 instances
1. Detailed Monitoring every 1 minute
1. Metrics Available:  
  * CPU 
  * Disk 
  * Network 
  * Status
  * **Exam tip:** Memory **not** available
1. Dashboards
1. Alarms
1. Events
1. Logs
  * Install agent on your EC2 instance to collect and report log records
  * At the application level (e.g. Apache http logs)

## Bash Script On Boot

## EC2 Instance Metadata

```bash
curl http://169.254.169.254/latest/meta-data
```
**Exam Tip**  The **exacty IP and URL** must be used in all EC2 instances

## Autoscaling Groups
1. Create Launch Configuration Group
  - Almost exactly like launching an EC2 instance but no instance is actually launched
  - Add bash script on boot to provision with app software 
1. Create AutoScaling Group
  - Specify subnets in different availability zones in case of an AZ outage
  - Specify Elastic Load Balancer to receive traffic
  - Specify Increase Group Size and Decrease Group Size parameters
  
## EC2 Placement Groups
An EC2 Placement Group is a logical grouping of EC2 instances within a single AZ for the purpose of apps requiring 
very high network throughput and very low network latency in between EC2 instances.
  - 10 GBps network
  - **Exam note:** Must be within a single availability zone
  - **Exam note:** Placement Group name must be unique within your AWS account
  - **Exam note:** Only certain types of instances can be launched in a placement group (Compute optimized, GPU, 
  memory optimized, storage optimized)
  - **Exam note:** AWS recommends homogenous instances within a placement group
  - **Exam note:** You cannot move an existing instance into a placement group.  
  - **Exam note:** You can create an AMI from an existing instance and then launch a new instance from that AMI 
  into the placement group
  
