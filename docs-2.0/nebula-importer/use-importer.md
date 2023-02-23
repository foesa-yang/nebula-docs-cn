# NebulaGraph Importer

NebulaGraph Importer（简称 Importer）是一款 [NebulaGraph](https://github.com/vesoft-inc/nebula) 的 CSV 文件单机导入工具。Importer 可以读取本地的 CSV 文件，然后导入数据至 NebulaGraph 图数据库中。

## 适用场景

Importer 适用于将本地 CSV 文件的内容导入至 NebulaGraph 中。

## 优势

- 轻量快捷：不需要复杂环境即可使用，快速导入数据。

- 灵活筛选：通过配置文件可以实现对 CSV 文件数据的灵活筛选。

## 更新说明

[Release notes](https://github.com/vesoft-inc/nebula-importer/releases/tag/{{importer.tag}})

## 前提条件

在使用 NebulaGraph Importer 之前，请确保：

- 已部署 NebulaGraph 服务。目前有三种部署方式：
  
  - [Docker Compose 部署](../4.deployment-and-installation/2.compile-and-install-nebula-graph/3.deploy-nebula-graph-with-docker-compose.md)
  
  - [RPM/DEB 包安装](../4.deployment-and-installation/2.compile-and-install-nebula-graph/2.install-nebula-graph-by-rpm-or-deb.md)
  
  - [源码编译安装](../4.deployment-and-installation/2.compile-and-install-nebula-graph/1.install-nebula-graph-by-compiling-the-source-code.md)

- NebulaGraph 中已创建 Schema，包括图空间、Tag 和 Edge type，或者通过参数`clientSettings.postStart.commands`设置。

- 运行 Importer 的机器已部署 Golang 环境。详情请参见 [Golang 环境搭建](https://github.com/vesoft-inc/nebula-importer/blob/{{importer.branch}}/docs/golang-install.md)。

## 操作步骤

配置 yaml 文件并准备好待导入的 CSV 文件，即可使用本工具向 NebulaGraph 批量写入数据。

### 下载二进制包运行

1. 在[Release](https://github.com/vesoft-inc/nebula-importer/releases/tag/{{importer.tag}})页面下载二进制包，并添加执行权限。

2. 启动服务。

  ```bash
  $ ./<binary_package_name> --config <yaml_config_file_path>
  ```

### 源码编译运行

1. 克隆仓库。

  ```bash
  $ git clone -b {{importer.branch}} https://github.com/vesoft-inc/nebula-importer.git
  ```

  !!! note
  
        请使用正确的分支。 
        NebulaGraph 2.x 和 3.x 的 rpc 协议不同。

2. 进入目录`nebula-importer`。

  ```bash
  $ cd nebula-importer
  ```

3. 编译源码。

  ```bash
  $ make build
  ```

4. 启动服务。

  ```bash
  $ ./nebula-importer --config <yaml_config_file_path>
  ```

  !!! note

        yaml 配置文件说明请参见下文的配置文件说明。

### 无网络编译方式

如果服务器不能联网，建议在能联网的机器上将源码和各种依赖打包上传到对应的服务器上编译即可，操作步骤如下：

1. 克隆仓库。

   ```bash
   $ git clone -b {{importer.branch}} https://github.com/vesoft-inc/nebula-importer.git
   ```

2. 使用如下的命令下载并打包依赖的源码。

   ```bash
   $ cd nebula-importer
   $ go mod vendor
   $ cd .. && tar -zcvf nebula-importer.tar.gz nebula-importer
   ```

3. 将压缩包上传到不能联网的服务器上。

4. 解压并编译。

   ```bash
   $ tar -zxvf nebula-importer.tar.gz 
   $ cd nebula-importer
   $ go build -mod vendor cmd/importer.go
   ```

### Docker 方式运行

使用 Docker 可以不必在本地安装 Go 语言环境，只需要拉取 NebulaGraph Importer 的[镜像](https://hub.docker.com/r/vesoft/nebula-importer)，并将本地配置文件和 CSV 数据文件挂载到容器中。命令如下：

```bash
$ docker run --rm -ti \
    --network=host \
    -v <config_file>:<config_file> \
    -v <csv_data_dir>:<csv_data_dir> \
    vesoft/nebula-importer:<version>
    --config <config_file>
```

- `<config_file>`：本地 yaml 配置文件的绝对路径。
- `<csv_data_dir>`：本地 CSV 数据文件的绝对路径。
- `<version>`：NebulaGraph 3.x 请填写`v3`。

!!! note
    建议使用相对路径。如果使用本地绝对路径，请检查路径映射到 Docker 中的路径。

## 配置文件说明

NebulaGraph Importer 通过`nebula-importer/examples/v2/example.yaml`配置文件来描述待导入文件信息、NebulaGraph 服务器信息等。用户可以参考示例配置文件：[无表头配置](config-without-header.md)/[有表头配置](config-with-header.md)。下文将分类介绍配置文件内的字段。

!!! note

    如果用户下载的是二进制包，请手动创建配置文件。

### 基本配置

示例配置如下：

```yaml
version: v2
description: example
removeTempFiles: false
```

|参数|默认值|是否必须|说明|
|:---|:---|:---|:---|
|`version`|v2|是|配置文件的版本。|
|`description`|example|否|配置文件的描述。|
|`removeTempFiles`|false|否|是否删除临时生成的日志和错误数据文件。|

### 客户端配置

客户端配置存储客户端连接 NebulaGraph 相关的配置。

示例配置如下：

```yaml
clientSettings:
  retry: 3
  concurrency: 10
  channelBufferSize: 128
  space: test
  connection:
    user: user
    password: password
    address: 192.168.11.13:9669,192.168.11.14:9669
  # # 只有 local_config 是 false 的情况下，才可以通过 UPDATE CONFIGS 更新配置
  # postStart:
  #   commands: |
  #     UPDATE CONFIGS storage:wal_ttl=3600;
  #     UPDATE CONFIGS storage:rocksdb_column_family_options = { disable_auto_compactions = true };
  #   afterPeriod: 8s
  # preStop:
  #   commands: |
  #     UPDATE CONFIGS storage:wal_ttl=86400;
  #     UPDATE CONFIGS storage:rocksdb_column_family_options = { disable_auto_compactions = false };
```

|参数|默认值|是否必须|说明|
|:---|:---|:---|:---|
|`clientSettings.retry`|3|否|nGQL 语句执行失败的重试次数。|
|`clientSettings.concurrency`|10|否|NebulaGraph 客户端并发数。|
|`clientSettings.channelBufferSize`|128|否|每个 NebulaGraph 客户端的缓存队列大小。|
|`clientSettings.space`|-|是|指定数据要导入的 NebulaGraph 图空间。不要同时导入多个空间，以免影响性能。|
|`clientSettings.connection.user`|-|是|NebulaGraph 的用户名。|
|`clientSettings.connection.password`|-|是|NebulaGraph 用户名对应的密码。|
|`clientSettings.connection.address`|-|是|所有 Graph 服务的地址和端口。|
|`clientSettings.postStart.commands`|-|否|配置连接 NebulaGraph 服务器之后，在插入数据之前执行的一些操作。|
|`clientSettings.postStart.afterPeriod`|-|否|执行上述`commands`命令后到执行插入数据命令之间的间隔，例如`8s`。|
|`clientSettings.preStop.commands`|-|否|配置断开 NebulaGraph 服务器连接之前执行的一些操作。|

### 文件配置

文件配置存储数据文件和日志的相关配置，以及 Schema 的具体信息。

#### 文件和日志配置

示例配置如下：

```yaml
workingDir: ./data/
logPath: ./err/test.log
files:
  - path: ./student.csv
    failDataPath: ./err/student
    batchSize: 128
    limit: 10
    inOrder: false
    type: csv
    csv:
      withHeader: false
      withLabel: false
      delimiter: ","
      lazyQuotes: false
```

|参数|默认值|是否必须|说明|
|:---|:---|:---|:---|
|`workingDir`|-|否|在多个目录包含具有相同文件结构的数据，使用此参数在多个目录之间切换。例如，下面配置的 `path` 和 `failDataPath` 的值会自动更改为 `./data/student` 和 `./data/err/student`。参数可以是绝对的或相对的。|
|`logPath`|-|否|导入过程中的错误等日志信息输出的文件路径。|
|`files.path`|-|是|数据文件的存放路径，如果使用相对路径，则会将路径和当前配置文件的目录拼接。可以使用星号（\*）进行模糊匹配，导入多个名称相似的文件，但是文件的结构需要相同。|
|`files.failDataPath`|-|是|插入失败的数据文件存放路径，以便后面补写数据。|
|`files.batchSize`|128|否|单批次插入数据的语句数量。|
|`files.limit`|-|否|读取数据的行数限制。|
|`files.inOrder`|-|否|是否按顺序在文件中插入数据行。如果为`false`，可以避免数据倾斜导致的导入速率降低。|
|`files.type`|-|是|文件类型。|
|`files.csv.withHeader`|`false`|是|是否有表头。详情请参见[关于 CSV 文件表头](#csv_header)。|
|`files.csv.withLabel`|`false`|是|是否有 LABEL。详情请参见[有表头配置说明](config-with-header.md)。|
|`files.csv.delimiter`|`","`|是|指定 csv 文件的分隔符。只支持一个字符的字符串分隔符。使用特殊字符做分隔符时需要进行转义。例如当分隔符为十六进制`0x03`即`Ctrl+C`时，转义的写法为：`"\x03"`或`"\u0003"`。关于 yaml 格式特殊字符转义的细节请参见[更多](https://yaml.org/spec/1.2.2/#escaped-characters)。|
|`files.csv.lazyQuotes`|`false`|否|LazyQuotes设置为真时，一个引号可能会出现在非引号字段中，一个非双引号可能会出现在引号字段中。|

#### Schema 配置

Schema 配置描述当前数据文件的 Meta 信息，Schema 的类型分为点和边两类，可以同时配置多个点或边。

- 点配置

示例配置如下：

```yaml
schema:
  type: vertex
  vertex:
    vid:
      index: 1
      concatItems: # "c1{index0}c2{index1}2"
        - "c1"
        - 0
        - c2
        - 1
        - "2"
      function: hash
      prefix: abc
    tags:
      - name: student
        props:
          - name: age
            type: int
            index: 2
          - name: name
            type: string
            index: 1
          - name: gender
            type: string
            defaultValue: "male"
          - name: phone
            type: string
            nullable: true
          - name: email
            type: string
            nullable: true
            nullValue: "__NULL__"
          - name: address
            type: string
            nullable: true
            alternativeIndices:
              - 7
              - 8
```

|参数|默认值|是否必须|说明|
|:---|:---|:---|:---|
|`files.schema.type`|-|是|Schema 的类型，可选值为`vertex`和`edge`。|
|`files.schema.vertex.vid.index`|-|否|点 ID 对应 CSV 文件中列的序号。|
|`files.schema.vertex.vid.concatItem`|-|否|用于连接两个或多个数组，连接项可以是`string`、`int`或者混合。`string`代表常量，`int`表示索引列。如果设置了`concatItem`，`index`参数将不生效。|
|`files.schema.vertex.vid.function`|-|否|生成 VID 的函数。目前，我们只支持 `hash` 函数。|
|`files.schema.vertex.vid.type`|-|否|点 ID 的数据类型，可选值为`int`和`string`。|
|`files.schema.vertex.vid.prefix`|-|否|给 原始vid 添加的前缀，当同时指定了 `function` 时, 生成 VID 的方法是先添加 `prefix` 前缀, 再用 `function`生成 VID。|
|`files.schema.vertex.tags.name`|-|是|Tag 名称。|
|`files.schema.vertex.tags.props.name`|-|是|Tag 属性名称，必须和 NebulaGraph 中的 Tag 属性一致。|
|`files.schema.vertex.tags.props.type`|-|是|属性数据类型，支持`bool`、 `int`、 `float`、 `double`、 `string`、 `time`、 `timestamp`、 `date`、 `datetime`、 `geography`、 `geography(point)`、 `geography(linestring)` 和 `geography(polygon)`。|
|`files.schema.vertex.tags.props.index`|-|否|属性对应 CSV 文件中列的序号。|
|`files.schema.vertex.tags.props.nullable`|`false`|否|属性是否可以为`NULL`，可选`true`或者`false`。|
|`files.schema.vertex.tags.props.nullValue`|`""`|否|`nullable`设置为`true`时，属性的值与`nullValue`相等则将该属性值设置为`NULL`。|
|`files.schema.vertex.tags.props.alternativeIndices`|-|否|当`nullable`为`false`时忽略。该属性根据索引顺序从 csv 中获取，直到不等于`nullValue`。|
|`files.schema.vertex.tags.props.defaultValue`|-|否当`nullable`为`false`时忽略。根据`index`和`alternativeIndices`获取的所有值为`nullValue`时设置默认值。|

!!! note
    CSV 文件中列的序号从 0 开始，即第一列的序号为 0，第二列的序号为 1。

- 边配置

示例配置如下：

```yaml
schema:
  type: edge
  edge:
    name: follow
    srcVID:
      index: 0
      function: hash
    dstVID:
      index: 1
      function: 
    rank:
      index: 2
    props:
      - name: grade
        type: int
        index: 3
```

|参数|默认值|是否必须|说明|
|:---|:---|:---|:---|
|`files.schema.type`|-|是|Schema 的类型，可选值为`vertex`和`edge`。|
|`files.schema.edge.name`|-|是|Edge type 名称。|
|`files.schema.edge.srcVID.index`|-|否|边的起始点 ID 对应 CSV 文件中列的序号。|
|`files.schema.edge.srcVID.function`|-|否|生成 VID 的函数。目前，我们只支持 `hash` 函数。|
|`files.schema.edge.dstVID.index`|-|否|边的目的点 ID 对应 CSV 文件中列的序号。|
|`files.schema.edge.dstVID.function`|-|否|生成 VID 的函数。目前，我们只支持 `hash` 函数。|
|`files.schema.edge.rank.index`|-|否|边的 rank 值对应 CSV 文件中列的序号。|
|`files.schema.edge.props.name`|-|是|Edge type 属性名称，必须和 NebulaGraph 中的 Edge type 属性一致。|
|`files.schema.edge.props.type`|-|是|属性类型，支持`bool`、`int`、`float`、`double`、`timestamp`、`string`和`geo`。|
|`files.schema.edge.props.index`|-|否|属性对应 CSV 文件中列的序号。|

## 关于 CSV 文件表头（header）

Importer 根据 CSV 文件有无表头，需要对配置文件进行不同的设置，相关示例和说明请参见：

- [无表头配置说明](config-without-header.md)

- [有表头配置说明](config-with-header.md)

## 视频

* [数据库导入工具——NebulaGraph Importer 简介](https://www.bilibili.com/video/BV1ny4y1u7i4)（3 分 09 秒）
<iframe src="//player.bilibili.com/player.html?aid=803505035&bvid=BV1ny4y1u7i4&cid=351250785&page=1&high_quality=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="720px" height="480px"> </iframe>
