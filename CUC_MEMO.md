记录CUC代码与备忘
===============

使用源代码目录调试本地程序
```
PYTHONPATH=. ./bin/ryu-manager --enable-debugger --verbose ryu/app/simple_switch_13.py
```

运行之后在console中可以看到以下的提示信息

```
(CUC) debugging is available (--enable-debugger option is turned on)
```


==创建Qos交换机app
REF: https://osrg.github.io/ryu-book/en/html/rest_qos.html

```
cd ryu/ryu/app/
cp simple_switch_13.py qos_simple_switch_13.py #复制1.3app
cd ../../..
sed '/OFPFlowMod(/,/)/s/)/, table_id=1)/' ryu/ryu/app/simple_switch_13.py > ryu/ryu/app/qos_simple_switch_13.py
cd ryu/
PYTHONPATH=. ryu-manager ryu.app.rest_qos ryu.app.qos_simple_switch_13 ryu.app.rest_conf_switch
```
