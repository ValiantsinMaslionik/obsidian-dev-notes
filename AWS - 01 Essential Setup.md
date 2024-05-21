#aws

---


# Root account

- **Root User** - you should use *User Name* and *Password* to login
- **IAM User** - you should use *User Name*, *Password*, *Account ID/alias* or *Special LInk* to login

Typical usage scenarios:

- First IAM user
- Root password
- Support plans
- Account closure

**Should be protected with a hardware MFA key!**

How to enable MFA:
```
User name -> Security Credentials -> Multifactor authentication : Enable MFA
```

# How to create an IAM User Group

Prerequisites:

```
User name -> Account -> IAM User and Role Access to Billing Information : Activate IAM Access
```

```
> IAM | Access Management -> User groups : Create Group > admins
Attach Permission Policies -> Search box > AdministratorAccess : check -> Create Group	
```

Each resource in AWS has its unique ARN (Amazon Resource Name). 
ARN contains account ID and resource name.
**ARN is used to link different AWS services/resources to each other.**

# How to create an IAM User and Access Key

```
> IAM | Users
	- : Add User > Administrator : Password - AWS Management Console access | Assign user to a group
	- : Add User > SuperDev : Access Key - Programmatic access | Assign user to a group
```

Download CSV with a password and access key.
There is no way to restore passwords!

# Break down the bill in AWS

```
Console Home -> Go to AWS Cost Management | Cost Explorer
or
> Billing and Cost Management 
```

Configure billing preferences:

```
> Billing and Cost Management | Billing preferences
		- Receive PDF invoices by email
		- Receive Free Tier usage alerts
		- Receive billing alerts
	: Save Preferences
```

Create a budget to control costs:

```
> Billing and Cost Management | Budgets : Create budget
```

# Quiz

Q: You recently started a few new services, and you would like to see what effect these new services will have on your upcoming bill. What service would you use?
A: Cost Explorer

Q: Your organization uses Microsoft Active Directory to provide access controls to all of your employees. What service will let your employees use their same logins with AWS?
A: IAM Identity Center

Q: What is the best way to manage multiple AWS root accounts?
A: AWS Organizations

Q: What is the best way to share my AWS account with my team?
A: Create an IAM user for each team member.

Q: What is the most secure use case for your personal AWS access key?
A: Configure the AWS command line tools with your access key on your secured workstation.

Q: What is something only the AWS root user can do?
A: Delete the AWS account.

Q: Which security principle grants users only the rights they need to perform their daily tasks?
A: least-privilege permissions

Q: Will you be charged for using AWS for the first time?
A: It depends. Some services offer a limited amount of free usage under the free tier.

Q: How can you control costs for your entire AWS account or for individual projects?
A: AWS Budgets