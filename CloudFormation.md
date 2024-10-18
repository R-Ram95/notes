---
tags:
  - AWS
  - DevOps
  - Fundamentals
---
Infrastructure as code. Partition resources, set up security groups, etc. using code in a `YAML`

# Resources

FAQ:

Can I create a dynamic amount of resources?

No, Everything in the CloudFormation template must be declared, no code generation

Is every AWS resource supported?

Almost, there are a few niche cases.

# Parameters

Allow us to provide inputs to CloudFormation template. We do not need to create new templates. Use params if resource config is going to change in the future.

## Referencing Params

`Fn::Ref` ⇒ `!Ref` in YAML

References can be used for Parameters AND Resources

  

# Mappings

Hard coded and fixed variables within CloudFormation templates. They are useful for to differentiate between different environments (dev vs. prod), regions, AMI Types, etc. They are great to use if you know the values in advance.

## Accessing Maps

use the `Fn::FindInMap` function ⇒ shorthand = `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`

  

# Outputs

Optional output parameters that can be imported into other stacks. You can also view outputs in the CLI. Enables cross stack collaboration.

Use the `Export:` block

## Cross Stack Reference

- to import, the stack that exports the value cannot be deleted

`Fn::ImportValue` function ⇒ shorthand `!ImportValue`

# Conditions

Used to control the creation of resources based on some condition on variables. Common conditions:

- Environment (dev/ test/ prod)

Conditions can reference another condition, parameter, value or mappings

## Creating a condition

use the `Condition:` block

`!Equals [!Ref param, ValueToTestAgainst]`

`Fn::And`

`Fn::Equals`

`Fn::If`

`Fn::Not`

`Fn::Or`

# Must Know Functions

- `Fn::Ref` ⇒`!Ref` - reference a param ⇒ returns the value of the parameter or the physical resource id if you reference a resource
- `Fn::GetAtt` - get attributes from resources, not just the physical id
- `Fn::FindInMap` - reference a map
- `Fn::ImportValue` - import values that are exported from other templates
- `Fn::Join` ⇒ `!Join`- join value with a delimiter
- `Fn::Sub` ⇒ `!Sub` - substitute values within strings
- Condition functions

# CloudFormation Rollbacks

Stack Creation Fails ⇒ Default: Everything gets deleted, Option: disable rollback and troubleshoot what happened

  

Stack Update Fails ⇒ Stack rolls back to the previous working state ⇒ can look in the log what happened and error messages

  

# Advanced Concepts

## ChangeSets

When you update a stack, you need to know what will change for greater confidence. ChangeSets tell us what will happen but cannot tell us if the changes will be successful.

## NestedStacks

NestedStacks are stacks that are part of other stacks. They allow you to isolate repeated patterns / other components in separate stacks.

Examples:

- Load Balancer that is re-used

To update a nested stack ⇒ ALWAYS UPDATE THE PARENT STACK

### NestedStacks vs. Cross

Cross:

- helpful when stacks have different lifecycles
- Export and Import
- when you need to export values to multiple stacks

NestedStack:

- helpful when components must be re-used
    - important only to the higher-level stack ⇒ not shared

## StackSet

Create, Update, or Delete stacks across multiple accounts and regions with a single operation.

Administrator account creates StackSets

Trusted accounts Create, Update or Delete stack instances from StackSets

When you update a stack set ⇒ all stack instances across all regions and accounts are updated

# Drift

Someone could make manual config changes to resources created by CloudFormation ⇒ DRIFT helps us detect these changes