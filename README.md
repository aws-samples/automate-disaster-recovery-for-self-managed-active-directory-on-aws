## Automate disaster recovery for your self-managed Active Directory on AWS

This repo hosts the AWS Cloudformation template for AWS blog post: [Automate disaster recovery for your self-managed Active Directory on AWS](https://aws.amazon.com/blogs/modernizing-with-aws/automate-disaster-recovery-for-your-self-managed-active-directory-on-aws/) published on the Microsoft Workloads on AWS blog channel. In this blog post, I show how you can leverage Amazon Web Services (AWS) for disaster recovery (DR) for your self-managed Microsoft Active Directory (AD).


## Solution overview
Taking regular backups is critical for implementing a DR plan for AD. This proposed solution leverages a combination of encrypted [Amazon Elastic Block Store (EBS)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html) volumes and the [AWS Backup](https://aws.amazon.com/backup/) service to provide robust protection for your AD backup data. You start by backing up system state data (which contains AD data) to a secondary encrypted Amazon EBS volume. You then backup the Amazon EBS volume using AWS Backup, as shown in Figure 1, to further protect the data from incidents like accidental deletion. And, in the event of a disaster, or as part of your regular DR drills, you restore the Amazon EBS volume from AWS Backup and then restore AD from the Amazon EBS volume. I provide a sample AWS Cloudformation template to automate the process. To use this solution for your on-premises AD, you must extend your on-premises AD to AWS, as shown in Figure 1.

Refer to this [blog post](https://aws.amazon.com/blogs/security/securely-extend-and-access-on-premises-active-directory-domain-controllers-in-aws/) for steps on extending your self-managed AD to AWS.


![Solution Architecture](https://github.com/aws-samples/automate-disaster-recovery-for-self-managed-active-directory-on-aws/blob/main/Images/Figure1%20-%20Solution-Architecture.png)

## Prerequisites
The instructions in this post assume that you understand how to [create Amazon EC2 instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html) and how to [use Remote Desktop Protocol (RDP) to log in to the instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html). For customers running AD on premises, these instructions also assume you have extended your on-premises AD to Amazon EC2. 

## High Level Steps
Here are the high-level steps to implement the solution.

**PROTECT** - The solution starts with steps to protect your AD data:

1.  Extend your on-premises AD to AWS by deploying at least 2 domain controllers in different availability zones on AWS, as shown in Figure 1. This step is not required if your self-managed AD is hosted on Amazon EC2
2. Create and attach an encrypted [Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html) volume to each domain controller on Amazon EC2.
3. Run daily system state backups to the attached secondary encrypted volumes.
4. Configure [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) to take daily snapshots of the secondary Amazon EBS volumes.
5. Configure AWS Backup to take regular AMI backup of the domain controller instance. Set up the frequency to align with your existing Windows patching maintenance window.

\
**RESTORE** - The solution then provides steps required for self-managed AD disaster recovery dry runs on AWS.
1. Build out an isolated environment: new [Amazon Virtual Private Cloud (Amazon VPC)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) VPC, subnets, [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), etc.
2. Use the domain controller AMI backup to launch an Amazon EC2 instance in the isolated environment.
3. Restore the Amazon EBS volume containing system state backup from AWS Backup.
4. Attach the backup volume to the Amazon EC2 instance.
5. Restore the system state backup from the backup volume.
6. Perform AD Forest recovery steps.

**For detailed implementation steps for this solution, refer to [the blog](https://aws.amazon.com/blogs/modernizing-with-aws/automate-disaster-recovery-for-your-self-managed-active-directory-on-aws/).**

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License


This library is licensed under the MIT-0 License. See the LICENSE file.

