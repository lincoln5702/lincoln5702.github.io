
## Overview

**AWS Config** is a fully managed service that helps you assess, audit, and evaluate the configurations of your AWS resources over time. AWS Config allows you to track configuration changes, assess compliance with internal policies, and gain insights into your AWS environment. It plays a crucial role in helping organizations ensure that their AWS resources remain properly configured and compliant with security and regulatory standards.

---

## Key Features of AWS Config

1. **Auditing and Recording Compliance**:
   - AWS Config continuously monitors the configuration of your AWS resources, providing a historical record of configuration changes.
   - It answers crucial questions like:
     - *Is there unrestricted SSH access to my security groups?*
     - *Do my buckets have any public access?*
     - *How has my Application Load Balancer (ALB) configuration changed over time?*

   **Example**: 
   - If you are worried about SSH access to your security groups, AWS Config can track the configuration of the security group over time and notify you when an unrestricted rule is added.

2. **Alerting (SNS Notifications)**:
   - You can set up **SNS (Simple Notification Service)** notifications to alert you whenever a resource configuration goes out of compliance or when a change is detected.
   - Notifications can be sent via email, SMS, or other channels supported by SNS.

3. **Per-Region Service**:
   - AWS Config operates at the region level. Each region has its own configuration history.
   - However, you can aggregate AWS Config data across multiple regions and accounts for a centralized view.

4. **Data Storage in Amazon S3**:
   - Configuration history can be stored in **Amazon S3** for long-term storage and further analysis.
   - You can use **AWS Athena** to query this data for deeper insights.

---

## Config Rules

**AWS Config Rules** allow you to create policies for evaluating the compliance of your AWS resources. Config Rules are evaluated on configuration changes or at regular time intervals.

### AWS Managed Config Rules
AWS provides a collection of **over 75 pre-built (managed)** rules that can be applied to your AWS resources without needing to write custom code.

**Examples**:
1. **EBS Volume Type**: Ensure that all EBS volumes are of type `gp2`.
2. **EC2 Instance Type**: Ensure that only instances of type `t2.micro` are used in a particular region.

### Custom Config Rules
If the AWS-managed rules do not meet your specific needs, you can create **custom Config Rules** using **AWS Lambda** functions.

**Example**:
- You might want to check that all EC2 instances are tagged with a `Cost Center` tag. You would write a custom Lambda function that evaluates the presence of the `Cost Center` tag and set it as a rule.

### Evaluation Triggers
- **On Configuration Changes**: Whenever a resource's configuration changes, AWS Config evaluates it against the set rules.
- **At Regular Intervals**: You can set AWS Config to check resources periodically, like every hour.

### Pricing
- **Configuration Item Recording**: $0.003 per configuration item per region.
- **Config Rule Evaluations**: $0.001 per evaluation per region.

**Note**: There is no free tier for AWS Config.

---

## Config Resource

AWS Config helps you track the compliance and configuration history of your resources over time. You can view:

1. **Compliance History**: A record of whether your resources were compliant with the rule over time.
2. **Configuration History**: A snapshot of the configuration of a resource at different points in time.
3. **CloudTrail API Calls**: You can trace changes made to a resource and track who made the change and when.

---

## Config Rules - Remediations

**Automated Remediation**: When a resource is found to be non-compliant, AWS Config can trigger **SSM (Systems Manager) Automation** documents to automatically fix the non-compliance.

### Example:
- **IAM Access Key Expiration**: If an IAM access key is not used for 90 days, AWS Config can automatically deactivate the key using an SSM automation document.

**Custom Remediation**: You can write custom Automation Documents that trigger Lambda functions to remediate issues.

**Remediation Retries**: If a resource remains non-compliant after the first remediation attempt, AWS Config can retry the remediation process a certain number of times.

---

## Config Rules - Notifications

You can use **Amazon EventBridge** to trigger notifications when a resource is non-compliant.

1. **EventBridge and SNS**: 
   - Use **SNS topics** to send notifications. 
   - These notifications can trigger actions like sending an email or invoking a Lambda function.

2. **Filter Events**: EventBridge allows you to filter AWS Config events, so you can focus on only the most important events.

**Example**: 
- If an EC2 instance is launched without a `Backup` tag, AWS Config can trigger a notification that informs your DevOps team to correct the issue.

---

## CloudWatch vs CloudTrail vs Config

### Differences:
1. **CloudWatch**: 
   - Focuses on **performance monitoring**, collecting metrics like CPU, memory, disk usage, and network traffic.
   - Provides event alerting and log aggregation.
  
2. **CloudTrail**: 
   - Records **API calls** made by users, services, or resources within your AWS account.
   - Tracks user actions for security auditing and compliance purposes.

3. **AWS Config**: 
   - Tracks **configuration changes** and ensures resources comply with policies.
   - Provides a historical timeline of changes and compliance status.

### Example: Elastic Load Balancer (ELB)
1. **CloudWatch**: Monitors incoming connections, error codes, and other performance metrics. 
   - *Example*: You can create a dashboard to visualize error codes as a percentage over time.
   
2. **Config**: Monitors the configuration of the ELB, such as security group rules and SSL certificate assignment.
   - *Example*: Ensures that your ELB always has an SSL certificate assigned.
   
3. **CloudTrail**: Tracks **who** made changes to the ELB through API calls.
   - *Example*: CloudTrail records the API calls to update the ELB configuration and who made them.

---

## Conclusion

AWS Config plays a crucial role in helping you maintain the security, compliance, and operational integrity of your AWS environment. By automating compliance checks, tracking configuration changes, and integrating with services like CloudTrail and CloudWatch, AWS Config ensures that your resources stay in line with organizational policies.

---

**Real-World Analogy**:

Think of **AWS Config** as a **compliance officer** in a company who keeps track of all the configurations (settings) of the organization's assets (AWS resources). This officer ensures that every asset (e.g., EC2 instances, security groups, etc.) is in line with company policies. If a new policy is implemented (e.g., SSH access is restricted), the compliance officer (AWS Config) checks if all assets are compliant, and if not, triggers an automatic action (remediation) or sends an alert to the responsible team.

