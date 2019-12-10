This is an example of a multi-tenant AWS Amplify application.

The code is not runnable as-is -- the repository only contains the crucial files.

The code is based on the various posts at https://github.com/aws-amplify/amplify-cli/issues/318

The GraphQL resolvers generated by AWS Amplify are passed the access token in order to enforce the access control rules specified in the "schema.graphql" file. This access token contains the Cognito groups that the current user belongs to.

We use a Cognito Pre Token Generation Lambda Trigger to run the Lambda function in "./amplify/function/.../src/alter-claims.js"
This adds a tenant string as a virtual/fake cognito group to the "cognito:groups" field of the access and id tokens.

That tenant string, in the cognito:groups field of the access token, can then be used by the generated GraphQL resolvers to enforce the rule "@auth(rules: [{allow: groups, groupsField: "tenant"}])" in "./amplify/backend/api/.../schema.graphql"
This ensures that a user can only view items associated with their tenant in the database and can only add items with the correct "tenant" field set to the database.


The app was created with the following commands
- `npx react-native init AmplifyMultiTenant`
- `npm install aws-amplify aws-amplify-react-native amazon-cognito-identity-js`
- `amplify init`
- `amplify add auth`
- Make sure to select "Override ID Token Claims" as in https://aws-amplify.github.io/docs/cli-toolchain/quickstart?sdk=js#adding-a-lambda-trigger
- `amplify add api`