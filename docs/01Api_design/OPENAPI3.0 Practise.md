OPENAPI3.0 Practise



1. ##### https://app.swaggerhub.com/apis/f5626

2. 





### API 锲约类型



SOAP

REST

OPENAPI

SWAGGER

RAML



Spring

SOAPUI

POSTMAN

MULESOFT











#### REST 设计规范：

1. ​	客户服务器分离
2. ​	可缓存
3. ​	分层系统
4. ​	无状态
5. ​	统一接口
6. ​	服务器可将能力扩展到客户端（非必须）



SOAP：

	- XML
	- 

REST:

 - Openapi
   	- 路径：表示API的访问路径，对于每个路径可以定义路径的参数、查询参数、请求格式和响应格式等
   	- 模型：定义请求和响应中可能出现的对象的结构
	- Swagger

gRPC：

- Protobuf

GraphQL

- GraphQL 描述了数据模型和可以





API锲约工具：

- Postman

- Swagger Editor

- Insomnia

- Insomnia Designer

- Stoplight

- Stoplight Studio

  - Speccial 验证

  - Prism 模拟服务器 : prism-cli.exe mock -d product.v1.yaml

    https://stoplight.io/open-source/prism/

 工具均可以导出API json/yaml file







展示API锲约给API开发者： 

​	SwaggerUI 加载 api json/yaml

​	docker  run --rm -p 8800:8080 -v D:\api-frist\product.v1.yaml:/openapi.yml -e SWAGGER_JSON=/openapi.yaml swaggerapi/swagger-ui

https://swagger.io/tools/swagger-ui/



OpenAPI Generator

https://github.com/openapitools/openapi-generator





