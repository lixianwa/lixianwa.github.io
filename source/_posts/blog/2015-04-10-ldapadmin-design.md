---
title: "统一账户管理系统RESTful api设计"
date: 2015-04-10
categories: 技术
tags: ["RESTFul API"]
---


***LDAPAdmin***, 为用户管理提供一组API，以HTTP-based RESTful API的形式提供服务，提供了对ldap数据的增删改查功能。主要用到了HTTP的四种方法：GET， POST， PUT和 DELETE。
 
下面主要对LDAPAdmin 的REST Api做个简单介绍：
 
##Functionality
###Terminology
###User stories
* 利用该组件的HTTP API，通过提交一组描述 用户／部门／系统／管理员的JSON数据，添加、删除、更新、查询数据；
* 利用该组件的HTTP API，可以方便的添加一条 用户／部门／系统／管理员记录；
* 利用该组件的HTTP API，可以方便的更新一条指定的 用户／部门／系统／管理员记录；
* 利用该组件的HTTP API，可以方便的将指定的 用户／部门／系统／管理员删除；

###Scope and Non-Scope
####Scope
* 用户／部门／系统／管理员 信息相关数据的添加、更新、删除；
* 查询 用户／部门／系统／管理员的信息；
* 用户／部门／系统／管理员 的有效性；

###Non-Scope
* 用户数据的可靠性，数据由管理员负责维护

 
##Low-level Design
###Response Code


Code|Remind|Description
:--------------|:------------------|:-------------
200	|OK – [GET]|成功返回用户请求的数据
201	|CREATED – [POST/PUT]|	新建/修改数据成功
204	|NO CONTENT – [DELETE]|	删除数据成功
400	|INVALID REQUEST – [POST/PUT|用户发出请求有误，服务器没有新建/修改数据
401	|Unauthorized – [*]	|token认证失败，token过期等情况
403	|Forbidden – [*]	|用户认证成功，但没有权限进行相应操作（权限细分）
404	|NOT FOUND – [*]	|请求的资源（在这里是表、字段不存在等情况）
406	|NOT Acceptable – [GET]|	用户请求的格式不可得
500	|INTERNAL SERVER ERROR – [*]	|服务器内部错误，用户无法判断请求是否成功
 
###Response| Result
#####除了每个响应都携带的返回码和可选的description字段，以下为该API的返回结果（具体形式在每种请求中都有举例）：

Method|Description
:------------|:-------------
GET	|返回被查询对象的列表，或为空（查询结果为空）
POST|	返回是否插入成功（1为成功，0为失败）
PUT	|返回被修改的行数
DELETE|	返回被删除的行数
 
###Http methods table
####1.用户
HTTP Method|URI|Description
:---------------|:--------------|:-------------
GET	|/v1/user	|获取所有用户记录
GET	|/v1/user/:id	|根据id获取记录
POST|	/v1/user	|添加用户记录
PUT	|/v1/user/:id	|修改某个用户记录
DELETE|	/v1/user/:id	|删除某个用户记录
 
####2. 部门
HTTP Method|URI|Description
:-------------------|:--------------|:-------------
GET	| /v1/department	|获取所有部门记录
GET	| /v1/department/:id	|根据id获取部门记录
GET	| /v1/department/:id?type=subtree	|根据id获取该部门下所有的用户和子部门记录
POST|	/v1/department	|添加部门记录
PUT	| /v1/department/:id	|修改某个部门记录
DELETE|	/v1/department/:id	|删除某个部门记录
 
####3. 系统
HTTP Method|URI|Description
:---------------|:--------------|:-------------
GET	|/v1/system	|获取所有组记录
GET	|/v1/system/:id	|根据id获取记录
POST|	/v1/system	|添加组记录
PUT	|/v1/system/:id	|修改某个组记录
DELETE|	/v1/system/:id	|删除某个组记录
 
####4. 管理员
HTTP Method|URI|Description
:---------------|:--------------|:-------------
GET	| /v1/admin	|获取所有管理员记录
GET	| /v1/admin/:id	|根据id获取记录
POST|	/v1/admin	|添加管理员记录
PUT	| /v1/admin/:id	|修改某个管理员记录
DELETE|	/v1/admin/:id|	删除某个管理员记录
 
###Record infomations
####1.用户
Response

```
{
    "Id": ${id},                  //用户ID
    "UserName": ${user name},     //用户名
    "Password": ${password},      //密码
    "Department": ${department},  //所属部门
    "Mail": ${mail},              //邮件
    "Telephone": ${telephone},    //电话
}
```

####2.部门
Response

```
{
    "Id": ${id},                  //部门ID
    "Name": ${name},              //部门名
    "Parent", ${parent}           //上级部门   
}
```

####3.系统
Response

```
{
    "Id": ${id},                  //组ID
    "Name": ${name},              //组名
    "Member", ${member}           //成员
}
```

####4. 管理员
Response

```
{
    "Id": ${id},                  //管理员ID
    "Name": ${name},              //名字
    "Password", ${password}       //密码   
}
```

###RESTful API
####1. 用户
* GET /v1/user

此API用于查询所有 用户记录

```
Request:
GET / HTTP/1.1
Response:
HTTP/1.1 200 OK
Content-Type: application/json
 
{
   
}
```
Example
下面的示例将查询用户信息

```
Request
GET /v1/user HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
[
  {
  },
  {
  },
  ......
]
```

* GET /v1/user/:id
此API用于查询指定的用户记录

```
Request
GET /v1/user/:id HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    
}
```

Example
下面的示例将查询id为1的用户录

```
Request
GET /v1/user/1 HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "code": 0,
    "rows": 1,
    "result": [{"id": "1", "name": "xxxx", "password": "xxxx", "department":[1, 2], 
    "mail":"xxx@xywy.com", "telephone":"15212233445"}]
}
``` 
 
* POST /v1/user
此API用于新增 用户记录记录

```
Request
POST /v1/user HTTP/1.1
Content-Type: application/json
 
{
    ${Record informations}
}

Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code":${code},   //type is int
    "id": ${id}       //type is string, specifing the id inserted, which can be used in querying through the id    
}
```

Example
下面的示例将插入 用户记录

```
Request
POST /v1/user HTTP/1.1
Content-Type: application/json
 
{
    ${Record informations}
}
Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code": 0,
    "id": 1
}
```
 
 
* PUT /v1/user/:id
此API用于修改用户记录

```
Request
POST /v1/user/:id HTTP/1.1
Content-Type: application/json
 
{
 
}
Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code":0,
    "rows": ${line}       //type is int
}
```
Example
下面的示例将修改用户信息

```
Request
PUT /v1/user/1 HTTP/1.1
Content-Type: application/json
 
{
 
}
Response
HTTP/1.1 201 UPDATED
Content-Type: application/json
 
{
    "code": 0,
    "rows"：3
}
```

* DELETE /v1/user/:id
此API用于删除用户记录

```
Request
DELETE /v1/user/:id
{
     
}
Response
HTTP/1.1 204 NO CONTENT
Content-Type: application/json
```
Example
下面的示例将删除用户记录

```
Request
DELETE  /v1/user/1 HTTP/1.1
{
}
Response
HTTP/1.1 204 NO CONTENT
```
 
2. 部门
 
* GET /v1/department 
此API用于查询所有部门记录

```
Request
GET /v1/department HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
   ${Record informentions}
}
```
Example
下面的示例将查询部门信息

```
Request
GET /v1/department HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
[
  {
 
  },
  {
  },
  ......
]
```
 
* GET /v1/department/:id
此API用于查询指定的部门记录

```
Request
GET /v1/department/:id HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    
}
```

Example
下面的示例将查询id为1的部门记录

```
Request
GET /v1/department/1 HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "code": 0,
    "rows": 1,
    "result": [{"id": "1", "name": "zonghezhikongzhongxin", "parent": ""]
}
``` 
 
* GET /v1/department/:id?type=subtree
此API用于查询指定的部门的所有子用户和子部门记录（树形结构）

```
Request
GET /v1/department/:id?type=subtree HTTP/1.1
Response
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    
}
```

Example
下面的示例将查询id为1的部门的详细信息（包括子部门和用户）记录

```
Request
GET /v1/department/1?type=subtree HTTP/1.1

Response
HTTP/1.1 200 OK
Content-Type: application/json

{
    "code": 0,
    "rows": 1,
    "result": {
                "user":[
                        {"id": 1, "name": "xxxx", "department":[1], "mail":"xxxx@xywy.com", "telephone":"xxxxx"}, 
                        {"id": 2, "name": "xxxxx","department":[1, 2], "mail":"xxxxx@xywy.com", "telephone":"xxxxx"},
                        ......
                       ]
               "department":[
                              {"id": 1 "name":"sa",   "parent":1},
                              { " id": 1, "name" : "anquan", "parent" : 1 }
                                ......
                            ]
             }
 
 ```
 
* POST /v1/department
此API用于新增 部门记录

```
Request
POST /v1/department HTTP/1.1
Content-Type: application/json
 
{
    ${Record informations}
}

Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code":${code},   //type is int
    "id": ${id}       //type is string, specifing the id inserted, which can be used in querying through the id    
}
```

Example
下面的示例将插入部门记录

```
Request
POST /v1/department HTTP/1.1
Content-Type: application/json
 
{
    ${Record informations}
}

Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code": 0,
    "id": 1
}
``` 
 
* PUT /v1/department/:id
此API用于修改部门记录

```
Request
POST /v1/department/:id HTTP/1.1
Content-Type: application/json
 
{
 
}
Response
HTTP/1.1 201 CREATED
Content-Type: application/json
 
{
    "code":0,
    "rows": ${line}       //type is int
}
```

Example
下面的示例将修改ID为1的部门信息

```
Request
PUT /v1/department/1 HTTP/1.1
Content-Type: application/json
 
{
 
}

Response
HTTP/1.1 201 UPDATED
Content-Type: application/json
 
{
    "code": 0,
    "rows"：3
}
```
 
* DELETE /v1/department/:id
此API用于删除部门记录

```
Request
DELETE /v1/department/:id
{
     
}

Response
HTTP/1.1 204 NO CONTENT
Content-Type: application/json
```

Example
下面的示例将删除部门记录

```
Request
DELETE  /v1/department/1 HTTP/1.1
{
}

Response
HTTP/1.1 204 NO CONTENT
```
 
###3. 系统／管理员 与 用户Api很类似，只是URI稍有不同
####系统： /v1/system
####管理员： /v1/admin
返回记录信息在 Record informations中有说明。
 
