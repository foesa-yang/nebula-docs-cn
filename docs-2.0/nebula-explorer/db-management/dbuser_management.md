# 数据库用户管理

NebulaGraph Explorer 支持管理数据库用户，包括创建用户、删除用户、修改密码等。

## 前提条件

登录 Explorer 的用户需要拥有相关操作的权限。例如`God`权限的用户可以执行所有操作，`Admin`权限的用户可以将权限内的图空间授权给其他用户。关于角色权限详情，参见[角色权限](../..//7.data-security/1.authentication/3.role-list.md)。

## 入口

在顶部导航栏里，点击 ![db_user_management](https://docs-cdn.nebula-graph.com.cn/figures/db_user_management_221024.png) 图标。

![dbuser_management](https://docs-cdn.nebula-graph.com.cn/figures/explorer_dbuser_management_221024_cn.png)

## 创建用户

!!! note

    仅`root`用户可以创建用户。

1. 在标签**用户列表**内单击**创建用户**，设置如下参数。

  |参数|说明|
  |:--|:--|
  |账号|用户名。|
  |密码|用户名对应密码。|
  |IP 白名单|用户只有通过列表内的 IP 才能连接 NebulaGraph。多个 IP 之间用英文逗号（,）分隔。仅 NebulaGraph 企业版支持该参数。|

  !!! note

        单击左上角**添加**，可以批量创建用户。

2. 单击**确认**。

## 授权用户

1. 切换至标签**授权**，在左上角选择需要授权给用户的图空间名称。该页面会展示拥有该图空间权限的所有用户（`root`用户除外）。

2. 单击**授权用户**，设置如下参数。

  |参数|说明|
  |:--|:--|
  |用户名|设置需要授权的用户名。如果登录的是`root`用户，可以在下拉菜单中选择用户；如果是`Admin`权限的用户，需要手动填写用户名。|
  |角色|在下拉菜单中选择要授权的角色。关于角色权限详情，参见[角色权限](../..//7.data-security/1.authentication/3.role-list.md)。|

3. 单击**确认**。

## 用户列表其他操作

!!! note

    仅`root`用户可以查看**用户列表**。

- 查看：查看用户在各个空间内的权限。
- 修改密码：修改用户对应的密码。修改时不需要提供旧密码。非`root`用户无法查看**用户列表**，可以在页面右上角的![clear_connection](https://docs-cdn.nebula-graph.com.cn/figures/session_221024.png)图标内修改密码。
- 删除用户：仅`root`用户可以删除其他用户。
- 搜索账号：通过关键字搜索账号。

## 授权列表其他操作

- 编辑：修改用户的角色。
- 取消授权：撤销用户的角色。
- 搜索账号：通过关键字搜索账号。

!!! note

    修改或撤销角色后，对应的用户需要重新登录才能生效。
