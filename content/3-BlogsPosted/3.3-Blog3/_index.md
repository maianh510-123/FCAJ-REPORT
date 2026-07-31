---
title: "Blog 3 - Least Privilege on AWS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---
# Least Privilege on AWS: Granting Only What Is Needed Is an Important Skill

**Content status:** Complete  

When I first worked with AWS, I had a simple habit: if an application failed because of permissions, I granted more permissions.

- Lambda cannot read S3? Add permissions.
- EC2 cannot upload a file? Attach a broader policy.
- A user cannot perform an action? Use administrator access for speed.

This can make testing faster, but the more I learned about AWS, the more dangerous the habit appeared. In cloud environments, permissions do not only decide whether software runs. They also determine the potential damage if credentials are exposed, a service is exploited, or configuration is incorrect.

This is why **Least Privilege** matters.

## What does Least Privilege mean?

Least Privilege means every user, role, or workload receives only the permissions required to complete its task—no more.

It does not mean mechanically granting “as little as possible.” A policy that is too narrow prevents the workload from running. The goal is to understand actual application behavior and then constrain:

- permitted actions;
- accessible resources;
- required prefixes or objects;
- applicable conditions;
- how long permissions are needed.

## A direct QuickBite example

The QuickBite backend on EC2 needs two primary permission groups:

1. upload/read menu images in `quickbite-menu-images-<env>`, mainly under `menu/*`;
2. write container logs to CloudWatch Logs.

The backend does **not** need to:

- administer all S3 resources;
- delete every bucket in the account;
- read unrelated buckets;
- create IAM users;
- use a fixed access key stored in `.env`.

### An overly broad policy

The following policy may make code “work immediately,” but its scope is excessive:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

If EC2 is compromised, an attacker can interact with every S3 resource allowed by that policy.

### A narrower QuickBite policy

The following example limits the backend to the image bucket and prefix:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListMenuImagePrefix",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::quickbite-menu-images-<env>",
      "Condition": {
        "StringLike": {
          "s3:prefix": [
            "menu/*"
          ]
        }
      }
    },
    {
      "Sid": "ReadWriteMenuImages",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::quickbite-menu-images-<env>/menu/*"
    },
    {
      "Sid": "WriteQuickBiteLogs",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogStream",
        "logs:DescribeLogStreams",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:ap-southeast-1:<account-id>:log-group:quickbite/backend:*"
    }
  ]
}
```

If the code does not directly read images, `s3:GetObject` can be reconsidered. If deleting old images becomes a real requirement, `s3:DeleteObject` should be added only after that use case is confirmed.

## Why use an IAM role for EC2?

QuickBite uses an **EC2 instance profile/IAM role** instead of a long-term access key.

With a role attached to EC2:

- the AWS SDK can automatically receive temporary credentials;
- `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` do not need to exist in `.env`;
- credentials rotate;
- removing the role or policy revokes access without changing a source-code key.

This is more appropriate than creating an IAM user simply for an EC2 application.

## Security Groups are also Least Privilege

Least Privilege is not limited to IAM.

For QuickBite:

- SSH port 22 should be open only from the administrator IP;
- RDS should not be public;
- RDS port 5432 should accept traffic only from `quickbite-ec2-sg`;
- PostgreSQL should not be open to `0.0.0.0/0`;
- the frontend bucket should use CloudFront OAC instead of direct public access;
- CORS should allow only the actual frontend domain.

Each layer reduces the access scope.

## Do not hard-code secrets

A good policy is not enough if secrets are committed to Git.

Useful practices include:

- add `.env` to `.gitignore`;
- commit only `.env.example` with placeholders;
- generate `SECRET_KEY` directly on EC2;
- avoid screenshots containing the RDS password;
- never place the `.pem` key pair in the repository;
- prefer IAM roles and temporary credentials;
- for production, consider Systems Manager Parameter Store or Secrets Manager.

In the current demo, the database password and application secret are still supplied through `.env` on EC2. The report states this limitation clearly; Secrets Manager is a future improvement, not a deployed component.

## How IAM Access Analyzer helps

AWS IAM Access Analyzer can help:

- identify public or cross-account resource access;
- review external access;
- validate policies;
- support policy generation from activity in some workflows;
- identify permissions that may no longer be required.

However, the tool does not replace understanding the application. A developer still needs to know which actions `/uploads/image` uses and which permissions Docker `awslogs` requires.

## A practical permission-reduction workflow

The following process is safer than attaching AdministratorAccess:

1. list the workload's actual actions;
2. scope resource ARNs;
3. use an IAM role;
4. run the main feature;
5. inspect `AccessDenied` errors;
6. add only the required action;
7. run policy validation/Access Analyzer;
8. review after feature changes;
9. remove policies, roles, or permissions that are no longer used.

This may take slightly longer at first, but it produces a safer and more explainable architecture.

## Lesson learned

AWS security is not only about “not leaking keys.” Even when credentials remain private, an overly broad policy increases the blast radius.

Least Privilege forces me to understand the workload:

- which bucket does EC2 truly need?
- does the backend need to read, write, or delete objects?
- where should RDS accept traffic from?
- which log group needs write access?
- which permissions exist only for testing and should be removed?

For QuickBite, the EC2 IAM role, S3 restriction to `menu/*`, CloudWatch log-group scope, and RDS access only from the EC2 Security Group are direct and verifiable applications.

Least Privilege does not make a system perfectly secure, but it reduces the scope of damage when an incident occurs. In cloud design, reducing blast radius is essential.

## Evidence
![alt text](/images/Post_3.png)

## References

- [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [What is IAM Access Analyzer?](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html)
- [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html)