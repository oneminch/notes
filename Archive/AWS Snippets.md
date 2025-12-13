## Deploy a Lambda Function via a Docker Image

```json
// lambda-trust-policy.json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Service": "lambda.amazonaws.com"
			},
			"Action": "sts:AssumeRole"
		}
	]
}
```

```bash
# Create Role for Lambda Execution
aws iam create-role \
	--role-name <Lambda-Role-Name> \
	--assume-role-policy-document file://lambda-trust-policy.json \
	--description <Lambda-Role-Description>
```

```bash
# Create ECR Repository
aws ecr create-repository --repository-name <repo-name> --region <aws-region>

# Get Temporary Auth Token for ECR & Authenticate Docker to Your ECR Repository
aws ecr get-login-password --region <aws-region> | docker login --username <username> --password-stdin <account-id>.dkr.ecr.<aws-region>.amazonaws.com

# Build Docker Image
docker build -t <image-name> .

# Tag Docker Image
docker tag <image-name>:<image-tag> <account-id>.dkr.ecr.<aws-region>.amazonaws.com/<repo-name>:<unique-image-tag>

# Push Docker Image to ECR
docker push <account-id>.dkr.ecr.<aws-region>.amazonaws.com/<repo-name>:<unique-image-tag>

# Create Lambda Function using Created Role & Uploaded Image
aws lambda create-function \
	--function-name <function-name> \
	--package-type Image \
	--code ImageUri=<account-id>.dkr.ecr.<aws-region>.amazonaws.com/<repo-name>:<unique-image-tag> \
	--role arn:aws:iam::<account-id>:role/<Lambda-Role-Name> \
	--environment Variables="{SUPABASE_PROJECT_URL=<supabase-project-url>,SUPABASE_PUBLIC_ANON_KEY=<supabase-anon-key>}" \
	--memory-size 512 \
	--timeout 30
```

### CI/CD using GitHub Actions

```bash
# Create an OpenID Connect identity provider for GitHub
aws iam create-open-id-connect-provider \
	--url "https://token.actions.githubusercontent.com" \
	--client-id-list "sts.amazonaws.com"
```

```json
// gh-actions-trust-policy.json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::<account-id>:oidc-provider/token.actions.githubusercontent.com"
			},
			"Action": "sts:AssumeRoleWithWebIdentity",
			"Condition": {
				"StringEquals": {
					"token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
					"token.actions.githubusercontent.com:sub": "repo:<repo-owner>/<repo-name>:ref:refs/heads/<repo-branch>"
				}
			}
		}
	]
}
```

```bash
# Create Web Identity Role for GitHub Actions to Assume
aws iam create-role \
	--role-name <GH-Role-Name> \
	--assume-role-policy-document file://gh-actions-trust-policy.json \
	--description <GH-Role-Description>
```

```json
// gh-actions-permissions.json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ecr:PutImage",
				"ecr:InitiateLayerUpload",
				"ecr:UploadLayerPart",
				"ecr:BatchCheckLayerAvailability",
				"ecr:GetAuthorizationToken",
				"ecr:GetDownloadUrlForLayer",
				"ecr:BatchGetImage",
				"ecr:CompleteLayerUpload"
			],
			"Resource": "*"
		},
		{
			"Effect": "Allow",
			"Action": [
				"lambda:UpdateFunctionCode",
				"lambda:GetFunctionConfiguration"
			],
			"Resource": "arn:aws:lambda:<aws-region>:<account-id>:function:<function-name>"
		}
	]
}
```

```bash
# Attach Permissions Policy
aws iam put-role-policy \
	--role-name <GH-Role-Name> \
	--policy-name <policy-name> \
	--policy-document file://gh-actions-permissions.json
```

Add the role created, `AWS_ROLE_TO_ASSUME`, along with `AWS_ECR_REPO_NAME`, `AWS_LAMBDA_FUNCTION_NAME`, `AWS_REGION` as secret variables inside the GitHub repo.

```yml
# .github/workflows/deploy.yml
name: Deploy to AWS Lambda

on:
	push:
		branches:
			- aws

jobs:
	deploy:
		runs-on: ubuntu-latest

		permissions:
			id-token: write
			contents: read

		steps:
			- uses: actions/checkout@v3

			- name: Configure AWS credentials
				uses: aws-actions/configure-aws-credentials@v4
				with:
					role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
					aws-region: ${{ secrets.AWS_REGION }}

			- name: Login to Amazon ECR
				id: login-ecr
				uses: aws-actions/amazon-ecr-login@v2

			- name: Build, tag, and push image to Amazon ECR
				env:
					ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
					ECR_REPOSITORY: ${{ secrets.AWS_ECR_REPO_NAME }}
					IMAGE_TAG: ${{ github.sha }}
				run: |
					docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
					docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

					echo "image_uri=${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}" >> $GITHUB_ENV

			- name: Update Lambda function
				run: |
					aws lambda update-function-code --function-name ${{ secrets.AWS_LAMBDA_FUNCTION_NAME }} --image-uri ${{ env.image_uri }}
```
