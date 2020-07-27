# *用户管理API SERVICE*

## 流程说明

1. 默认分配固定个数管理员账号，一个用户ID可对应多个账号管理员可通过该账号新建子账号并针对子账号设定操作链码管理组织等权限
2. 未登录账号可通过账号密码登录并生成登录token
3. 已登录账号验证token登录状态
4. 用户退出登录清除token
5. 用户创建组织|节点加入组织操作通过Baas调度中心反馈的用户映射信息进行存储
6. 接受Baas调度中心推送的用户对某个通道进行的操作并与之前存储的映射信息进行比对，校验并反馈操作权限 
7. 维护一个消息列表保存加入组织等操作的推送信息

## 接口设计

- 统一状态码
    * success：
    ```js
    {
        code: 200,
        status: true,
        msg: 'success'
    }
    ```

    * fail：
    ```js
    {
        code: 500,
        status: false,
        msg: 'failed'
    }
    ```

- 登录
    * 入参：
    ```js
    { // body
        account: '' [String], // 登录账号
        password: '' [String] // 登录密码
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data:{
            token: '' [String] // 返回的用户token
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- token校验
    * 入参：
    ```js
    { // header
        token: '' [String] // 用户token
    }
    ```
    * 出参：
    ```js
    {
        code: 999 , // token校验状态码，999已过期，200生效中
        data: {
            status: false [Boolean]
        },
        msg: 'token已过期' [String] // token说明
    }
    ```

- 退出登录
    * 入参：
    ```js
    { // body
        
    }
    ```
    * 出参：
    ```js
    {
        code: 200 [Number],
        data:{
            status: true [Boolean]
        },
        msg: 'success' [String]
    }
    ```

- 用户操作权限映射表存储 [待定]
    * 入参：
    ```js
    {   // header
        BAAS-TOKEN: '' [String] // 
        // body
        userId: '' [VARCHART],
        orgIds: 'id,id' [VARCHART]
    }
    ```
    * 出参：
    ```js
    {
        code: 200 INT,
        data:{
            status: true [INT]
        },
        msg: 'success' [VARCHART]
    }
    ```

- 用户操作权限映射表校验 [待定]
    * 入参：
    ```js
    {   // header
        BAAS-TOKEN: '' [VARCHART]
        // body
        userId: '' [VARCHART],
        orgIds: 'id,id' [VARCHART]
    }
    ```
    * 出参：
    ```js
    {
        code: 200 INT,
        data:{
            status: true [INT]
        },
        msg: 'success' [VARCHART]
    }
    ```

- 获取推送列表
    * 入参：
    ```js
    {   // header
        BAAS-TOKEN: '', [String] // 用户token请求头
        // body
        data: {}
    }
    ```
    * 出参：
    ```js
    {
        code: 200,
        data: {
            list: [{
                fromName: 'fromName', [String] // 发起者名称
                fromId: 'fromId', [String] // 发起者id
                type: 0 [Number], // 0加入联盟 1退出联盟 2加入账本 3退出账本
                isRead: 0, [Boolean], // 是否已读 0未读1已读
                contain: 'XXX正在申请XXX' // 通知内容 
            }]
        },
        msg: 'success' [String]
    }
    ```

- 更新推送列表状态
    * 入参：
    ```js
    {   // header
        BAAS-TOKEN: '', [String] // 用户token请求头
        // body
        data: {
            id: '', // 消息id
            isRead: 0, // 状态 0未读 1已读
        }
    }
    ```
    * 出参：
    ```js
    {
        code: 200,
        data: {
            status: true
        },
        msg: 'success' [String]
    }
    ```

- 新增一条推送信息
    * 入参：
    ```js
    {   // header
        BAAS-TOKEN: '', [String] // 用户token请求头
        // body
        data: {
            type: 0 [Number], // 推送类型 0加入联盟 1退出联盟 2加入账本 3退出账本
        }
    }
    ```
    * 出参：
    ```js
    {
        code: 200,
        data: {
            status: true
        },
        msg: 'success' [String]
    }
    ```

## 数据库表设计

- 用户信息表

```js
 id: // 用户id作为主键，唯一字符串，识别用户身份，存储类型VARCHART
 account: // 用户账号，存储类型VARCHART，长度设计3-50字符
 password: // 用户账号，存储类型VARCHART，长度设计3-50字符
```

- 用户token关联表

```js
 id: // 用户id作为外键，用来关联用户信息表中的用户，存储类型VARCHART
 token: // 用户token，每登陆用户账号时根据外键id查询并更新该id对应的token值，存储类型VARCHART
 pass_time: // 用户token过期时间，每登陆用户账号时根据外键id查询并更新token的失效时间，退出登陆的同时将该值更新为0，存储类型VARCHART
```

- 消息列表

```js
 id: // 递增id作为主键，用来查询详情
 fromName: // 消息发起者名称，存储类型VARCHART，长度设计3-50字符
 fromId: // 消息发起者id，存储类型VARCHART
 type: // 消息自定义类型，0加入联盟 1退出联盟 2加入账本 3退出账本，存储类型INT
 isRead: // 消息是否已读 0未读 1已读 更新消息状态的时候用到，新增的时候默认为0，存储类型INT
 contain: // 通知内容，用来前端展示消息列表通知内容，存储类型VARCHART
```

