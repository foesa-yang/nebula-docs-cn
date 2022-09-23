# 使用 NebulaGraph Dashboard 备份和恢复 NebulaGraph 数据

为了防止操作失误或者系统故障导致数据丢失，我们需要经常对数据进行备份，NebulaGraph 提供 Backup & Restore（BR）工具可帮助用户备份和恢复图数据。NebulaGraph Dashboard 企业版集成了 BR 能力并提供简单的 UI，用户只需几步即可进行数据备份和恢复操作。本文介绍如何使用 NebulaGraph Dashboard 备份和恢复 NebulaGraph 数据。

## 使用限制

- 目前仅支持将数据备份至兼容 S3 协议的云存储中（例如阿里云对象存储 OSS、MinIO、Ceph RGW 等），不支持备份至本地。

  !!! note

        如需备份至本地，请参见[备份与恢复](../../../backup-and-restore/nebula-br/1.what-is-br.md)。

- 目前仅支持数据的全量备份和全量恢复，不支持增量备份和增量恢复。
- 不支持图空间级别数据的备份和恢复。
- 备份数据只能被恢复至原集群，不能跨集群恢复数据。
- 不支持断点续传备份和恢复数据。
- 目前仅支持查看备份和恢复操作产生的日志。

## 前提条件

- [使用 Dashboard 创建 NebulaGraph 集群](../../3.create-import-dashboard/1.create-cluster.md)。
- 开通兼容 S3 协议的云存储服务并创建存储桶。具体操作，参见对应云存储服务的文档。
  
## 操作步骤

### 入口

1. 在 NebulaGraph Dashboard 企业版顶部导航栏，单击**集群管理**。
2. 在目标集群右侧**详情**。
3. 在左侧导航栏，单击**集群信息**->**备份恢复**。
   
### 备份数据

用户通过创建备份文件的方式将备份的数据存至云存储服务中，操作如下：

1. 在**备份恢复**页面，单击**备份列表**。
2. 在页面右上角，单击 **S3 配置**。
3. 填写对应的云存储的配置信息，并单击**提交**。
   
  | 配置项        | 说明                                                         |
  | ------------- | ------------------------------------------------------------ |
  | s3.access_key | Access Key ID，用于标识用户，例如，`AKIAI44QH8DHBxxxx`。          |
  | s3.endpoint   | 表示云存储服务对外服务的访问域名 URL，例如`https://s3.us-east-2.amazonaws.com`。不支持带`bucket_name`的 URL，例如`https://{bucket_name}.s3.us-west-2.amazonaws.com`。 |
  | s3.region     | 数据中心所在的物理位置，例如`us-east-1`。                    |
  | s3.secret_key | Access Key Secret，用于加密签名字符串和用来验证签名字符串的密钥，例如`je7MtGbClwBF/2Zp9Utk/h3yCoxxxx`。 |
  | storage path  | 数据的存储路径，**路径必须以`s3`开头**，例如`s3://br-test/backup/`。                 |      

  以下列举阿里云对象存储 OSS 和 Amazon S3 的示例配置信息：

  - Amazon S3 的示例配置如下：

    ![amazon_s3](https://docs-cdn.nebula-graph.com.cn/figures/amazon_s3_2022-06-20_14-29-11.png)

  - 阿里云对象存储 OSS 的示例配置如下：

    ![aliyun_oss](https://docs-cdn.nebula-graph.com.cn/figures/br_s3_aliyun_2022-06-20_12-07-37.png)

  !!! caution
      备份数据至阿里云 OSS 时，需将备份路径中的`oss`替换为`s3`，例如将`oss://nebula-br-test/`改成`s3://nebula-br-test/`。
  
4. 在页面右上角，单击**创建备份**。
5. 在**创建备份**页面，单击**检查环境**检查相关配置是否正常运行，然后单击**提交**。

  环境检查包括：

  - NebulaGraph 需正常运行。
  - 登录云存储服务的密钥未过期。
  - 业务流量。仅检查用户业务的 QPS 是否为 0，当 QPS 不为 0 时，页面提示用户在业务流量低峰期进行备份操作。

  !!! note

        如果 NebulaGraph 运行异常或者云存储服务的登录信息有误，用户将无法提交备份。
  
6. 在备份列表中查看创建的备份文件。
  ![br_list](https://docs-cdn.nebula-graph.com.cn/figures/brlist_2022-06-27_14-23-24_cn.png)

  - 备份列表中默认显示 30 天内创建的备份，展示备份名称、时间、状态、图空间、存储方式、存储路径、相关操作。
    - 备份状态包括`running`、`success`、`failed`。
    - 相关操作包括恢复备份、查看恢复历史、查看备份操作产生的日志、删除备份。
  - 用户可以通过设置筛选条件（创建时间和状态）或者搜索备份名称查看创建的备份。

  !!! note

        在上一次备份操作未完成前，不能再次执行备份操作。

7. 检查存储服务中是否有上述创建的备份文件。
  成功创建的备份会被存储至上述用户设置的存储路径中，例如`s3://nebula-br-test`。

  - Amazon S3: 
  ![br_s3_path](https://docs-cdn.nebula-graph.com.cn/figures/br_s3_2022-06-20_12-07-37.png)
  - 阿里云 OSS:
  ![aliyun_oss_backup_cn](https://docs-cdn.nebula-graph.com.cn/figures/aliyun_oss_backup_2022-06-27_13-52-23_cn.png)

  !!! danger

        请勿修改生成备份文件信息，包括名称、保存路径等，否则将无法恢复数据。


### 恢复数据

用户将备份数据存储至云存储服务后，可以恢复备份的数据至图数据库中。

!!! caution

    - 恢复数据前，请确认保存至云存储服务中的备份文件的名称、存储路径等无改动，否则数据恢复会失败。
    - 恢复数据的过程中，集群中的所有数据会被移除，且替换为备份文件中的数据。
    - 恢复过程为离线执行，用户在恢复数据的过程中无法进行其他操作。

按照以下步骤执行恢复操作：

1. 在**备份恢复**页面，单击**备份列表**。
2. 在目标备份文件右侧，单击**恢复**。
3. 单击**检查环境**，环境检查通过后，单击**提交**。
   
  环境检查包括：

  - NebulaGraph 需正常运行。
  - 登录云存储服务的密钥未过期。
  - 无业务流量的进入。
  
4. 在**恢复列表**页面，查看恢复记录。
  ![restore_list](https://docs-cdn.nebula-graph.com.cn/figures/restorelist_2022-06-27_14-23-24_cn.png)
   
  - 恢复记录不可被删除。 
  - 恢复列表中默认显示 30 天内创建的恢复记录
  - 恢复记录展示记录 ID、备份名称、恢复状态、恢复时间、恢复的图空间、存储路径、操作人、及查看恢复操作产生的日志。
  - 恢复状态包括`running`、`success`、`failed`。

    !!! note

        在上一次恢复操作未完成前，不能再次执行恢复操作。

  - 用户可以通过设置筛选条件（创建时间和状态）或者搜索备份名称查看恢复记录。

