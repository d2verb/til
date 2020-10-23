# serverless framework で CloudWatch Logs にログを吐く lambda function を作る
## serverless.yml
まずは、`serverless.yml`
```yaml
service: lambda-logs

frameworkVersion: '2'

provider:
  name: aws
  runtime: python3.8

  # you can overwrite defaults here
  stage: dev
  region: ap-northeast-1

  # you can add statements to the Lambda function's IAM Role here
  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "logs:PutLogEvents"
        - "logs:CreateLogGroup"
        - "logs:CreateLogStream"
        - "logs:DescribeLogStreams"
      Resource:
        Fn::GetAtt:
          - actionLogGroup
          - Arn

  # you can define service wide environment variables here
  environment:
    LOG_GROUP_NAME: /sample/${opt:stage, self:provider.stage, 'dev'}/actionLogs

functions:
  post-to-cloudwatch-logs:
    handler: handler.invoke

resources:
  Resources:
    LogGroup:
      Type: AWS::Logs::LogGroup
      Properties:
        LogGroupName: /sample/${opt:stage, self:provider.stage, 'dev'}/actionLogs
        RetentionInDays: 30
  Outputs:
    actionLogGroup:
      Description: "ARN of LogGroup for actionLogs"
      Value:
        Fn::GetAtt:
          - actionLogGroup
          - Arn
```
ポイントはいくつかある。
1. `iamRoleStatements`
2. `Fn::GetAtt`

### iamRoleStatements
serverless framework のドキュメント[1]によると

> To add specific rights to this service-wide Role, define statements in provider.iamRoleStatements which will be merged into the generated policy.

service-wide な IAM ロールを追加する。 service-wide なので、すべての functions に適用される。
また、 `will be merged into the generated policy` なので、デフォルトのポリシーを上書きするのではなく、マージされるらしい。

### Fn::GetAtt
ロググループの ARN を取得するのに使う。 `Ref` が使えるかと思ったが、どうやら `Ref` では ARN ではなくリソース名を取得するらしい[2]。
Cloud Formation のドキュメントによると ARN は `Ref` ではなく、 `Fn::GetAtt` 組み込み関数を使えば取れるようなので、これを使った。

## コード
実際に使ったコードの一部を以下に示す。コードの説明はコメントに書いてあるので、詳しくはコードを参照。

```python
def invoke(event, context):
    client = boto3.client("logs", region_name="ap-northeast-1")

    log_group_name = os.environ["LOG_GROUP_NAME"]
    log_stream_name = time.strftime("%Y-%m-%d")

    # ログストリームを作成する。すでに作成済みの場合は ResourceAlreadyExistsException を発生させるがこれは無視
    try:
        client.create_log_stream(
            logGroupName=log_group_name,
            logStreamName=log_stream_name
        )
    except client.exceptions.ResourceAlreadyExistsException as e:
        pass

    log_events = # 吐く予定のログを生成する

    # PutLogEvents action には sequence token が必要なので、これを取得する
    # 新規に作成したログストリームには不要
    sequence_token = None
    try:
        response = client.describe_log_streams(
            logGroupName=log_group_name,
            logStreamNamePrefix=log_stream_name
        )
        if len(response["logStreams"]) > 0:
            sequence_token = response["logStreams"][0].get("uploadSequenceToken", None)
    except:
        print("Error: failed to describe log streams")

    try:
        if sequence_token:
            res = client.put_log_events(
                logGroupName=log_group_name,
                logStreamName=log_stream_name,
                logEvents=log_events,
                sequenceToken=sequence_token,
            )
        else:
            res = client.put_log_events(
                logGroupName=log_group_name,
                logStreamName=log_stream_name,
                logEvents=log_events,
            )
    except:
        print("Error: failed to put logs")

    return {"statusCode": 200}
```

---
参考文献

[1] https://www.serverless.com/framework/docs/providers/aws/guide/iam#the-default-iam-role

[2] https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-logs-loggroup.html
