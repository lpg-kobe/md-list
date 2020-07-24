# *用户管理API SERVICE*

## 流程

1. 默认分配固定个数管理员账号，一个用户ID可对应多个账号管理员可通过该账号新建子账号并针对子账号设定操作链码管理组织等权限
2. 未登录账号可通过账号密码登录并生成登录token
3. 已登录账号验证token登录状态
4. 用户退出登录清除token
5. 用户创建组织|节点加入组织操作通过Baas调度中心反馈的用户映射信息进行存储
6. 接受Baas调度中心推送的用户对某个通道进行的操作并与之前存储的映射信息进行比对，校验并反馈操作权限 
7. 维护一个消息列表保存加入组织等操作的推送信息

## 接口设计

- 登录
    * 入参：
    ```js
    { // body
        account: '' [VARCHART],
        password: '' [VARCHART]
    }
    ```
    * 出参：
    ```js
    {
        code: 200 INT,
        data:{
            token: '' [VARCHART]
        },
        msg: 'success' [VARCHART]
    }
    ```

- token校验
    * 入参：
    ```js
    { // header
        token: '' [VARCHART]
    }
    ```
    * 出参：
    ```js
    {
        code: 999 INT,
        data:{
            status: false [INT]
        },
        msg: 'token已过期' [VARCHART]
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
        code: 200 INT,
        data:{
            status: true [INT]
        },
        msg: 'success' [VARCHART]
    }
    ```

- 用户操作权限映射表存储 [待定]
    * 入参：
    ```js
    {   // header
        BASS-TOKEN: '' [VARCHART]
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
        BASS-TOKEN: '' [VARCHART]
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

- 更新推送列表
    * 入参：
    ```js
    {   // header
        BASS-TOKEN: '' [VARCHART]
        // body
        data: {
            fromId: 'formId', [VARCHART]
            toId: 'toId' , [VARCHART]
            type: 0 [VARCHART], // ['加入组织','退出组织']
            isRead: 0, [INT]
        }
    }
    ```
    * 出参：
    ```js
    {
        code: 200 INT,
        data: {
            list: [{
                type: 0 [VARCHART], // ['加入组织','退出组织']
                fromId: '' [VARCHART],
                fromName: '' [VARCHART],
                isRead: 0, [INT]
            }]
        },
        msg: 'success' [VARCHART]
    }
    ```

## 数据库表设计

- 用户信息表

```shell
 id | account  | password | 
----+----------+----------+----------------
 A1 | bob      | a1b23f2c | ...
 A2 | adam     | c0932f32 | ...
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

