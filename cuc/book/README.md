# Ryu Book 代码

参考： https://osrg.github.io/ryu-book/zh_tw/html/index.html


## 1. `simple_switch_13.py` 交換器（ Switching Hub ）
对应章节代码
## 2. `simple_monitor.py` 流量監控（ Traffic Monitor ）
对应章节代码
## 3. `simple_switch_rest_13.py` REST API

对应章节代码， REST 操作 mac 表命令

```
# 插入mac表
curl -X PUT -d '{"mac" : "00:00:00:00:00:01", "port" : 1}' http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001
curl -X PUT -d '{"mac" : "00:00:00:00:00:02", "port" : 2}' http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001

# 取回mac表
curl -X GET http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001
```

预插入 2条 mac 表后， h1 ping h2 只有一个 arp 产生的 `pack_in (packet in 1 00:00:00:00:00:01 ff:ff:ff:ff:ff:ff 1)`, 而不是 3个 pack_in 消息。

TODO: 结合例子2, 实现REST monitor 查询

## 4. `simple_switch_lacp_13.py` 網路聚合（ Link Aggregation ）

对应章节代码

ryu自带代码 `ryu/appsimple_switch_lacp.py`是1.0版本, 这里是1.3版本, 需要mininet和linux bonding配合, 未测试.

## 5.  生成樹（ Spanning Tree ）
这里无源码, app代码位于自带目录 `ryu/app/simple_switch_stp.py`  中.

## 6.  OpenFlow 通訊協定

源码位于ryu自带库目录 `ryu/ofproto/ofproto_v1_3.py`， Instruction中包含指定該封包到所定義的 meter table中. 对 `ip_dscp` `ecn` `mpls_tc` 等协议字段进行定义。

## 7. ofproto 函式庫¶

这个部分比较重要，清晰之后对OF协议以及Ryu的协议实现会有更好理解。
台式中文的翻译可能不如直接阅读英文原文。

 每個 OpenFlow（ 版本 X.Y ）都有相對應的常數模組（ ofproto_vX_Y ）和解析模組（ ofproto_vX_Y_parser ）每個 OpenFlow 版本的實作基本上是獨立的。

 OpenFlow 版本|	常數模块（消息常量）|	解析模块（解析消息）
 ----|-------------------------|--------
1.0.x|	ryu.ofproto.ofproto_v1_0|	ryu.ofproto.ofproto_v1_0_parser
1.2.x|	ryu.ofproto.ofproto_v1_2|	ryu.ofproto.ofproto_v1_2_parser
1.3.x|	ryu.ofproto.ofproto_v1_3|	ryu.ofproto.ofproto_v1_3_parser
1.4.x|	ryu.ofproto.ofproto_v1_4|	ryu.ofproto.ofproto_v1_4_parser

## 8. `icmp_responder.py` 封包函式庫
以 `icmp_responder.py` 为例，介绍ryu的包封装函数库。 OpenFlow 中 Packet-In 和 Packet-Out 訊息是用來產生封包，可以在當中的欄位放入 Byte 資料並轉換為原始封包的方法。Ryu 提供了相當容易使用的封包產生函式庫給應用程式使用。 具体内容可参考 [API文档][1]

[1]: http://ryu.readthedocs.io/en/latest/ryu_app_api.html

## 8. OF-Config 函式庫

OF-Config 是用來管理 OpenFlow 交換器的一個通訊協定。 OF-Config 通訊協定被定義在 NETCONF（ RFC 6241 ）的標準中，它可以對邏輯交換器的通訊埠（ Port ）和佇列（ Queue ）進行設定以及資料擷取。

```python
from ryu.lib.of_config.capable_switch import OFCapableSwitch
#使用 SSH Transport 連線到交換器。 回呼（ callback ）函式 unknown_host_cb 是用來對應未知的 SSH Host Key 時所被執行的函式。 下面的範例中我們使用無條件信任對方並繼續進行連結。
sess = OFCapableSwitch(
    host='localhost',
    port=1830,
    username='linc',
    password='linc',
    unknown_host_cb=lambda host, fingeprint: True)
#使用 NETCONF GET 來取得交換器的狀態
csw = sess.get()
for p in csw.resources.port:
    print p.resource_id, p.current_rate    
```

Book 中未提供完整的参考代码。 可参考 `ryu/tests/integrated/test_of_config.py` 测试代码, 以及 `/ryu/cmd/of_config_cli.py` 命令行代码中对类 `OFCapableSwitch`的使用

## 9. 防火牆（ Firewall ）
源码位于ryu自带库目录 `ryu/app/rest_firewall.py`
启动命令
```
ryu-manager ryu.app.rest_firewall
```

TODO: 这里的防火墙需要在两个方向上作flow管理, 并不支持TCP会话管理.

## 10. 路由器（ Router ）¶

源码位于ryu自带库目录 `ryu/app/rest_router.py`
启动命令
```
python bin/ryu-manager ryu/app/rest_router.py
```
例子1容易理解, 是标准的路由器
- 設定交換器 s1 的 IP 位址為「172.16.20.1/24」和「172.16.30.30/24」。这里设定的ip在什么位置上呢?

例子2则加入的vlan内容.
- 在一个vlan对应的是一个租户, 每个租户下存在不同路由策略与子网
- 这里区别与过去的vlan. 过去的一个vlan对应的是一个子网, 而不是一个租户
