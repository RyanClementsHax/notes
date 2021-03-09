# IAM

- by default any additional users created on the AWS account are created with no access to any AWS resources
- they have to be explicitly granted
- allows for identity federation (AD, Facebook, Linkedin)
- MFA
- can provide temporary access
- PCI DSS compliance (credit card stuff)
- universal (doesn't apply to regions)
- new users have no permissions when first created
- new users are assigned access key id and secret access keys when first created
  - not the same as a password
  - only used for programatic access
- can only view password/access keys once

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