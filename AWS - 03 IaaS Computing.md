#aws

---

## Learn how to create an EC2 instance

Search > EC2 : Enter -> EC2 Dashboard -> Launch Instance : Launch Instance

Select AMZON AMI or search in the market.

## What is the best EC2 instance type?

[Instance Types](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-types.html)
[Compare Instance Types](https://eu-central-1.console.aws.amazon.com/ec2/home?region=eu-central-1#LaunchInstances:)

[Burstable Performance Instances \(T2\)](https://aws.amazon.com/ec2/instance-types/#Burstable_Performance_Instances)
![[zIMG-AWS-EC2-CpuBurst.png]]

[Reserved Instances](https://aws.amazon.com/ec2/pricing/reserved-instances/buyer/)

## How to create a key pair

Key pair is required to set up SSH connection to a EC2 instance.

EC2 -> Network & Security -> Key Pairs
or
EC2 Create instance wizard -> Create new key pair

## Set up a web server (Ubuntu)

* Connect to EC2 instance
*  `sudo apt update`
* `sudo apt upgrade`
* restart EC2 instance
* `sudo apt install apache2`

> ![[zICO - Warning - 16.png]] Even if an EC2 instance is stopped you will be charged for storage space occupied by hard drive and snapshots!

> ![[zICO - Warning - 16.png]] Start/Stop is not the same as Reboot! After Stop/Start the EC2 instance can be moved to different AZ an it's public IP will be changed.