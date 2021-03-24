# IAM

- by default any additional users created on the AWS account are created with no access to any AWS resources
- they have to be explicitly granted
- allows for identity federation (AD, Facebook, Linkedin)
  - includes web identity federation
- MFA
  - no charge
- can provide temporary access
- PCI DSS compliance (credit card stuff)
- universal (doesn't apply to regions)
- new users have no permissions when first created
- new users are assigned access key id and secret access keys when first created
  - not the same as a password
  - only used for programatic access
- can only view password/access keys once
- global service (not scoped to select regions)
  - but can provide regional based access
- doesn't allow biometric auth
- power users
  - full access to aws services but management of iam users/groups/roles
- https certs
  - iam can be used to store and deploy these
  - use iam only when you must support https connections in a region that is not supported by ACM
  - must obtain cert from external source to use in iam

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
- written in json (not yml)

## Roles
- secure way to grant permissions to entities that you trust
- can apply to
  - iam user in another account
  - application code
  - aws service
  - users from a corporate directory who use identity federation with SAML
- iam rules issue keys are valid for short durations, making them a more secure way to grant access