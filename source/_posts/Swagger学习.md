---
title: Swagger学习
date: 2017-05-10
tags: API
categories:
- 工具
comments: true
---

## Swagger学习

Swagger是一个API设计工具，使用它可以生成规范的API文档，甚至是生成服务端和客户端代码。

Swagger工具主要包括Swagger Editor、Swagger Codegen和Swagger UI，分别用于API的设计、生成服务端和客户端代码、可视化浏览API文档，另外还提供在线版Swagger Editor，以及Swaggerhub平台（就叫做云版吧）。在体验中，Swaggerhub提供了更好的使用方式。

一般事例：
```yaml
swagger: "2.0"                                  # 必须，后跟版本号，目前是2.0
info:                                           # 必须
  title: Sample API                             # 必须，API标题
  version: 1.0.0                                # 必须，API版本号
  description: API description in Markdown.     # 可选

host: api.example.com                           # 可选 默认为文件所在host
basePath: /v1                                   # 可选 默认为/
schemes:                                        # 可选
  - https

paths:                                          # 必须
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in Markdown.
      produces:
        - application/json
      responses:
        200:
          description: OK
```
YAML语法中尽最大可能省略了括号、引号，使用缩进表示结构层次，使用`-`表示数组，Swagger支持JSON与YAML进行编写。

## paths
最常用、最核心的配置应该是paths的配置，首先跟的是路径`/usrs`，路径下是方法get、post、put、delete等，之后是方法的summary、description等。

### parameters
第一个要注意的是parameters，参数支持查询参数、路径参数、header参数以及表单参数。  
```yaml
# 查询参数使用方法
# GET /users?name={name}&age={age}
paths:
  /users:
    get:
      parameters:
        - in: query                 # 查询参数方式
          name: name                # 参数名
          type: string              # 类型
          description: username     # 可选， 描述
        - in: query
          name: age
          type: integer
# 路径参数
# GET /cars/{carId}/drivers/{driverId}
path:
  /cars/{carId}/drivers/{driverId}: # 路径方式地址需要带参数，已表示参数位置
    get:
      parameters:
        - in: path                  # 路径参数方式
          name: carsId
          type: integer
          required: true            # 路径参数中，必须。 其他方式中此参数可选
          description: carsId
        - in: path
          name: driverId
          type: integer
          required: true
# header参数
# GET /ping HTTP/1.1
# Host: example.com
# X-Request-ID: 77e1c83b-7bb0-437b-bc50-a7a58e5660ac
paths:
  /ping:
    get:
      summary: Checks if the server is alive.
      parameters:
        - in: header
          name: X-Request-ID
          type: string
          required: true
# 表单参数
paths:
  /survey:
    post:
      summary: A sample survey.
      consumes:                                   # 用于表示参数类型
        - application/x-www-form-urlencoded       # 表单参数类型
        # 常用的表单类型有application/x-www-form-urlencoded （用于POST原始值和原始值的数组）
        # 和multipart/form-data （用于上传文件或文件和原始数据的组合）。
      parameters:
        - in: formData
          name: name
          type: string
          description: A person's name.
        - in: formData
          name: fav_number
          type: number
          description: A person's favorite number.
```
在参数配置中，还包括可选参数required(true/false)、default(默认值)、enum(枚举值：[]表示)、allowEmptyValue(true/false)。

### responses
```yaml
paths:
  /ping:
    get:
      produces:                         # 用于表示responses类型
        - application/json
        # 常用类型application/json和application/xml
      responses:
        200:                            # 必须，状态码
          description: OK               # 必须，描述
          schema:                       # 可选，用于描述返回内容
            type: object
            properties:
              id:
                type: integer
                description: The user ID.
              username:
                type: string
                description: The user name.
          header:                       # 可选，返回header内容
            X-RateLimit-Limit:
              type: integer
              description: Request limit per hour.
        default:                        # 可选，未定义的状态码
          description: Unexpected error
          schema:
            $ref: '#/definitions/Error
```
### 数据类型
| Name | type | format | Comments |
| -------| ---- | ---- | ------ | -------- |
| integer(整数) | integer | int32 | signed 32 bits |
| long(长整数) | integer | int64 | signed 64 bits |
| float(浮点数) | number | float |  |
| double(双浮点数) | number | double |  | 
| string(字符串) | string |  |  |
| byte(字节) | string | byte | base64 encoded characters |
| binary(二进制) | string | binary | any sequence of octets |
| boolean(布尔) | boolean  |  |  | 
| date(日期) | string | date | As defined by full-date - RFC3339 |
| dateTime(时间) | string | date-time | As defined by date-time - RFC3339 |
| password(密码) | string | password | Used to hint UIs the input needs to be obscured. |
ps: 除了上面定义的format，可以自定义format。

本文仅列出常用的，方便自己查找的内容，更多内容参考[swagger文档](http://swagger.io/docs/specification/what-is-swagger/)。