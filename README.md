# tm1test

#GITEA 24Retail


# TM1 settings
# IP or servername where TM1 server runs
@tm1server_ip=localhost
# HTTP Port Number in tm1s.cfg
@tm1server_port=
@tm1_url=https://{{tm1server_ip}}:{{tm1server_port}}
# Integrated Security 1 credentials
@tm1_user_name=
@tm1_user_password=

# git credentials
@git_user_name=
@git_user_password=
@git_user_email=
@git_repo_url=

###
#Simple check to see if we're connected to server -- will return the server metadata
GET {{tm1_url}}/api/v1/$metadata HTTP/1.1

### Get server configuration -- server name 
GET {{tm1_url}}/api/v1/Configuration HTTP/1.1
content-type: application/json
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}

#####
PUT {{tm1_url}}/api/v1/!tm1project HTTP/1.1
content-type: application/json
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}

{
  "Version": "1.0",
  "Name": "My Model",
  "Deployment": {},
  "PrePull": [],
  "PostPull": [],
  "PrePush": [],
  "PostPush": [],


      "Ignore": [
        "Cubes/Views",
        "Cubes",
        "Dimensions",
        "Processes",
        "!Processes('mover')"
    ]
}

##### GET tm1project
GET {{tm1_url}}/api/v1/!tm1project HTTP/1.1
content-type: application/json
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}


### 
#Git Init
POST {{tm1_url}}/api/v1/GitInit HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json

{
"URL": "{{git_repo_url}}",
"Deployment": "dev",
"Username": "{{git_user_name}}",
"Password": "{{git_user_password}}",
"Force": true
}

### 
#Git UnInit
POST {{tm1_url}}/api/v1/GitUninit HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json

{
"Force": true
}

### 
# Check GitStatus
POST {{tm1_url}}/api/v1/GitStatus HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json

{
"Username": "{{git_user_name}}",
"Password": "{{git_user_password}}"
}

### 
# Push code to repo -- 1st time the Branch should be empty 
# we're defining the 'base' or our code 
# ideall should be production server, doing dev now
# Create a push plan that will push the whole model to git
POST {{tm1_url}}/api/v1/GitPush HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json

{ "Branch":"main",
  "NewBranch":"b1",
  "Force": true,
  "Message": "коммит от user11",
  "Author": "user11",
  "Email": "{{git_user_email}}",
  "Username": "{{git_user_name}}",
  "Password": "{{git_user_password}}"
}

### List plans 
GET {{tm1_url}}/api/v1/GitPlans HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json


@git_push_plan_id=6CdQcIGCMyM=
### Execute push plan
POST {{tm1_url}}/api/v1/GitPlans('{{git_push_plan_id}}')/tm1.Execute HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json


### Logout
GET {{tm1_url}}/api/logout HTTP/1.1

### Pull the whole repo to new server
POST {{tm1_url}}/api/v1/GitPull
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json

{
  "Branch": "main",
  "ExecutionMode": "SingleCommit",
  "Force": true,
  "Username": "{{git_user_name}}",
  "Password": "{{git_user_password}}"
}

@git_pull_plan_id=7CdQ0IByITs=
### Execute pull plan
POST {{tm1_url}}/api/v1/GitPlans('{{git_pull_plan_id}}')/tm1.Execute HTTP/1.1
Authorization: Basic {{tm1_user_name}} {{tm1_user_password}}
content-type: application/json
