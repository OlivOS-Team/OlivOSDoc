# 开始使用
## 发布版本
### Windows
我们推荐`Windows`用户直接使用经过二进制编译的发布版本，你可以在这里获取最新版本：[releases](https://github.com/OlivOS-Team/OlivOS/releases)

## 源码版本
请确保你已经安装了Python3以及git，当前我们主要使用`3.7.5`以及`3.11.0`两个版本的Python。  

+ 首先，克隆本项目的源码  
```bash
git clone https://github.com/OlivOS-Team/OlivOS
cd OlivOS
```
+ 安装依赖环境
```bash
pip install -r requirements310_pure.txt
```
对于`3.7.5`版本的Python，有单独提供的依赖描述文件
```bash
pip install -r requirements_pure.txt
```
+ 启动环境
```
python main.py
```

## Pypi版本
OlivOS的核心组件部分已经被充分集成，并作为Pypi库进行了发布，你同样也可以使用`pip`来获取这个版本并将OlivOS作为库加以使用。  
要获取最新版本的OlivOS库，你需要
```bash
pip install olivos
```
以下是一个简单的OlivOS启动示例代码
```python
import OlivOS

if __name__ == '__main__':
    OlivOSTarget = OlivOS.bootAPI.Entity()
    OlivOSTarget.start()
```
