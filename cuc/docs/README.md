

# 官方文档学习参考

## 简单交换机
Source: http://ryu.readthedocs.io/en/latest/writing_ryu_app.html

`l2.py` 作所有数据的FLOOD操作. 修改为 OF1.3版本

这个交换机很傻(总是FLOOD) , 到 `ryu/app` directory and `integrated tests` directory 可以找到更多资源

## ryu event 事件处理
Source: http://ryu.readthedocs.io/en/latest/ryu_app_api.html
学习处理 ryu 的事件机制. 每个app是一个单线程. 通过set_ev_cls 注册相关事件进行处理.  
执行下面的命令可以触发事件EventOFPPortStateChange
```
mininet> s1 ifconfig s1-eth2 up
```
可以在不同的app中注册不同的handle, 如果都注册, 则app都会收到消息

```
#启动两个监听器app
./bin/ryu-manager --log-config-file=cuc/logging_config.ini  cuc/docs/l2.py cuc/docs/l2_events.py
# 执行命令触发以下事件与处理 mininet> h1 ping h2
(handle by l2.py) EventOFPPacketIn: <ryu.controller.ofp_event.EventOFPPacketIn object at 0x10e184310>
# 执行命令触发以下事件与处理 mininet> s1 ifconfig s1-eth2 up
(handle by l2_events.py) EventOFPPortStateChange: <ryu.controller.ofp_event.EventOFPPortStateChange object at 0x10e16fdd0>
```


## 拓扑查看器
参考: http://ryu.readthedocs.io/en/latest/gui.html

在mininet中运行
```
sudo mn --controller remote,ip=192.168.57.2 --topo tree,depth=3
```

再运行ryu命令
```
PYTHONPATH=. ./bin/ryu run --observe-links ryu/app/gui_topology/gui_topology.py
```

(TODO: 分析 run --observe-links 的代码调用路径.)  

打开浏览器 http://localhost:8080/ 效果如下:

![](http://ryu.readthedocs.io/en/latest/_images/gui.png)