## 一般情况下的错误原因
### 服务器无法分析请求参数
#### 场景
服务器要求使用 `application/json` 作为请求体的，但客户端传了一个 `text/palin` 过来
#### 客户端应做的
重写，或者报告 `BUG`
#### 服务器响应
400（Bad Request），还有对应的相应内容，相应内容在 [[doc/API#服务器校验#服务器响应|服务器校验]] 部分

### 服务器无法确认登录状态
#### 场景
用户密码输入错误、在未携带令牌或令牌受损的情况下访问受保护的请求路径（如：删除账户）
#### 客户端应该做的
提示用户登录或者重新输入账号密码
#### 服务器响应
401 （Unauthorized），如果是因为 jwt 失效而导致无法确定登陆状态，将返回下面值中的一个（token 被篡改，token 过期）
```json
[
  {"code": "token_invalid"},
  {"code": "token_expired"},
]
```

### 请求方法错误
#### 场景
使用 `POST` 方法请求一个只能用 `GET` 请求的路径
#### 客户端应做的
重写，或者报告 `BUG`
#### 服务器响应
405（Method Not Allowed）

### 服务器校验
#### 场景
服务器会对客户端发来的数据进行校验，如果数据没有通过校验，则会出发此错误，如：密码太短；邮箱格式不符合要求
#### 客户端应做的
提示用户重新输入一个新的值
#### 服务器响应
422 （Unprocessable Entity），响应内容为以下内容之一
```json
{"code":"jsonDataError"}                 // json 合法，但是无法转换为服务器要求的结构
{"code":"jsonSyntaxError"}               // json 语法错误
{"code":"pathFormatError"}               // path 参数格式错误，例如服务器要求一个数字id，但是参数提供的是一个非数字的字符串
{"code":"pathMissingFields"}             // path 参数不完整
{"code":"queryParamDeserializeFailed"}   // URI 请求参数反序列化失败
{"code":"contentTypeMissing"}            // Content-Type 首部缺失
{"code":"failedBufferBody"}              // 服务器无法缓存请求体
```

### 数据库可处理错误
#### 场景
数据库在处理数据时出现了可处理的异常，如：违反重复键约束；违反不可为空约束
#### 客户端应做的
提示用户该账号已经注册、某字段必填之类的
#### 服务器响应
422 （Unprocessable Entity），和一个 JSON 对象，以下为所有可能值的数组，在返回时，会返回下面所有值中的一个：
```json
[
    {"code":"unique"},   // 违反了唯一键约束
    {"code":"foreign"},  // 违反了外键约束
    {"code":"check"},    // 违反了定义的 contraint
    {"code":"not_null"}, // 违反了 NOT NULL 约束
    {"code":"other"},    // 其他错误
]
```

### 服务器错误
#### 场景
服务器在计算哈希值时内存耗尽、数据库连接丢失且不可恢复
#### 客户端应做的
提示用户停止操作，择日再来
#### 服务器响应
500（Internal Server Error）

## 一般情况下错误码出现的原因

|          **错误码**          |                **可能的原因**                 |
| :-----------------------: | :--------------------------------------: |
|    **400 Bad Request**    |       **[[doc/API#服务器无法分析请求参数]]**        |
|   **401 UnAuthorized**    |       **[[doc/API#服务器无法确认登录状态]]**        |
|  **405 Method Not Allowed**   |            **[[doc/API#请求方法错误]]**            |
| **422 Unprocessable Entity**  | **[[doc/API#服务器校验]] 或 [[doc/API#数据库可处理错误]]** |
| **500 Internal Server Error** |            **[[doc/API#服务器错误]]**             |
