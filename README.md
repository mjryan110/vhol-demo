# vhol-demo

This is a demo repo to show how easy it is to get setup with dbt continuous integration and deployment using GitHub Actions.
You should be able to run this self-contained example, use the `yml` workflow files as template, make some minor changes and have your own dbt project up and running with CI/CD in no time.

![](img/Datafold_in_dbt_CI.png)

**Tech Stack:**

- dbt Core
- Snowflake
- GitHub Actions
- Datafold Cloud
- AWS S3

If you have questions unique to your tech stack, schedule a call with us at: [Datafold](https://www.datafold.com/)

## What does success look like?

- [ ] Slim CI pipeline that runs and tests only the models that have changed and their downstream models in your pull requests
- [ ] Automated Datafold data diffs in your pull requests if you have a Datafold account
- [ ] Automated deployment of changed dbt models to production and their downstream models when pull requests merge into the main production branch
- [ ] Automated staging schema cleanup after a pull request is closed so you don't have schema clutter
- [ ] You get that sweet sense of satisfaction automating your high quality work

## Getting Started

> This guide is self-contained and can run from scratch. Once you get the hang of it, you can repeat the steps for your own dbt project.
> For those that don't have permissions to configure AWS S3, you can still run the CI pipeline and see the results in your pull requests. You just won't be able to utilize [Slim CI](https://docs.getdbt.com/best-practices/best-practice-workflows#run-only-modified-models-to-test-changes-slim-ci). During the VHOL, I'll walk you through how to ignore the AWS S3 setup and still get the most out of your CI pipeline.

1. Fork and clone this repo locally to your computer. Search the entire repo for `TODO` and replace with your own values
2. Setup your S3 bucket for Slim CI: [Video Tutorial](https://www.loom.com/share/d7be198712df4a7cbc12ddac3e529365?sid=fa1d1557-d4c0-45e7-991b-2e7f61bcdf73)

```bash
# setup your virtual environment
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
source venv/bin/activate

# run the production dbt models
# upload your production manifest file to your S3 bucket
dbt ls --target prod
```
<details>
  <summary>AWS IAM Policy Code Snippet</summary>
  
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListObjectsInBucket",
            "Effect": "Allow",
            "Action": ["s3:ListBucket"],
            "Resource": ["arn:aws:s3:::vhol-datafold-dbt-prod-manifest"] # TODO: replace with your own bucket name
        },
        {
            "Sid": "AllObjectActions",
            "Effect": "Allow",
            "Action": "s3:*Object",
            "Resource": ["arn:aws:s3:::vhol-datafold-dbt-prod-manifest/*"] # TODO: replace with your own bucket name
        }
    ]
}
  ```

  This is an Amazon Web Services (AWS) Identity and Access Management (IAM) policy. IAM policies define permissions for action on resources. In your context, specifically:

1.	"ListObjectsInBucket" - This statement permits the involved IAM identity (User, Group, or Role) the action s3:ListBucket which means they can list the objects in the specified S3 bucket - "vhol-datafold-dbt-prod-manifest".

2.	"AllObjectActions" - This statement allows the IAM identity to perform all operations (﻿s3:*Object) that apply to objects within the S3 bucket "vhol-datafold-dbt-prod-manifest". This could include actions like s3:GetObject, s3:PutObject, s3:DeleteObject etc.

These permissions are only for the specified S3 bucket (arn:aws:s3:::vhol-datafold-dbt-prod-manifest and arn:aws:s3:::vhol-datafold-dbt-prod-manifest/*). Any other buckets and resources are unaffected by this policy.

Please replace the placeholder with your own bucket name for the policy to work as intended.
  
</details>

3. Set up your GitHub secrets
4. Commit your changes and push to your remote repository
5. Run the below to build your dbt project on the `main` branch

```bash
dbt build --target prod
```

6. Run the below to switch branches and test your CI pipelines

```bash
git checkout -b feature/your-feature
```

7. Make some changes to your dbt models and push to your remote repository. You can copy and paste the examples here and run it locally: [example_dbt_changes](example_dbt_changes/)

```bash
dbt build
```

8. See your CI pipeline run and test your changes in your pull request

9. Merge the pull request and watch as your changes are deployed to production and your staging schema is cleaned up
