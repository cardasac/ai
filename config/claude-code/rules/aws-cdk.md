# AWS CDK / CloudFormation

- Follow the AWS CDK best practices guide: https://docs.aws.amazon.com/cdk/v2/guide/best-practices.html
- Before writing or reviewing CDK code, run the `cdk_best_practices` tool from the deploy-on-aws plugin and follow what it returns.
- Never set physical resource names (for example `UserPoolName`, `UserPoolClientName`, `BucketName`, `TableName`, `FunctionName`, `RoleName`, `SecretName`, `LogGroupName`). Let CDK and CloudFormation generate them, as the CDK best practices guide says ("Use generated resource names, not physical names").
- Only set a name when the service requires one, or when something outside the stack has to find the resource by that name (for example an ECR repository pulled from another account). When you do, tell me which name and why in your reply.
- Use L2 constructs, or L1 (`Cfn*`) constructs when no L2 covers the resource or property. L3 constructs (patterns) are fine too.
- Never use custom resources: no `CustomResource`, `AwsCustomResource`, `Provider` from `custom_resources`, or `Custom::` resource types.
- If something cannot be done with L1, L2 or L3 constructs, say so and stop. Do not fall back to a custom resource.
- Only deploy AWS resources through the project's CDK code. Never suggest any other way to deploy them.
- The CDK CLI is installed and maintained globally. Run it as plain `cdk` (for example `cdk deploy <stack> --profile ops`). Never use `npx cdk`, `npm run cdk -- ...`, or a local copy, and never add `aws-cdk` as a project dependency.
- Never hardcode values AWS generates in CDK code, such as ARNs, resource IDs and DNS records (for example ACM validation CNAMEs) copied from the console or API output. Reference them through constructs, cross-stack references or `Fn.getStackOutput` (for example `CertificateValidation.fromDns(hostedZone)`). Account IDs and regions in a stack's `env` are fine. If CDK cannot produce a value that way, say so and stop.
- Share values between stacks like this:
  - Same CDK app: pass the construct or attribute and let CDK create the reference. Set the feature flag `@aws-cdk/core:defaultCrossStackReferences` to `weak`, because strong cross-region references are custom resources.
  - Different CDK app, same region: the producer publishes an SSM parameter and the consumer reads it with `StringParameter.valueForStringParameter`, resolved at deploy time.
  - Different CDK app, different region: add an explicit `CfnOutput` to the producer and read it with `Fn.getStackOutput(stackName, outputName, region)`.
  - Use `StringParameter.valueFromLookup` only when the value is needed at synth time, because it saves the value into `cdk.context.json`.
