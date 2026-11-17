# AWS CDK / CloudFormation

- Follow the AWS CDK best practices guide: https://docs.aws.amazon.com/cdk/v2/guide/best-practices.html
- Before writing or reviewing CDK code, run the `cdk_best_practices` tool from the deploy-on-aws plugin and follow what it returns.
- Never set physical resource names (for example `UserPoolName`, `UserPoolClientName`, `BucketName`, `TableName`, `FunctionName`, `RoleName`, `SecretName`, `LogGroupName`). Let CDK and CloudFormation generate them, as the CDK best practices guide says ("Use generated resource names, not physical names").
- Only set a name when the service requires one, or when something outside the stack has to find the resource by that name (for example an ECR repository pulled from another account). When you do, tell me which name and why in your reply.
- Use L2 constructs, or L1 (`Cfn*`) constructs when no L2 covers the resource or property. L3 constructs (patterns) are fine too.
- Never use custom resources: no `CustomResource`, `AwsCustomResource`, `Provider` from `custom_resources`, or `Custom::` resource types.
- If something cannot be done with L1, L2 or L3 constructs, say so and stop. Do not fall back to a custom resource.
