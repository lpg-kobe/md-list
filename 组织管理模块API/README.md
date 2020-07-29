# *组织管理API SERVICE*

## 流程说明

1. 用户通过创建组织按钮进入页面自定义组织配置并创建，创建组织后生成唯一的组织id，组织首页默认展示所有组织列表
2. 用户点击组织列表单条信息可以查看该组织参与的所有联盟列表，包括审批中以及审批完成的申请加入的联盟，审批中的加入联盟申请可以点击查看审批详情，展示相关审批的组织列表以及审批说明
3. 组织与组织之间可以建立通道，俗称账本，账本之间相互隔离且默认不加入该组织下的节点到任何联盟或者通道，可以通过组织id查询该组织加入的账本列表
4. 单条组织默认展示所有该组织下的peer节点列表，可以对单个节点的账本进行管理，决定是否加入某个联盟下的账本，节点加入账本同时将所有账本中的数据筛选出来组装更新到节点加入的账本表中，节点账本的申请加入跟退出需要发送验证码确认

## 接口设计

- 统一header请求头
    ```js
    {
        BAAS-TOKEN: '' // 用户token
    }
    ```

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

- 获取组织列表
    * 入参：
    ```js
    data: {}
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list: [{
                id: '' [String] // 递增id
                org_id: '' [String] // 组织id
                org_name: '' [String] // 组织名称
                org_domain: '' [String] // 组织域名
                org_nodes: '' [Number] // 组织节点个数，包含anchor锚节点、leader节点、enbase背书节点、peer节点
                org_size: '' [String] // 组织存储空间
                org_area: '' [String] // 组织地域
                org_format: 0 [Number] // 组织规格 Hyperledger Fabric（1.4.6）
                org_tag: 0 [Number] // 组织标签 0基础版 1企业版 2企业安全版
                org_peers: '' [Number] // 组织peers节点个数
                org_channels: '' [Number] // 加入的账本个数
                org_users: '' [Number] // 用户数量
                org_status: '' [Number] // 组织状态 0创建中 1创建成功 2创建失败
                org_create_time: '' [String] // 组织创建日期
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 创建组织
    * 入参：
    ```js
    data: {
        org_format: 0 [Number] // 组织规格 Hyperledger Fabric（1.4.6）
        org_tag: 0 [Number] // 组织标签 0基础版 1企业版 2企业安全版
        org_name: '' [String] // 组织名称
        org_domain: '' [String] // 组织域名
        org_nodes: '' [Number] // 组织节点个数
        org_peers: '' [Number] // 组织peers节点个数
        org_size: '' [String] // 组织存储空间
        org_area: '' [String] // 组织地域
        org_status: '' [String] // 组织状态，默认创建中0
        org_create_time: '' [String] // 组织创建日期，默认提交时的时间戳
    }
    ```
    * 出参：
    ```js
    data: {
        code: 200,
        data: {
            org_id: '' // 返回的组织id
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取组织详情
    * 入参：
    ```js
    data: {
        id: '' // 组织id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            id: '' [String] // 递增id
            org_id: '' [String] // 组织id
            org_name: '' [String] // 组织名称
            org_domain: '' [String] // 组织域名
            org_nodes: '' [Number] // 组织节点个数，包含anchor锚节点、leader节点、enbase背书节点、peer节点
            org_size: '' [String] // 组织存储空间
            org_area: '' [String] // 组织地域
            org_format: 0 [Number] // 组织规格 Hyperledger Fabric（1.4.6）
            org_tag: 0 [Number] // 组织标签 0基础版 1企业版 2企业安全版
            org_peers: '' [Number] // 组织peers节点个数
            org_channels: '' [Number] // 加入的账本个数
            org_users: '' [Number] // 用户数量
            org_status: '' [Number] // 组织状态 0创建中 1创建成功 2创建失败
            org_create_time: '' [String] // 组织创建日期
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取组织加入的联盟列表
    * 入参：
    ```js
    data: {
        id: '' // 组织id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list:[{
                id: [Number] // 递增id
                league_id: [String] // 联盟id   
                league_name: [String] // 联盟名称   
                league_domain: [String] // 联盟域名   
                league_status: [Number] // 联盟状态 0审批中 1已完成 2被退回
                league_create_time: [String] // 联盟创建时间
                league_join_time: [String] // 联盟加入时间
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取该组织所属联盟参与审批的组织列表
    * 入参：
    ```js
    data: {
        id: '' // 组织id
        league_id: '' // 联盟id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list:[{
                id: [Number] // 递增id
                org_id: [String] // 组织id   
                org_name: [String] // 组织域名   
                org_status: [Number] // 组织审批状态 0审批中 1已通过 2已拒绝   
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取该组织加入的账本列表
    * 入参：
    ```js
    data: {
        id: '' // 组织id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list:[{
                id: [Number] // 递增id
                channel_id: [String] // 账本id   
                channel_name: [String] // 账本名称   
                belong_league_id: [String] // 所属联盟id 
                belong_league_name: [String] // 所属联盟名称   
                belong_league_domain: [String] // 所属联盟域名   
                channel_create_time: [String] // 创建账本时间
                channel_join_time: [String] // 加入账本时间 
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取该组织的peer节点
    * 入参：
    ```js
    data: {
        id: '' // 组织id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list:[{
                id: [Number] // 递增id
                peer_id: [String] // 节点id   
                peer_network_ip: [String] // 节点公网ip   
                peer_domain: [String] // 节点域名   
                peer_port: [Number] // 节点端口   
                peer_create_time: [String] // 节点创建时间   
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取peer节点加入的账本channel列表
    * 入参：
    ```js
    data: {
        id: '' // peer节点id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list:[{
                id: [Number] // 递增id
                channel_id: [String] // 账本id   
                channel_name: [String] // 账本名称   
                belong_league_id: [String] // 所属联盟id 
                belong_league_name: [String] // 所属联盟名称     
                channel_create_time: [String] // 创建账本时间
                channel_join_time: [String] // 加入账本时间 
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 更新节点加入的账本channel列表
    * 入参：
    ```js
    data: {
        id: '' // 节点id
        join_ids: 'id,id' // 加入的账本id
    }
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {},
        msg: 'success' [String] // 返回信息说明
    }
    ```

- 获取所有账本channel列表
    * 入参：
    ```js
    data: {}
    ```
    * 出参：
    ```js
    {
        code: 200, // 状态码
        data: {
            list: [{
                id: [Number] // 递增id
                channel_id: [String] // 账本id   
                channel_name: [String] // 账本名称   
                belong_league_id: [String] // 所属联盟id 
                belong_league_name: [String] // 所属联盟名称 
                belong_league_domain: [String] // 所属联盟域名      
                channel_create_time: [String] // 创建账本时间
                channel_join_time: [String] // 加入账本时间 
            }]
        },
        msg: 'success' [String] // 返回信息说明
    }
    ```


## 数据库表设计

----------to do-------------
