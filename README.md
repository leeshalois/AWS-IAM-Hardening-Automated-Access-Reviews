# 🕵🏻 AWS-IAM-Hardening-Automated-Access-Reviews
Hardened AWS IAM for a fast-growing startup by implementing least privilege, MFA, and automated access reviews. Built scripts, dashboards, and audit-ready reports to secure cloud access.

## 🛡️ Role
Cloud Security Engineer

## 🛠 Tools Used
- **AWS IAM / AWS SSO** – Centralized identity management and role-based access control
- **AWS CLI / Boto3 (Python)** – Scripting and automation of access reviews
- **AWS CloudTrail** – Monitoring user and role activity
- **AWS IAM Access Analyzer** – Identifying overly permissive policies
- **AWS CloudWatch / QuickSight** – Dashboards for reporting and visibility
- **SNS / Slack Webhooks** – Notifications for access review approvals

## 🌐 AWS IAM Audit & Access Review Table
Audit-focused AWS IAM table showing users, roles, policies, MFA, last activity, and risk levels. Highlights least privilege enforcement, account hygiene, and automated access review practices.

| #  | Team / Function     | Role / User            | IAM Policy / Permission Level     | Privilege Level | MFA Status       | Last Used            | Access Key Age | Risk Level | Findings / Issues      | Remediation Status           |
| -- | ------------------- | ---------------------- | --------------------------------- | --------------- | ---------------- | -------------------- | -------------- | ---------- | ---------------------- | ---------------------------- |
| 1  | Engineering         | DeveloperStandard      | EC2 & S3 ReadOnly                 | Low             | Enforced         | Active               | N/A            | Low        | No issues              | Compliant                    |
| 2  | Engineering         | Dev-ToolingAccess      | CloudWatch ReadOnly, SSM Describe | Low             | Enforced         | 7 days ago           | N/A            | Low        | No issues              | Compliant                    |
| 3  | Engineering         | MobileDevAccess        | Amplify + S3 RW                   | Medium          | Required         | 14 days ago          | N/A            | Medium     | S3 overly broad        | Restricting ARNs             |
| 4  | DevOps              | DevOpsAdmin-JIT        | Lambda/EC2 Admin, IAM Scoped      | High            | FIDO             | 2 days ago           | N/A            | Medium     | Privileged role        | JIT mitigates risk           |
| 5  | DevOps              | PipelineDeployRole     | CFN Deploy, S3 RW                 | Medium          | N/A              | Active               | N/A            | Medium     | No permission boundary | Boundary applied             |
| 6  | DevOps              | ECRAdminRole           | ECR FullAccess                    | Medium          | N/A              | 5 days ago           | N/A            | Medium     | Full ECR admin         | Scoped rewrite planned       |
| 7  | Security            | SecurityAdmin          | GuardDuty + CloudTrail Admin      | High            | Required         | Active               | N/A            | Low        | No issues              | Compliant                    |
| 8  | Security            | ThreatHuntingRole      | Athena Query, S3 Logs Read        | Medium          | Required         | 1 day ago            | N/A            | Low        | No issues              | Compliant                    |
| 9  | Security            | SOC-ReadOnly           | ReadOnlyAccess                    | Low             | Required         | Active               | N/A            | Low        | No issues              | Compliant                    |
| 10 | Security Automation | AccessReviewLambdaRole | IAM ReadOnly, Orgs Read           | Low             | N/A              | Daily                | N/A            | Low        | No issues              | Compliant                    |
| 11 | Management          | ReadOnlyAudit          | AWS ReadOnlyAccess                | Low             | Required         | 40+ days inactive    | N/A            | Low        | Stale role             | Scheduled for removal        |
| 12 | Finance             | BillingAdmin           | Billing View + Budgets            | Medium          | Required         | 12 days ago          | N/A            | Medium     | No issues              | Compliant                    |
| 13 | Data Team           | DataEngineerAccess     | Glue Admin, S3 Data Lake RW       | Medium          | Required         | Active               | N/A            | Medium     | Broad S3 access        | Restricting to buckets       |
| 14 | Data Team           | AnalyticsQueryRole     | Athena Query Only                 | Low             | Required         | Active               | N/A            | Low        | No issues              | Compliant                    |
| 15 | AI/ML Team          | MLTrainingRole         | SagemakerFullAccess               | High            | Required         | Active               | N/A            | Medium     | Overprivileged         | Replacing with custom policy |
| 16 | AI/ML Team          | MLOpsJobExecutionRole  | S3 RW, Sagemaker Invoke           | Medium          | N/A              | 3 days ago           | N/A            | Low        | No issues              | Compliant                    |
| 17 | Networking          | NetOpsAdmin            | VPC, Route53, ELB Admin           | High            | Required         | 1 day ago            | N/A            | Medium     | High privilege         | Under review                 |
| 18 | Networking          | DNS-UpdateRole         | Route53 ChangeResourceRecordSets  | Medium          | Required         | 7 days ago           | N/A            | Low        | No issues              | Compliant                    |
| 19 | Infra               | InfraMonitoringRole    | CloudWatch Full                   | Medium          | N/A              | Active               | N/A            | Low        | No issues              | Compliant                    |
| 20 | Automation          | CICDServiceRole        | CFN Deploy, S3, STS Assume        | Medium          | N/A              | Active               | N/A            | Medium     | Needed boundary        | Boundary added               |
| 21 | Automation          | GitHubActionsRole      | AssumeRole limited, S3 RW         | Medium          | N/A              | 6 days ago           | N/A            | Medium     | S3 RW too broad        | Scoped to prefix             |
| 22 | Support             | SupportReadOnly        | ViewOnlyAccess                    | Low             | Required         | Active               | N/A            | Low        | No issues              | Compliant                    |
| 23 | Support             | SupportEscalationRole  | Limited EC2/RDS Describe          | Low             | Required         | 2 days ago           | N/A            | Low        | No issues              | Compliant                    |
| 24 | Contractors         | ContractorDev          | DeveloperStandard                 | Medium          | Required         | Inactive 45 days     | N/A            | Medium     | Past contract period   | Access revoked               |
| 25 | Contractors         | ContractorData         | DataEngineerAccess                | Medium          | Required         | Inactive 60 days     | N/A            | Medium     | Stale access           | Removed                      |
| 26 | Legacy              | IAM-User-001           | Custom Inline Policies            | Medium          | Missing          | 120+ days inactive   | Key 400+ days  | High       | Non-compliant          | Scheduled disablement        |
| 27 | Legacy              | IAM-User-002           | S3 + EC2                        | High            | Missing          | Unknown              | Key 300+ days  | High       | Severe overprivilege   | Disabled                     |
| 28 | Orphaned            | OldProjectRoleA        | S3 + EC2 custom                   | Medium          | N/A              | No activity 90+ days | N/A            | Medium     | Orphaned               | Pending deletion             |
| 29 | Orphaned            | AppMigrationRole       | DynamoDB RW + Lambda              | Medium          | N/A              | No activity          | N/A            | Medium     | No owner               | Owner assigned               |
| 30 | Break-Glass         | EmergencyAdmin         | AdministratorAccess               | Critical        | FIDO + isolation | Never used           | N/A            | High       | Sensitive role         | Controlled, tested quarterly |

### 🚧 Project in progress 🚧
