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

- 推送列表
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

## 数据库表设计

- 用户信息表

```js
 id: // 用户id，唯一字符串，识别用户身份，存储类型VARCHART
 account: // 用户账号，存储类型VARCHART
 password: // 用户账号，存储类型VARCHART
```

- 用户token关联表

```shell
    id   | token  | pass_time | 
---------+--------+-----------+----------------
 userId1 | token1 | a1b23f2c  | ...
 userId2 | token2 | c0932f32  | ...
```

- 消息列表

```shell
 id  | type  | from_id | to_id  | isRead |
-----+-------+---------+------------------------
 id1 | type1 | a1b23f2 | a2s5d2 |    0   | ...
 id2 | type2 | c0932f3 | 2s5ef2 |    1   | ...
```

