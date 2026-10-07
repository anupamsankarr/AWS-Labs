# Lab 1 — Introduction to AWS IAM

## Overview

This lab explores **AWS Identity and Access Management (IAM)** and demonstrates how identities, groups, and policies can be used to control access to AWS resources.

The lab uses a simulated business scenario in which users are assigned different responsibilities and receive permissions through IAM groups.

## Objectives

* Explore IAM users and groups
* Analyze managed and inline IAM policies
* Understand how permissions are inherited through group membership
* Apply the principle of least privilege
* Test permissions from different IAM identities
* Observe the difference between read-only and administrative access
* Understand how IAM policies control access to AWS services
* Verify authorization boundaries through permitted and denied actions

## Scenario

A company uses Amazon EC2 and Amazon S3 and needs to assign AWS permissions according to employee responsibilities.

| User     | Role              | IAM Group     | Access                   |
| -------- | ----------------- | ------------- | ------------------------ |
| `user-1` | S3 Support        | `S3-Support`  | Read-only S3             |
| `user-2` | EC2 Support       | `EC2-Support` | Read-only EC2            |
| `user-3` | EC2 Administrator | `EC2-Admin`   | View, Start and Stop EC2 |

The objective is to implement these access requirements using IAM group membership and policies.

---

## IAM Architecture

The lab uses three IAM groups:

### S3-Support

Attached policy:

`AmazonS3ReadOnlyAccess`

Provides read-only access to Amazon S3 resources.

### EC2-Support

Attached policy:

`AmazonEC2ReadOnlyAccess`

Provides read-only access to Amazon EC2 and related monitoring information.

### EC2-Admin

Uses an inline policy allowing:

* `ec2:Describe*`
* `ec2:StartInstances`
* `ec2:StopInstances`

This provides the user with the ability to view EC2 resources and start or stop instances.

---

## Task 1 — Explore IAM Users and Groups

The pre-created IAM environment contains:

* `user-1`
* `user-2`
* `user-3`

Initially, the users do not have the required group-based permissions.

The following groups are available:

* `S3-Support`
* `EC2-Support`
* `EC2-Admin`

The policies attached to these groups determine the actions that members can perform.

---

## Task 2 — Assign Users to Groups

Users are assigned to groups according to their job responsibilities.

### `user-1`

Added to:

`S3-Support`

Result:

```text
user-1
   └── S3-Support
          └── AmazonS3ReadOnlyAccess
```

The user inherits read-only S3 permissions from the group.

### `user-2`

Added to:

`EC2-Support`

Result:

```text
user-2
   └── EC2-Support
          └── AmazonEC2ReadOnlyAccess
```

The user inherits read-only EC2 permissions.

### `user-3`

Added to:

`EC2-Admin`

Result:

```text
user-3
   └── EC2-Admin
          └── EC2 administrative policy
```

The user receives permissions to view, start, and stop EC2 instances.

---

## Task 3 — Permission Testing

The permissions were tested by authenticating as each IAM user and attempting actions against different AWS services.

### Test 1 — S3 Support

**Identity:** `user-1`

Expected behavior:

| Action               | Result  |
| -------------------- | ------- |
| List S3 buckets      | Allowed |
| View S3 objects      | Allowed |
| Access EC2 resources | Denied  |

This demonstrates that `user-1` has access based on the S3-specific permissions inherited from the `S3-Support` group.

---

### Test 2 — EC2 Support

**Identity:** `user-2`

Expected behavior:

| Action             | Result  |
| ------------------ | ------- |
| View EC2 instances | Allowed |
| Stop EC2 instance  | Denied  |
| Access S3 buckets  | Denied  |

Although `user-2` can view EC2 resources, the read-only policy does not grant permissions such as:

```text
ec2:StopInstances
```

Attempting to stop the instance therefore results in an authorization failure.

This demonstrates the distinction between **read permissions** and **write/control permissions**.

---

### Test 3 — EC2 Administrator

**Identity:** `user-3`

Expected behavior:

| Action             | Result      |
| ------------------ | ----------- |
| View EC2 instances | Allowed     |
| Start EC2 instance | Allowed     |
| Stop EC2 instance  | Allowed     |
| Access S3          | Not granted |

The EC2 administrator can perform the actions explicitly allowed by the attached policy.

---

## IAM Policy Structure

IAM policies are JSON-based authorization documents.

A policy statement generally contains elements such as:

```json
{
  "Effect": "Allow",
  "Action": [
    "ec2:DescribeInstances",
    "ec2:StartInstances",
    "ec2:StopInstances"
  ],
  "Resource": "*"
}
```

### `Effect`

Determines whether the statement allows or denies an action.

Possible values include:

```text
Allow
Deny
```

### `Action`

Defines the AWS API operations covered by the policy.

Examples:

```text
ec2:DescribeInstances
ec2:StartInstances
ec2:StopInstances
```

### `Resource`

Defines which AWS resources the statement applies to.

A value of:

```text
"*"
```

represents all applicable resources.

In production environments, resource-level permissions should be restricted where supported rather than unnecessarily using `"*"`.

---

## Managed Policy vs Inline Policy

### Managed Policy

A managed policy is a reusable IAM policy that can be attached to multiple identities.

Example:

```text
AmazonEC2ReadOnlyAccess
```

If the policy is attached to multiple groups or users, changes to that managed policy can affect all identities using it.

### Inline Policy

An inline policy is directly embedded into a specific IAM identity, such as a user or group.

In this lab, the `EC2-Admin` group uses an inline policy to provide the required EC2 permissions.

Inline policies can be useful for tightly scoped, identity-specific permissions, although reusable managed policies are generally preferred when the same permission set needs to be applied to multiple identities.

---

## Security Concepts Demonstrated

### Least Privilege

Each user receives permissions appropriate to their role rather than unrestricted AWS access.

For example:

```text
S3 Support       → S3 read access
EC2 Support      → EC2 read access
EC2 Administrator → EC2 operational access
```

### Group-Based Access Control

Permissions are assigned to groups rather than individually attaching the same policies to every user.

This makes access management more scalable and consistent.

### Authorization Boundaries

A user's ability to access an AWS service is determined by the effective permissions associated with their identity.

For example:

```text
user-2 → EC2 ReadOnly
       → ec2:DescribeInstances = ALLOWED
       → ec2:StopInstances     = DENIED
```

### Access Denied as a Security Control

An `AccessDenied` response is not necessarily an error in the environment. It can demonstrate that AWS authorization is correctly preventing an identity from performing an operation outside its assigned permissions.

---

## Results

The final permission model was:

```text
                    IAM
                     │
        ┌────────────┼────────────┐
        │            │            │
     user-1       user-2       user-3
        │            │            │
   S3-Support   EC2-Support   EC2-Admin
        │            │            │
    S3 Read       EC2 Read      EC2 Control
```

The permission tests confirmed that users could perform actions permitted by their assigned policies while unauthorized operations were rejected.

## Key Takeaways

* IAM controls authentication and authorization within AWS.
* Group membership can be used to efficiently manage permissions.
* IAM policies define what actions an identity is allowed or denied to perform.
* Read-only permissions do not automatically provide modification permissions.
* `AccessDenied` responses can be used to verify that authorization controls are functioning as intended.
* Least-privilege access reduces unnecessary permissions and limits the potential impact of compromised credentials.
* IAM permissions should be designed around job responsibilities rather than providing broad administrative access.

## Evidence

Screenshots demonstrating:

* IAM users
* IAM groups
* Attached policies
* Group membership
* Successful S3 access by `user-1`
* Successful EC2 read access by `user-2`
* Denied EC2 modification attempt by `user-2`
* Successful EC2 administrative action by `user-3`

**Note:** Credentials, passwords, account-sensitive information, and other secrets should never be included in the repository.

## Conclusion

This lab provided a practical introduction to AWS IAM by implementing role-based access through IAM groups and policies. Permission testing demonstrated how AWS restricts actions according to an identity's effective permissions and provided a practical example of least-privilege access control.

