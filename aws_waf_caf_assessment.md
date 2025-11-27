## Task 1 – Review the Existing Architecture

### 1. Components of the workload (current AWS deployment after lift-and-shift)

| AWS Resource                  | Details                                                                 |
|-------------------------------|-------------------------------------------------------------------------|
| VPC                           | Single VPC with public and private subnets (probably only 1 or 2 AZs)  |
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
