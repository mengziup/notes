# GIOT 链码实现

### 需求

- 作为组织管理员，我希望在合约中注册组织，client和broker设备，以确定数据的发布和使用者身份
- 作为组织管理员，我希望可以批量管理设备和数据
- 作为组织管理员，我希望特定client在特定的broker上发布信息，以保护数据的来源
- 作为组织管理员，我希望特定的client在特定的broker上接收信息，保证数据被恰当的使用
- 作为组织管理员，我希望组织的数据能够安全传输而不被窃听
- 作为组织管理员，我希望接收的数据是完整的
- 作为组织管理员，我希望将数据分享给特定的接收者，以确保数据的安全性
- 作为组织管理员，我希望客户端可以连接不同的broker，以避免单点故障造成系统不可用

### 模型

```mermaid
classDiagram
class Id {
  +String id
  +String pub
  +String description
  +Role role
}

class Org~Id~

class Client~Id~{
   +String org
}

class Broker~Id~{
   +String org
   +String ip
}

Id <|-- Client
Id <|-- Broker
Id <|-- Org

class Auth {
   +String id
   +String op
   +String security
}
class Group {
    +String id
    +String[] items
}

class Security {
    +int level
}
class Operation {
    +op //sub, pub
}

Auth --> Group
Auth --> Security
Auth --> Id
Auth --> Operation

class CB {
    +String[] clients
    +String[] brokers
}

CB --> Client
CB --> Broker

class Connection {
    +String client
    +String broker
}

Connection --> Client
Connection --> Broker

class Data {
    +String Id
    +String client
    +String hash
}

```

### 流程

#### 身份管理

##### 注册组织

```mermaid
sequenceDiagram
    user->>api: register org
    api ->>AccCenter: register
    AccCenter ->> AccCenter: check signature
    AccCenter ->> AccFactory: create account
    AccFactory -->>AccCenter: acc
    AccCenter ->> AccRepo: save
    AccRepo -->> AccCenter: success
    AccCenter -->> api: success
    api -->> user: success
    
    
```

##### 注册Client/Broker

```mermaid
sequenceDiagram
    user->>api: register client/
    api ->>AccCenter: register
    AccCenter ->> AccRepo: get org
    alt org not exist
    AccCenter -->> api: err
    api -->> user: err
    end
    AccCenter ->> AccCenter: check signature
    AccCenter ->> AccFactory: create a account
    AccFactory -->>AccCenter: acc
    AccCenter ->> AccRepo: save
    AccRepo -->> AccCenter: success
    AccCenter -->> api: success
    api -->> user: success
```

##### 查询身份

```mermaid
sequenceDiagram
    user->>api: get id
    api ->>AccCenter: get
    AccCenter ->> AccRepo: get
    AccRepo -->> AccCenter: 
    AccCenter -->> api: 
    api -->> user: 
```

##### 删除身份

```mermaid
sequenceDiagram
    user->>api: del id
    api ->>AccCenter: del
    AccCenter ->> AccRepo: del
    AccRepo -->> AccCenter: ok
    AccCenter -->> api: ok
    api -->> user: ok
```



#### 组管理

##### 创建组

```mermaid
sequenceDiagram
    user->>api: add to group
    api ->>GroupManager: add
    GroupManager ->> GroupRepo: get
    alt group != nill
    groupRepo -->> GroupManager: group
    else
    GroupManager ->> GroupFactory: create
    GroupFactory -->> GroupManager: group
    end
    GroupManager ->> Group: add items 
    GroupManager ->> GroupRepo: save
    GroupManager -->> api: 
    api -->> user: 
```

##### 查询组

```mermaid
sequenceDiagram
    user->>api: get by group id
    api ->>GroupManager: get
    GroupManager ->> GroupRepo: get
    GroupRepo -->> GroupManager: group
    GroupManager -->> api: group
    api -->> user: group
```

##### 通过身份Id查询组Id

```mermaid
sequenceDiagram
    user->>api: get group names by id
    api ->>GroupManager: getGroupsById
    GroupManager ->> GroupRepo: getGroupsById
    GroupRepo -->> GroupManager: group names
    GroupManager -->> api: group names
    api -->> user: group names
```

##### 删除组

```mermaid
sequenceDiagram
    user->>api: del
    api ->>GroupManager: del
    GroupManager ->> GroupRepo: del
    GroupRepo -->> GroupManager: ok
    GroupManager -->> api: ok
    api -->> user: ok
```

##### 将身份移出组

```mermaid
sequenceDiagram
    user->>api: delFromGroup: {group, [id...]}
    api ->>GroupManager: delFromGroup
    GroupManager ->> GroupRepo: delFromGroup
    GroupManager -->> api: ok
    api -->> user: ok
```



#### 权限管理

##### 授权

```mermaid
sequenceDiagram
    user->>api: grant: {topic, [{groups, op, se}...]}
    api ->>AuthManager: grant
    AuthManager ->> AuthFactory: get
    AuthFactory -->> AuthManager: auth
    AuthManager ->> AuthRepo: save:auth
    AuthManager -->> api: ok
    api -->> user: ok
```

##### 鉴权

```mermaid
sequenceDiagram
    user->>api: auth: {topic, id, op}
    api ->>AuthManager: auth
    AuthManager ->> GroupRepo: get groups by id
    AuthManager -->> AuthRepo: get auth by topic and group
    AuthManager ->> Auth: auth
    AuthManager -->> api: ok
    api -->> user: ok
```

##### 查找授权

```mermaid
sequenceDiagram
    user->>api: get auth: {topic, id, op}
    api ->>AuthManager: get
    AuthManager ->> GroupRepo: getGroupsById
    AuthManager ->> AuthRepo: get by topic 
    AuthRepo -->> AuthManager: auth
    AuthManager -->> api: auth
    api -->> user: auth
```

##### 取消授权

通过将Id移出相关的组实现

#### 网络管理

##### 设置client可连接的broker

```mermaid
sequenceDiagram
    user->>api: connect: {clients, brokers}
    api ->>ConnectionManager: add
    ConnectionManager ->> ConnectionRepo: add
    ConnectionManager -->> api: 
    api -->> user: 
```

##### 获取client可连接的broker

```mermaid
sequenceDiagram
    user->>api: getCon: client
    api ->>ConnectionManager: get
    ConnectionManager ->> ConnectionRepo: get
    ConnectionManager -->> api: [brokers...]
    api -->> user:  [brokers...]
```

##### 移除client和broker的连接

```mermaid
sequenceDiagram
    user->>api: delCon: client, broker
    api ->>ConnectionManager: del
    ConnectionManager ->> ConnectionRepo: del
    ConnectionManager -->> api: [brokers...]
    api -->> user:  [brokers...]
```

##### 记录当前连接

```mermaid
sequenceDiagram
    user->>api: connect: {client, broker}
    api ->>ConnectionManager: addCurrent
    ConnectionManager ->> ConnectionRepo: addCurrent
    ConnectionManager -->> api: 
    api -->> user: 
```

##### 获取当前连接

```mermaid
sequenceDiagram
    user->>api: getCurCon: {client}
    api ->>ConnectionManager: getCur
    ConnectionManager ->> ConnectionRepo: getCur
    ConnectionManager -->> api: {broker}
    api -->> user: {broker}
```

##### 断开当前连接

```mermaid
sequenceDiagram
    user->>api: discon: {client}
    api ->>ConnectionManager: discon
    ConnectionManager ->> ConnectionRepo: discon
    ConnectionManager -->> api: ok
    api -->> user: ok
```

#### 数据管理

##### 上传数据Hash

```mermaid
sequenceDiagram
    user->>api: data: {id, client, hash}
    api ->>DataManager: add
    DataManager ->> DataRepo: add
    DataManager -->> api: ok
    api -->> user: ok
```

##### 获取数据Hash

```mermaid
sequenceDiagram
    user->>api: getData: {id, client}
    api ->>DataManager: get
    DataManager ->> DataRepo: get
    DataManager -->> api: {hash}
    api -->> user: {hash}
```

### 框架设计

##### 系统框架

![image-20220714175046142](https://raw.githubusercontent.com/mengziup/pic/main/image-20220714175046142.png)

##### 合约代码框架

- api  //处理外部请求，参数转换
- models //核心模型
- business //业务流程
- frameworks  //框架相关，如持久化