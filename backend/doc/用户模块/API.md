## 注册功能
-  路由：`usr/signup`
- 功能：创建一个用户
- 是否受到保护：否
- 接受参数：js 对象
```json
{
	name: "string",
	email: "string",
	phone: "string",
	passwd: "string"
}
```

> [!info]
> `email` 和 `phone` 字段二选一
> 
> 前端传过来之前最好验证一下是否符合邮件和电话号码格式（电话号码格式采用 E164 格式，就是由国家前缀，如中国 +86 那种，并且中间没有空格）


- 返回内容：裸的 jwt，放在报文的 body 部分

## 登录功能
- 路由：`usr/login`
- 功能：处理一个账户登录请求
- 是否收到保护：否
- 接收参数：js 对象
```json
{
	email: "string",
	phone: "string",
	id: 123,
	passwd: "string",
}
```

> [!info]
> `email` `phone` `id` 字段三选一，同样需要校验，格式同上

- 返回内容：裸的 jwt，放在报文的 body 部分
## 注销功能
- 路由：`usr/delete_count`
- 功能：删除一个账户
- 是否收到保护：是
- 接收参数：密码，将密码放在 Body 部分，使用 JSON 作为承载媒体，根节点仅有一个 String
- 返回值：操作是否成功以及对应的操作码

## JWT 内容

### 头部

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

此处选用 `HS256` 算法

### 负载

```json
{
  "id": 2,
  "name": "Sylvan Lyon",
  "email": "sylvanlyon@gmail.com",
  "phone": null,
  "exp": 1754696343
}
```

返回字段包含 `id`、`name`、`email`、`phone` 和 `exp`。其中  `exp` 为 `token` 过期时间，目前默认是 12 小时后，默认拥有 60 秒的宽限期