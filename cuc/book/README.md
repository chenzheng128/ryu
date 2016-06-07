# Ryu Book 代码

参考： https://osrg.github.io/ryu-book/zh_tw/html/index.html


1. `simple_switch_13.py` 交換器（ Switching Hub ）

2. `simple_monitor.py` 流量監控（ Traffic Monitor ）

3. `simple_switch_rest_13.py` REST API

```
# 插入mac表
curl -X PUT -d '{"mac" : "00:00:00:00:00:01", "port" : 1}' http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001
curl -X PUT -d '{"mac" : "00:00:00:00:00:02", "port" : 2}' http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001

# 取回mac表
curl -X GET http://192.168.57.2:8080/simpleswitch/mactable/0000000000000001
```

预插入 2条 mac 表后， h1 ping h2 只有一个 arp 产生的 `pack_in (packet in 1 00:00:00:00:00:01 ff:ff:ff:ff:ff:ff 1)`, 而不是 3个 pack_in 消息。 
