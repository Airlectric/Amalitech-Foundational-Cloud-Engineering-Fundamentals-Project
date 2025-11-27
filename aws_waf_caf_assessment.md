## Task 1 – Review the Existing Architecture

### 1. Components of the workload (current AWS deployment after lift-and-shift)

| AWS Resource                  | Details                                                                 |
|-------------------------------|-------------------------------------------------------------------------|
| VPC                           | Single VPC with public and private subnets (probably only 1 or 2 AZs)   |
| EC2 instances                 | 1–2 t3.medium or m5.large instances running the web or frontend /application tier   |
| Amazon RDS                    | Single-AZ MySQL or PostgreSQL instance in a private subnet             |
| Security Groups               | Web SG: ports 80, 443, 22 open from 0.0.0.0/0|
| Internet Gateway              | Attached to the VPC so EC2 instances are reachable from the internet  |
| Elastic IP or Public DNS      | Directly pointing to the EC2 instance (no load balancer)            |
| EBS volumes        | Root and data volumes attached to EC2                                  |
| RDS automated backups | Enabled with the default retention period             |



### 2. Potential risks or weaknesses

- **Single-AZ deployment:** EC2s as well as RDS are provisioned in a single Availability Zone, loss of either of the two will cause the entire application to fail.
- **No load balancer:** Traffic is sent to one or two EC2 instances , single point of failure and not health checked.
- **No Auto Scaling:** It is unable to meet sudden traffic spikes and not capable of scaling down to save money.
- **Security groups excessively open:** Port 22 (SSH) and some 80/443 are open to 0.0.0.0/0 (internet as a whole),hence high probability of being hacked.
- **Direct access using SSH keys:** Admins use SSH keys to access the server rather than comply with AWS best practices, less easier to manage and audit.
- **Single-AZ RDS only:** When the database instance or the AZ is not working, the application will fail, and we may lose information.
- **Weak backup strategy:** RDS only default 7 days of automated backups, no manual snapshots, no cross region backups.
- **No Web Security:** Web application prone to SQL injection and other typical web attacks.
- **No CDN:** Customers located outside the area can load pages slowly; all the traffic is redirected to the EC2 instances.
- **Lack of proper monitoring and alarms:** Depending on basic CloudWatch, it is difficult to check whether something is amiss until users report the issue.
- **Fixed 24/7 running instances:** Purchasing compute even at night/weekends when traffic is extremely low.




## Task 2 – Well-Architected Framework Evaluation

| Pillar                     | Observation (Strength) | Improvement Needed         | Recommendation         | AWS Service                                              |
| -------------------------- | -----------------------| ---------------------------| -----------------------| -------------------------------------------------------- |
| **Operational Excellence** | Infrastructure provisioned in cloud enables automation possibility | Lacks monitoring/observability            | Implement centralized monitoring and logs          | **CloudWatch, CloudTrail, AWS Config**                   |
| **Security**               | Workloads moved away from on-prem unsecured network                | Security groups too open + unencrypted DB | Apply least-privilege access, encryption, IAM policies | **VPC security groups, KMS, IAM, Secrets Manager**       |
| **Reliability**            | Backup process can be introduced easily in AWS                     | Single-AZ deployment risks downtime       | Deploy Multi-AZ + automatic backups                    | **RDS Multi-AZ, ASG, ELB**                               |
| **Performance Efficiency** | Scalable cloud infrastructure available                            | Manual scaling could cause overload       | Enable autoscaling and caching                         | **Auto Scaling Group, CloudFront, ElastiCache**          |
| **Cost Optimization**      | Pay-as-you-go model reduces upfront cost                           | On-demand DB/EC2 may be over-provisioned  | Right-size workloads and use savings plans             | **Trusted Advisor, Compute Savings Plans, S3 Lifecycle** |



## Task 3  Implemention of  the AWS Cloud Adoption Framework (CAF).

### Business Perspective

Business-wise the organization seems to be eager to move to AWS to enhance scalability, lower costs incurred in infrastructure, and hasten the product delivery. Nonetheless, the business preparedness has been partially developed since no clear cloud value roadmap or financial model has been developed to quantify the cost savings and improved efficiency. Stakeholders realize that the cloud can be of some good, but KPIs like uptime improvement, time-to-market decrease, and customer satisfaction industry are yet to specify.

To advance towards success, the business should be able to implement a robust sponsorship pattern with executive sponsorship and establish anticipated business results. There should also be a report of a total cost of ownership (TCO) and return on investment (ROI) which will be used to justify migration investments. There is also a need to collaborate with senior leadership on product owners to ensure that the cloud goals are aligned with customer experience, market competitiveness, and growth in revenues. Having the appropriate strategy and measures of success will enable cloud adoption to contribute to long-term business goals.

### People Perspective

The company possesses an IT department currently who worked on premises but has fewer skills with AWS cloud, which is a challenge when migrating to cloud. Team members largely experience managing physical servers and traditional networking as opposed to managed services, automation and cloud-native practices.

The company must invest in high-level upskilling using AWS Academy, certification exams, workshops, and practical cloud labs to be prepared to adopt AWS. The roles and responsibilities might also have to evolve as well since cloud operations are not isolated by development, security and operations teams, but rather are shared. Such factors as communication and change management will also be significant, as most of the staff will not be comfortable with the new systems like infrastructure as code and DevOps pipelines. Setting up a Cloud Center of Excellence (CCoE) and mentorship system will aid the team in standardization of best practice. On the whole, the company is staff-ready following adequate training, job definition, and team building programs.


### Governance Perspective

With the governance perspective, the organization has no standard policies regarding the use of the cloud, which is a threat to cost management, compliance, and security. 

To successfully migrate, the business should have policies to label the resources, budget warning, access regulation, lifecycle policies and compliance audits. The procurement and finance departments ought to be sensitized on variable consumption-based pricing. Enablers of governance also involve the definition of cloud budgets, the implementation of the cost dashboards, and the enforcement of the guardrails with the help of the Organizations, Control Tower, and Trusted Advisor AWS services. It should also be planned that the compliance with all regulatory frameworks, like GDPR, or PCI-based, should be maintained during migration and not after its deployment. Well-Architected reviews periodically can be used to review that workloads remain in accordance with best practices. By implementing the governance controls, the organization will be in a better standpoint to scale the use of cloud without losing its financial, legal, and compliance controls.

### Platform Perspective

In terms of platform, the current AWS configuration is technically sound but still not configured to be highly available, automated, and scaled.

It needs to be more cloud-native, which is founded on Multi-AZ deployments, load balancing, Auto scaling, and Infrastructure as code (IaC). Manual work and risk of failure will be minimized by using managed services when appropriate (RDs Multi-AZ, Elastic Load Balancing, CloudFront, and AWS Backup). CodePipeline, CodeBuild and CodeDeploy deployment pipelines will allow faster and more regular version releases. The platform to ensure better networking security and performance must implement VPC subnet levels (public/ private), NAT gateways, and least-privilege access rules to route tables. After implementing modern AWS architectural patterns, the platform will also be more resilient, scaled, and easily operable.

### Security Perspective

The level of security preparedness is currently low since the majority of security policies focus on the traditional on premises perimeter-based security approach as opposed to cloud-native security.

The organization should introduce the following changes:
- the AWS shared responsibility model and the implementation of least-privilege access using IAM roles and policies as opposed to unprotected SSH access.
- AWS KMS should be used to encrypt data in transit and rest. The secrets should not be stored manually and be administered with the help of Secrets Manager or Parameter Store.
- VPC security groups, NACLs and WAF should be used to strengthen the network perimeter against threats such as SQL injections, DDoS and cross-site scripting.
- CloudTrail, GuardDuty, and Security Hub should also become the priorities in logging and threat visibility. 

The compliance and auditing criteria ought to be specified early enough to ensure that the security is always enforced during the migration. In place with these controls, the organization will be secure by design rather than responding to occurrences as they arise.

### Operations Perspective 

The readiness of the operations is average since the team is still relying on manual maintenance instead of automated cloud operations.

To be able to migrate successfully, the organization needs to move to proactive monitoring, automated patching, backup policies, and scripted infrastructure deployments. CloudWatch should be configured to generate metrics, dashboards and alarms to ensure that problems are identified in time before they impact the customers. The response times should be enhanced by creating incident response plans, runbooks and post incident reviews. The disaster recovery target objectives (RPO and RTO) should be documented and tested with the help of the backup and restore drills. The success of operations is determined by the extent of adopting automation, minimizing manual server management and frequent review of the operational performance. With automated monitoring and patching, and alarms, the environment will become more reliable, with reduced downtime and recovery.

