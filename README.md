# AWS Well-Architected & Cloud Adoption Framework Lab  

## Repository Contents
- `aws_waf_caf_assessment.md`  Full lab report with all 4 tasks, tables, explanations, and reflection  
- `images`  Improved multi-AZ architecture image created in Lucidchart  
- `README.md`  This file explains my approach.

## My Approach – Step by Step

1.Task 1 – Existing Architecture  
I imagined the application had already been migrated to AWS using the simple lift-and-shift method.  
So the current (bad) setup was:  
- 1–2 EC2 instances (public subnet or with Elastic IP)  
- Single-AZ RDS  
- Open security groups (port 22 and 80/443 from 0.0.0.0/0)  
- No load balancer, no Auto Scaling, no CloudFront  
I listed all AWS components and wrote down the real risks (single-AZ, open SSH, no scaling, etc.).

2. Task 2 – Well-Architected Framework Table  
For each of the 5 pillars I wrote one real strength and one real weakness of the current bad setup, then gave a simple fix using services I know from the Cloud Practitioner course.

3. Task 3 – Cloud Adoption Framework (CAF)  
I wrote paragraphs for every perspective (Business, People, Governance, Platform, Security, Operations) explaining where the company is today and the most important next steps. I kept it simple and only used services and ideas that are in the CCP course.

4. Task 4 – Improved Architecture  
I redesigned everything to follow modern AWS best practices:  
- Application Load Balancer in public subnets  
- EC2 web servers in public subnets subnets (no public IP, no SSH)  
- Multi-AZ Auto Scaling Group  
- Multi-AZ RDS  
- Static files served from S3 + CloudFront (I mentioned it in the text even if I didn’t draw it yet)  
- Systems Manager Session Manager instead of SSH  
I then explained exactly how this new design satisfies all 5 WAF pillars and supports CAF.

## Conclusion
Doing this lab after getting my Cloud Practitioner certification made everything finally click. I now understand why AWS keeps repeating “build it secure and reliable from day one” instead of fixing it later.

This repository is ready for submission.

Thank you!