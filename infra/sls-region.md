# serverless framework は aws profile の region を使わない

例えば、以下のように profile 内で region を ap-northeast-1 としていても、
```
$ aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile           sample-profile           manual    --profile
access_key     ****************XXXX shared-credentials-file
secret_key     ****************XXXX shared-credentials-file
    region           ap-northeast-1      config-file    ~/.aws/config
```

serverless.yml の region を明確に指定しないと `sls deploy` 時の region はデフォルトで us-east-1 になる。
```yaml
# Welcome to Serverless!
#
# This file is the main config file for your service.
# It's very minimal at this point and uses default values.
# You can always add more config options for more control.
# We've included some commented out config examples here.
# Just uncomment any of them to get that config option.
#
# For full config options, check the docs:
#    docs.serverless.com
#
# Happy Coding!

service: sns-lambda
# app and org for use with dashboard.serverless.com
#app: your-app-name
#org: your-org-name

# You can pin your service to only deploy with a specific Serverless version
# Check out our docs for more details
frameworkVersion: '2'

provider:
  name: aws
  runtime: python3.8

  # you can overwrite defaults here
  stage: dev
  region: ap-northeast-1
  
...omit...
```

また、 `sls` コマンドでも region を指定できる。
```
$ sls deploy --region ap-northeast-1
```

-----
参考文献

[1] https://www.serverless.com/framework/docs/providers/aws/guide/deploying/
