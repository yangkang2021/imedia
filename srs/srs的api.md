# srs的api

## 文档地址
https://ossrs.net/lts/zh-cn/docs/v4/doc/http-api

## 最常用的api
```
srs api 维护
获取所有流/客户端信息
curl -v -X GET  http://localhost:1985/api/v1/streams/?count=1000 |  jq
curl -v -X GET  http://localhost:1985/api/v1/clients/?count=1000 |  jq

获取单个流/客户端信息
curl -v -X  GET http://localhost:1985/api/v1/streams/{stream_id} |  jq
curl -v -X  GET http://localhost:1985/api/v1/clients/{client_id} |  jq

踢客户端
curl -v -X   DELETE http://localhost:1985/api/v1/clients/{client_id} |  jq```