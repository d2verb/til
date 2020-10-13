# AWS SNS から AWS Lambda にメッセージを発行するときのペイロード
仕事で調べる必要があったので、以下のような lambda function を作って、slack に event の内容を飛ばして確認してみた。
```python
#!/usr/bin/python3.6
import urllib3
import json
http = urllib3.PoolManager()

def lambda_handler(event, context):
    url = "https://hooks.slack.com/services/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"
    msg = {
        "text": json.dumps(event),
    }
    
    encoded_msg = json.dumps(msg).encode('utf-8')
    http.request('POST',url, body=encoded_msg)
```

タイトルを `This is test`、メッセージ本文を `これはテストです。` にして SNS からメッセージを発行すると、以下の event が飛んできました。
```json
{
  "Records": [
    {
      "EventSource": "aws:sns",
      "EventVersion": "1.0",
      "EventSubscriptionArn": "...omit...",
      "Sns": {
        "Type": "Notification",
        "MessageId": "...omit...",
        "TopicArn": "...omit...",
        "Subject": "This is test",
        "Message": "これはテストです。",
        "Timestamp": "2020-10-13T12:29:06.725Z",
        "SignatureVersion": "1",
        "Signature": "...omit...",
        "SigningCertUrl": "...omit...",
        "UnsubscribeUrl": "...omit...",
        "MessageAttributes": {}
      }
    }
  ]
}
```

タイトルは `event["Records"][0]["Sns"]["Subject"]` 、メッセージの内容は `event["Records"][0]["Sns"]["Message"]` にありますね。
これをやったあとに気づいたが、 lambda function のマネジメントコンソール画面から、テストを選択して、SNS のイベントテンプレートを見れば、event の構造が書いてあった…

```json
{
  "Records": [
    {
      "EventSource": "aws:sns",
      "EventVersion": "1.0",
      "EventSubscriptionArn": "arn:aws:sns:ap-northeast-1:{{{accountId}}}:ExampleTopic",
      "Sns": {
        "Type": "Notification",
        "MessageId": "95df01b4-ee98-5cb9-9903-4c221d41eb5e",
        "TopicArn": "arn:aws:sns:ap-northeast-1:123456789012:ExampleTopic",
        "Subject": "example subject",
        "Message": "example message",
        "Timestamp": "1970-01-01T00:00:00.000Z",
        "SignatureVersion": "1",
        "Signature": "EXAMPLE",
        "SigningCertUrl": "EXAMPLE",
        "UnsubscribeUrl": "EXAMPLE",
        "MessageAttributes": {
          "Test": {
            "Type": "String",
            "Value": "TestString"
          },
          "TestBinary": {
            "Type": "Binary",
            "Value": "TestBinary"
          }
        }
      }
    }
  ]
}
```
