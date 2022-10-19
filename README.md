目的：找到登陆时候密码password，加密的方法

1、要定位到password加密处有两种方式：

        1）、搜索parssword

        2）、通过通过newtwork-initiator，鼠标放上看发包的顺序

这里用第二种方式如图：
<img width="1713" alt="image" src="https://user-images.githubusercontent.com/85286900/196634650-0f2e63e4-3cbd-4825-9b1c-17dfe0a8b834.png">
send和ajax是ajax请求的不看，点到n.login

发现目标：password，下断点
<img width="1783" alt="image" src="https://user-images.githubusercontent.com/85286900/196634879-c01e0ba2-8116-48f0-bf4b-4e0efca880da.png">
