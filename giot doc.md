# GIOT 使用说明

### Mosquitto Broker
https://mosquitto.org/

#### 配置 mosquitto.conf
```
#/etc/mosquitto/mosquitto.conf

# listening all interfaces
bind_address 0.0.0.0
#listener 1883

persistence false
persistence_location /var/lib/mosquitto/

log_dest file /var/log/mosquitto/mosquitto.log

allow_anonymous true

# plugin location
include_dir /etc/mosquitto/conf.d

```


#### 配置mosquitto go auth 插件

https://github.com/iegomez/mosquitto-go-auth
1. 按照说明下载代码，编译成 go-auth.so
2. 配置go-auth.conf

```
# pluin location
auth_plugin /etc/mosquitto/conf.d/go-auth.so

auth_opt_log_level debug

# http auth backend
auth_opt_backends http
auth_opt_check_prefix false

auth_opt_cache false
auth_opt_cache_host go-cache
auth_opt_cache_reset true
#Use redis DB 4 to avoid messing with other services.
auth_opt_cache_db 4

# enable user auth and acl check by call http apis custom provides
auth_opt_http_register user, acl

#http backend apis
#http://192.168.118.192:33820/giot/api/v1/check/user
#http://192.168.118.192:33820/giot/api/v1/check/acc
auth_opt_http_host 192.168.118.192
auth_opt_http_port 33820
auth_opt_http_getuser_uri /giot/api/v1/check/user
auth_opt_http_aclcheck_uri /giot/api/v1/check/acc
auth_opt_http_response_mode json
auth_opt_http_params_mode json
auth_opt_http_method POST
```

3. 将go-auth.so 和 go-auth.conf 放到include_dir /etc/mosquitto/conf.d

#### 启动broker
```
mosquitto -c /etc/mosquitto/mosquitto.conf
```

### GIOT Service
代码位置 ../giot/giot-service
后台管理服务，一个controller包含了两个服务的接口，应该要分开。
一个服务是后台管理服务，用来注册身份，组和ACLs
另外一个服务完成broker发来的鉴权请求

#### 配置鉴权服务参数
```
# application.yaml

....
# broker id
broker: org1_broker1

# broker private key
prvkey: emtpy
...

```
#### 启动服务
按照一般的java服务方式启动

### GIOT Client
MQTT客户端服务，提供pub和sub接口

代码位置：../giot/giot-client

#### 配置
application.yaml

```
# client id which was registered to blockchain
client: org1_client1

# client private key
prvkey: -----BEGIN PRIVATE KEY-----\nMIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgq9iujpHHNwn4Ec1G\nszc8UEqDjNqMveqSUuUiTepUUK2hRANCAAQHhVQqCk4MHKUtHziKazF22WqPjTw5\nvjsblPAv/aiugY1ysmysRwn6McIpWoJvF50ZjD7FqSMwdvC/fraMeYVf\n-----END PRIVATE KEY-----\n

# broker ip
broker: 192.168.15.170

```
** 在配置文件里面配置prvkey有点格式问题，需要调试 **

#### 启动服务
按照一般的java服务方式启动

### 接口使用

#### 通过giot service 注册身份，组和ACLs
http://localhost:33820/giot/swagger-ui.html#/


#### 通过giot client 发布和订阅消息

```
http://localhost:33821/giot/api/v1/client/pub

{
    "topic": "org2/topic1/test",
    "msg": "hello msg from org2"
}

http://localhost:33821/giot/api/v1/client/sub
{
    "topic": "org1/topic1/test"
}
```
#### 通过mosquitto 自带命令pub和sub

```
# -P 后面是签名
mosquitto_sub -t 'org1/topic1/test' -v --id org2_client2 -u org2_client2 -P MEUCIAkvMGM/nqZPUoOeij2mG2AUCeEXw75JC83LBpjH6Z+7AiEA/sgmx7Xq8Ro+dhkykCveyHs4YBNtUWx/B8mQXtSZ5kg=

mosquitto_pub -t '/org2/topic1/test' -m 'hello world' --id org2_client2 -u org2_client2 -P xxx

```
