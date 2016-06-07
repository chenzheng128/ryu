# 记录CUC代码与备忘


## 建立保存安装环境
`pip freeze > requirements.txt`

## 安装与设置
```
#创建venv
cd <ryu_root>
python -m virtualenv venv
source venv/bin/active
#源代码安装 repoze.lru-0.6 , oslo.config-3.9.0
cd ~/Downloads/repoze.lru-0.6; sudo python setup.py install
cd ~/Downloads/oslo.config-3.9.0; sudo python setup.py install

#安装两个额外库
sudo pip install lxml paramiko
#安装依赖库(老的 wiki tutorial 只提到这几个库，其实还有很多）
sudo pip install eventlet routes webob

#安装ryu依赖库
sudo pip install ryu

#源代码下载
git clone  http://...
#python setup.py install
git pull #更新代码
```

## 使用源代码目录调试本地程序
```
PYTHONPATH=. ./bin/ryu-manager --enable-debugger --verbose ryu/app/simple_switch_13.py
```

运行之后在console中可以看到以下的提示信息

```
(CUC) debugging is available (--enable-debugger option is turned on)
```

## 创建Qos交换机app

REF: https://osrg.github.io/ryu-book/en/html/rest_qos.html

```
cd ryu/ryu/app/
cp simple_switch_13.py qos_simple_switch_13.py #复制1.3app
cd ../../..
sed '/OFPFlowMod(/,/)/s/)/, table_id=1)/' ryu/ryu/app/simple_switch_13.py > ryu/ryu/app/qos_simple_switch_13.py
cd ryu/
PYTHONPATH=. ryu-manager ryu.app.rest_qos ryu.app.qos_simple_switch_13 ryu.app.rest_conf_switch
```
