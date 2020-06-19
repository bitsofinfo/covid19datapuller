# serverless demo

This demo will get you up and running with the [Serverless framework](https://www.serverless.com/) with an example AWS lambda (python3) function that pulls public data from http://coronavirusapi.com/ every `60` minutes, and writes it to an s3 bucket under `[bucketname]/[fetch-timestamp]/data.json`

## Prerequisites

* Recent version of Node
* Python 3.x & pip 3.x
* AWS cli w/ configured credentials with the appropriate access to create s3 buckets, lambda functions, cloudformation stacks, cloudwatch events etc.

## Overview

1. Clone this project and `cd covid19datapuller/`

2. Install Serverless:
```
curl -o- -L https://slss.io/install | bash
```

3. Install a few serverless plugins:
```
npm install --save serverless-plugin-scripts
npm install serverless-offline --save-dev
```

4. Initialize local py modules
```
serverless install-mods
```

5. Verify your aws credentials are good to go:
```
aws configure list
```

6. Deploy the function to the "dev" stage
```
serverless deploy --stage dev
```

7. Deploy the function to the "prod" stage
```
serverless deploy --stage prod
```

8. Run in "offine" local mode (functions exec locally, but writes to s3 are real)
```
serverless offline
```

## Notes

Once up and running you can checkout the created items in the follow AWS services. Be sure to verify that your region matches the region in your local AWS creds file which in-turn is the region where serverless will deploy everything to.

* https://console.aws.amazon.com/events (click on events -> rules)
  * There will be 2 `covid19datapuller-[dev|prod]` event triggers created for both the dev/prod functions
  
* https://s3.console.aws.amazon.com/s3/home
  * There will be 2 `covid19datapuller-[dev|prod]` buckets created, one for each dev/prod function, where the pulled data will be written in JSON format and annotated w/ the function version and timestamp the data was pulled
  
* https://console.aws.amazon.com/lambda
  * There should be 2 `covid19datapuller-[dev|prod]` functions, one for each serverless "stage" (dev/prod)

* https://console.aws.amazon.com/cloudformation/home
    * There should be 2 `covid19datapuller-[dev|prod]` stacks, one for each dev/prod serverless app
  
* https://console.aws.amazon.com/iam/home (look in roles)
  * There should be 2 `covid19datapuller-[dev|prod]` roles with appropriate permissions

## Erratta

`serverless deploy --stage [env]` could be invoked via a simple CICD process which also would seed the `current-version.yaml` file with the current git tag/branch identifier which would subsequently tag the function versions + data written to disk.

## Cleanup


Manual cleanup of buckets should be done first.

After than you can run:
```
serverless remove -s dev
serverless remove -s prod
```
