# postman-script-generator 使用说明

## 1. 适用场景

当你有接口文档（Swagger/OpenAPI、Markdown、Word/PDF 的接口说明、或纯文本接口描述）并希望快速得到可直接粘贴到 Postman 的接口测试脚本（Pre-request Script + Tests）时使用本 Skill。

常见诉求示例：
- 根据接口文档生成 Postman 测试脚本/断言
- 将某个接口转换成 Postman 可运行的测试脚本
- 生成一组接口的 Postman 测试脚本（含变量、前后置依赖）

## 2. 使用前准备

建议在 Postman 中准备好环境变量（Environment）：
- baseUrl：接口域名或网关地址（例如 `https://api.example.com`）
- token（可选）：鉴权令牌（例如 JWT），推荐使用请求头 `Authorization: Bearer {{token}}`
- 其他业务变量（可选）：如 `tableName`、`sql`、`orderId`、`userId` 等

如果接口需要签名/加密参数，请在输入文档或文字说明里补充签名规则与密钥来源（建议使用环境变量，避免在脚本中写死敏感信息）。

## 3. 输入要求（你需要提供什么）

你可以用以下任一方式提供接口信息：

### 方式 A：直接给文件路径
示例：
- `根据 d:\path\接口文档.doc 生成 postman 接口测试脚本`
- `读取 d:\path\openapi.json 生成 Postman Tests`

### 方式 B：直接粘贴接口文档关键内容
建议包含以下字段（越完整越好）：
- 接口名称
- URL（完整路径或相对路径）
- Method（GET/POST/PUT/DELETE）
- Headers（含鉴权方式）
- Query 参数与 Body（字段、类型、是否必填、示例）
- 响应结构（字段含义、业务 code、success 标识等）
- 错误码与异常场景（可选）

### 方式 C：提供 Swagger/OpenAPI 地址
示例：
- `根据这个 Swagger 地址生成 Postman 测试脚本：https://.../v3/api-docs`

## 4. 推荐的提问模板

### 单接口
```
根据以下接口文档，为我生成 Postman 的 Pre-request Script 和 Tests：
1) 接口名称：创建订单
2) URL：/order/create
3) Method：POST
4) Body(JSON)：{...}
5) 响应示例：{...}
要求：
- 校验 HTTP 状态码、响应时间、业务字段（如 success/code/msg/data）
- 如有必要，请将关键字段保存为变量供后续接口使用
```

### 多接口（串联）
```
根据接口文档生成一组 Postman 测试脚本，包含：
- 登录获取 token
- 创建订单并保存 orderId
- 查询订单并校验状态
要求：
- 使用环境变量 baseUrl、token
- 关键字段通过 pm.environment.set 保存，后续接口复用
```

## 5. 输出内容说明

本 Skill 的输出通常包括：
- 每个接口的 Postman 脚本片段：
  - Pre-request Script：用于准备变量、生成时间戳/随机数、签名等
  - Tests：用于断言状态码、业务字段、响应时间、结构与关键字段类型/非空
- 基础使用说明：如何粘贴到 Postman
- 同步保存的 txt 结果文件：
  - 保存路径：`D:\Desktop\skill-demo-docs\testresults-docs\` 目录下
  - 文件内容：接口信息 + Pre-request Script + Tests，便于归档与复用

## 6. 如何粘贴到 Postman

1. 在 Postman 新建或打开一个 Request
2. 配置请求：
   - Method、URL（建议使用 `{{baseUrl}}` 拼接）
   - Headers（必要时加 `Authorization: Bearer {{token}}`、`Content-Type: application/json`）
   - Params / Body
3. 将生成的 Pre-request Script 粘贴到该请求的 “Pre-request Script” 标签页
4. 将生成的 Tests 粘贴到该请求的 “Tests” 标签页
5. 点击 Send 或使用 Collection Runner 批量运行

## 7. 常用脚本能力（你可以要求生成）

- 状态码与响应时间阈值校验
- 业务 success/code/msg/data 校验
- JSON Schema 或关键字段类型校验
- 从响应中提取字段保存为变量（如 `token`、`orderId`）
- 鉴权与签名（需要你提供规则）
- 接口串联（上游返回作为下游入参）
- 负向用例：缺参、非法值、越界值、鉴权失败等

## 8. 常见问题排查

- Postman 报 “Unexpected token”：
  - 通常是响应不是 JSON 或返回了 HTML/空字符串；可先加 `pm.response.to.be.json` 前置校验并打印 `pm.response.text()` 排查
- 断言字段不存在：
  - 说明响应结构与文档不一致；请补充实际响应示例或截图以便调整断言
- 鉴权失败（401/403）：
  - 检查 token 是否设置、header 是否正确、token 是否过期

## 9. 安全与规范建议

- 不要在脚本中写死密钥/账号密码，使用 Postman 环境变量管理
- 若需要打印日志排查，避免输出敏感信息（token、密码、密钥等）

