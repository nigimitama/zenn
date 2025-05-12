---
title: "複数のスタックを作る"
---

https://docs.aws.amazon.com/ja_jp/prescriptive-guidance/latest/patterns/automate-deployment-of-nested-applications-using-aws-sam.html

https://github.com/aws-samples/aws-sam-nested-stack-sample/blob/main/template.yml

```yml
AWSTemplateFormatVersion: "2010-09-09"
Transform: AWS::Serverless-2016-10-31
Description: >
  auth-resources

  SAM Template for auth resources

Resources:
  Auth:
    Type: AWS::Serverless::Application
    Properties:
      Location: auth.yaml
  Product:
    Type: AWS::Serverless::Application
    Properties:
      Location: product-mock.yaml
      Parameters:
        AllowedOrigin: "http://localhost:8080"
    DependsOn: Auth
  Shopping:
    Type: AWS::Serverless::Application
    Properties:
      Location: shoppingcart-service.yaml
      Parameters:
        AllowedOrigin: "http://localhost:8080"
    DependsOn: Product
```
