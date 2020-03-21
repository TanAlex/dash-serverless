# Plotly - Dash app using AWS serverless

Overview:

This is a demo app for Dash.

We use 2 serveless framework plugins to help deploying it to API Gateway and Lambda
  - serverless-domain-manager
  - serverless-wsgi

### serverless-wsgi
serveless-wsgi helps run Flask apps in Lambda

Just need to configure this part to run it
```
functions:
  app:
    handler: wsgi_handler.handler
```
and this to tell wsgi plugin where is the entrance
```
  wsgi:
    app: app.server
    pythonBin: python3
    packRequirements: false
```

### serverless-domain-manager
When we created API Gateway, the path is like: 
`https://bfsxw7f26j.execute-api.us-west-2.amazonaws.com/dev/{proxy+}`

It's long and also contains the "stage" like the /dev part in the URL. It's making the app hard to use and if the app redirect to /, it will also break the app because it has to use /dev in the URL.

The solution is to use API Gateway's "Custom Domain Names".

The plug is to help creating the Custom Domain Names in API Gateway.

Steps:
- Create a subdomain in Route53 like `lab.ttan.cc`
- Create an ACM for certificates with "*.lab.ttan.cc", "*.ttan.cc" etc. Record this ACM's ARN
- Put the domain name and ARN in serverless.yaml
- run `sls create_domain --stage dev --region us-west-2`
- run `sls deploy --stage dev --region us-west-2`

Test app locally using `sls wsgi serve`  
Test app online using `https://dash.lab.ttan.cc/dash`

