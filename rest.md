## REST Connector

### Examples

#### Basic Examples

Authentication using basic auth:
```
connector "rest" "logout" {
  connection = http.http_conn
  basic_auth {
    username = env("PHOTO_USERNAME")
    password = env("PHOTO_PASSWORD")
  }
  mode       = "Stream"
  resource   = "/me/logout"
  method     = "POST"
  headers = {
    Content-Type : "application/json",
    Accept : "application/json",
    X-Auth-Token : "4CB21004-05DB-48C7-93A3-4B4F8B1BF539"
    X-API-client: "logout"
  }

  payload {
    template =<<EOF
      {
        "authenticationToken": "{{env "CURRENT_AUTH_TOKEN"}}"
      }
    EOF
  }

  on_status {
    status       = 400
    policy       = "ExponentialBackoff"
    count        = 3
    initial_wait = 10
    factor       = 2
  }
  on_status {
    status = 500
    policy = "Log"
  }
} 
```

Authentication using api key (custom HTTP header):
```
connector "rest" "api-key" {
  resource   = "/students"  // a resource guarded with API key
  mode       = "stream"
  connection = http.photo_api
  // https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-key-call.html
  api_key {
    name = "X-API-Key"
    value = awsssm("default", "/api/key", true, true)
    include_as = "header" // or request parameter
  }
  method     = "POST"
  headers = {
    Content-Type : "application/json",
    Accept : "application/json",
    X-API-client: "login"
  }

  payload {
    template = file("assets/login.tmpl")
  }

  on_status {
    status       = 400
    policy       = "ExponentialBackoff"
    count        = 3
    initial_wait = 10
    factor       = 2
  }
  on_status {
    status = 500
    policy = "Log"
  }

  result {
    name = "token"
    filter {
      jsonpath = "/access_token"
    }
  }
} 
```

#### Advance Example
 
OAuth2 authentication using `client`
```
connector "rest" "oauth2" {
  resource   = "/classes"  // a resource guarded with OAuth2
  mode       = "stream"
  connection = http.photo_api

  oauth2 {
    // authorization - use basic auth 
    client_id = env("OUATH2_CLIENT_ID")
    response_type = "code"
    auth_endpoint = "localhost:9000/authorize"
    username = "username"
    password = "password"

    // then use that to get token (that will be sent as the Beaker token)
    secret = env("OAUTH2_CLIENT_SECRET")
    grant_type = "client_credentials"
    scope = "read"
    token_endpoint = "localhost:9000/oauth/token"

    template =<<EOF
      {
        "account_id": "5yiPLwmTTpQVBnMxOlf32q"
      }
    EOF
  }

  method     = "POST"
  headers = {
    Content-Type : "application/json",
    Accept : "application/json",
    X-API-client: "login"
  }

  payload {
    template = file("assets/login.tmpl")
  }

  on_status {
    status       = 400
    policy       = "ExponentialBackoff"
    count        = 3
    initial_wait = 10
    factor       = 2
  }
  on_status {
    status = 500
    policy = "Log"
  }
} 
```


### Reference

| Element | Description | Type | Default Value | Required |
|---------|-------------|------|---------------|----------|
|         |             |      |               |          |
|         |             |      |               |          |
