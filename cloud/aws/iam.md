# IAM

- by default any additional users created on the AWS account are created with no access to any AWS resources
- they have to be explicitly granted

## Best practices
- delete your root access keys
- activate MFA on root account
- create individual IAM users
- use groups to assign permissions
- apply an IAM password policy

## Policies
- inline policies are for one off permissions
- managed policies exist separately from users or groups
- explicit `Deny`'s will always override any allows