# serverless framework で AWS SNS のメッセージ受け取る lambda function を作る

`sls` コマンドでプロジェクトを作成して、 serverless.yml と handler.py を以下のように変更する。以下の yaml ファイルでは、lambda function と共に sns topic と subscription を同時に作成する。

```yaml
# serverless.yml
service: sns-lambda

frameworkVersion: '2'

provider:
  name: aws
  runtime: python3.8
  stage: dev
  region: ap-northeast-1

functions:
  hello:
    handler: handler.hello
    events:
      - sns:
          arn: !Ref snsTopic
          topicName: snsTopic

resources:
  Resources:
    snsTopic:
      Type: AWS::SNS::Topic
  Outputs:
    snsTopicArn:
      Description: "ARN of SNS Topic"
      Value: !Ref snsTopic
```

```python
# handler.py
import json


def hello(event, context):
    print(event["Records"][0]["Sns"]["Message"])
```

あとは、 `sls deploy -v` コマンドでデプロイするだけ。なお、 `-v` オプションを付けることによって stack output 表示することができる。
```
$ sls deploy -v
...omit...
Serverless: Stack update finished...
Service Information
service: sns-lambda
stage: dev
region: ap-northeast-1
stack: sns-lambda-dev
resources: 9
api keys:
  None
endpoints:
  None
functions:
  hello: sns-lambda-dev-hello
layers:
  None

Stack Outputs
snsTopicArn: arn:aws:sns:ap-northeast-1:123456789012:sns-lambda-dev-snsTopic-123456789012
HelloLambdaFunctionQualifiedArn: arn:aws:lambda:ap-northeast-1:123456789012:function:sns-lambda-dev-hello:4
ServerlessDeploymentBucketName: sns-lambda-dev-serverlessdeploymentbucket-123456789012
```

デプロイが完了したあと、 SNS のマネジメントコンソールからメッセージを発行して、cloudwatch logs から lambda のログを見てみると、送信したメッセージがあるはず。
