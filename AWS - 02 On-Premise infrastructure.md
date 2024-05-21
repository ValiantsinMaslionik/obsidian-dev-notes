#aws

---

# On-Premise Infrastructure

- Self-host
- Colocation (_the placement of several entities in a single location_)
- Lease servers (аренда серверов)

**You have most, or all, of the responsibility for keeping the servers up.**

# Birth of the Cloud: EC2 and S3

EC2 - Elastic Compute Cloud - Virtual servers which can be rented.
![[zIMG-AWS-Icons-EC2.png]]

IaaS - You rent virtual infrastructure from a cloud provider and pay for only what you use.

Shared responsibility model
![[zIMG-AWS-SharedResponsibilityModel.png]]

# Where in the world are your AWS services

Single Availability Zone like a single server rack in data center.
Availability Zones are clustered together by Regions.

Region icon
![[zIMG-AWS-Icons-Region.png]]

All Availability Zones in North Virginia are called North Virginia Region or USEast1:
- us-east-1a
- us-east-1b
- us-east-1c
...

Multi AZ infrastructure
![[zIMG-AWS-MultiAzArchitecture.png]]

Biggest Region with the latest version of services, receives new features first - North Virginia (Ohio).

## Get closer to user using Local Zones

AWS Local Zones are a type of infrastructure deployment that places compute, storage, database, and other select AAWS services close to a large population and industry centers.
![[zIMG-AWS-LocalZones.png]]

![[zIMG-AWS-LocalZonesLocation.png]]

![[zIMG-AWS-LocalZonesNameFormat.png]]

## Quiz

Q: What is the difference between Local Zones and Availability Zones?
A: Local zones extend an availability zone to provide faster services to users located around specific geographic locations.

Q: EC2 Auto Scaling revolutionized server hosting by providing which value add to businesses?
A: Elasticity, the ability to horizontally scale servers up and down to right-size infrastructure based upon current demand.	
	Elastic Compute Cloud (EC2) was one of the first to offer elasticity. It could stretch up to meet current demand, and then automatically shrink back down to save money on unused resources.

Q: What is the difference between an availability zone and a region?
A: A region is a geographical group of several availability zones that can consist of several individual data centers.

Q: In the shared responsibility model, which maintenance task will AWS perform on your EC2 instance?
A: Ensure that the physical server hardware is healthy.

Q: What is not a unique feature of Infrastructure as a Service?
A: Serverless computing.
		The following are unique: no capital expenses, pay-as-you-go pricing, on-demand scalability

Q: When you create a new server instance in AWS, what is happening behind the scenes?
A: AWS is creating a virtual machine (VM), which is a software abstraction that divides up the physical server's resources.

Q: What is an important value add of the cloud?
A: You do not have all of the capital expenses (CapEx) associated with on-premise hardware.