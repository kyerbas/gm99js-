目的：找到登陆时候密码password，加密的方法

1、要定位到password加密处有两种方式：

        1）、搜索parssword

        2）、通过通过newtwork-initiator，鼠标放上看发包的顺序

这里用第二种方式如图：
<img width="1713" alt="image" src="https://user-images.githubusercontent.com/85286900/196634650-0f2e63e4-3cbd-4825-9b1c-17dfe0a8b834.png">
send和ajax是ajax请求的不看，点到n.login

发现目标：password，下断点
<img width="1783" alt="image" src="https://user-images.githubusercontent.com/85286900/196634879-c01e0ba2-8116-48f0-bf4b-4e0efca880da.png">
重新提交账号密码登陆，断下来了

发现:function(t,e,i)的时候，密码还是明文：123456，但是经过：a.encode(t.password, s)，密码变成密文；所以加密过程就是在这之间；确定是a.encode，鼠标放到a.encode上面，进入它所在js，如图：
<img width="773" alt="image" src="https://user-images.githubusercontent.com/85286900/196839965-33390e52-4872-405e-83d6-f31d9ab95cdf.png">
进入后：
<img width="1788" alt="image" src="https://user-images.githubusercontent.com/85286900/196840048-0620df3e-623f-42d5-889d-feff259d5f40.png">
下断点：重新提交账号密码登陆，跟过来
<img width="1282" alt="image" src="https://user-images.githubusercontent.com/85286900/196840106-74966d44-6e6f-4c5d-bef2-eca2ab296a1c.png">
function(t,e)中，t还是密码的明文，但是this.jsencrypt.encrypt(i)后，变成密文了，确定 this.jsencrypt.encryp，这个就是加密方法，再点击进入,确定要扣的方法是：
<img width="1170" alt="image" src="https://user-images.githubusercontent.com/85286900/196840209-0d4ad427-ef41-422f-b7aa-83cdca58d7c6.png">
 到这一步就确定加密函数位置了，不要再进this.getKey().encrypt(t)这个函数看，这个函数里是加密的具体逻辑了，如果进去看了，心态会崩溃的诶

开始扣代码，由于这个js是webpack，先把它的头部模块加载器扣出来
```javascript
//头部模块加载器
!function(t) {
    function e(s) {
        if (i[s])
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
    var i = {};
    return e.m = t,
    e.c = i,
    e.p = "",
    e(0)
}(
```

然后补全删减，删减补全后

```javascript
//删减补全后
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
    //下面这段代码删掉
    var i = {};
    return e.m = t,
    e.c = i,
    e.p = "",
    e(0)
}({})//补全加自执行，这里可以放对象{}也可以放数组[]
 
//删减补全后案例1
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
}({})//补全加自执行，这里可以放对象{}也可以放数组[]
 
//删减补全后案例2
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
 
    function e(s) {
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
}({})//补全加自执行，这里可以放对象{}也可以放数组[]
 
//删减补全后案例2
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
 
    function e(s) {
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
}([])//补全加自执行，这里可以放对象{}也可以放数组[],放数组
```

开始扣代码：先把加密函数this.getKey().encrypt(t)所在的模块扣下来,

<img width="1185" alt="image" src="https://user-images.githubusercontent.com/85286900/196840730-12b7a1cb-d2a1-45ad-b09c-93da2fc5ad03.png">

根据这块代码往上找，找到所在模块开始位置
<img width="1651" alt="image" src="https://user-images.githubusercontent.com/85286900/196840809-9675f957-ab03-46c5-88b7-49b9873ded51.png">
把这个模块扣下来，放到上面删减好的模块加载器中，由于模块加载器的参数定义的是个数组，所以给刚扣下来的模块取个名字
```javascript
//删减补全后案例1
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
}({jsencry:function(t, e, i) {},
})//补全加自执行，这里可以放对象{}也可以放数组[]
```

然后回到最开始定位到加密位置的断点

<img width="772" alt="image" src="https://user-images.githubusercontent.com/85286900/196841015-85e5c80a-cb96-4316-a384-2a5426f86768.png">

a.encode,跟进去看，这个a.encode是如图的方法：

<img width="1779" alt="image" src="https://user-images.githubusercontent.com/85286900/196841138-590f6164-0daa-4c5c-83d8-913798862a4e.png">
把这个模块也扣下来
<img width="1246" alt="image" src="https://user-images.githubusercontent.com/85286900/196841259-78a44769-b207-4c80-983c-bc387d755924.png">
放到模块加载器，七个名字

```javascript
//删减补全后案例1
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
}({jsencry:function(t, e, i) {},
jiami:function(t, e, i) {},
})//补全加自执行，这里可以放对象{}也可以放数组[]
```

到这里还要补充下模块加载器的分析，不然下面的不知道怎么改

```javascript
//删减补全后案例1
!function(t) {//这个t参数就是自执行后面模块对象，现在只有一个名字叫3的模块
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        //t[s].call(n.exports, n, n.exports, e),
        //这个意思是:现在就是调用模块3:t[s]现在就表示模块3，call(n.exports, n, n.exports, e)中的参数是传给模块的参数n.exports是对象，n也是对象，e是e方法本身
        //所以在调用模块3的时候，模块3是3个参数(t, e, i),其中模块3中有段代码是
        //var r = i(4);这个i一看是个方法调用，i又是模块接收过来的参数
        //结合call(n.exports, n, n.exports, e)参数分析，只有e是方法，所以i就是e，那么i(4),意思就是调用的模块4，i(4)这种是调用数组形式的模块（就是自执行尾部是数组格式[]）,现在自己扣的时候，把自执行模块补全成了对象模式，所以这里也要改成i("4")这样去调用
        n.loaded = !0,
        n.exports
    }
}({    3: function(t, e, i) {
        var s;
        s = function(t, e, s) {
            function n() {
                "undefined" != typeof r && (this.jsencrypt = new r.JSEncrypt,
                this.jsencrypt.setPublicKey("-----BEGIN PUBLIC KEY-----MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDq04c6My441Gj0UFKgrqUhAUg+kQZeUeWSPlAU9fr4HBPDldAeqzx1UR92KJHuQh/zs1HOamE2dgX9z/2oXcJaqoRIA/FXysx+z2YlJkSk8XQLcQ8EBOkp//MZrixam7lCYpNOjadQBb2Ot0U/Ky+jF2p+Ie8gSZ7/u+Wnr5grywIDAQAB-----END PUBLIC KEY-----"))
            }
            var r = i(4);//这里的i方法，是本模块接收过来的参数i，结合上面的分析，i方法就是模块加载器中的e方法，所以i(4)的意思是，调用模块4（模块是存在数组格式中，所以用数字下标记），现在自己扣的时候，把模块加载器自执行补全成对象格式，所以这里调用模块4的时候改成i("4")
            n.prototype.encode = function(t, e) {
                var i = e ? e + "|" + t : t;
                return encodeURIComponent(this.jsencrypt.encrypt(i))
            }
            ,
            s.exports = n
        }
        .call(e, i, e, t),
        !(void 0 !== s && (t.exports = s))
    }},
    4:function(t, e, i) {})//补全加自执行，这里可以放对象{}也可以放数组[]
```

再看加密这个模块中的代码

<img width="1246" alt="image" src="https://user-images.githubusercontent.com/85286900/196841559-ae43d1d5-28f3-496a-ae31-b432e6b38cd0.png">

扣好之后，需要在自执行代码外定义一个变量，用来接收，导出的方法e,这样就可以在外部调用加密函数

```javascript
//删减补全后案例1
var _jiami = "";//定义全局变量，用来接收导出的方法
!function(t) {
 
    //由于下面i用到了，所以定义一个空对象i，当然也可以把调用到i的代码删掉
    var i ={};
    function e(s) {
        if (i[s])//这段调用i的代码可以删掉的
            return i[s].exports;
        var n = i[s] = {
            exports: {},
            id: s,
            loaded: !1
        };
        return t[s].call(n.exports, n, n.exports, e),
        n.loaded = !0,
        n.exports
    }
   _jiami = e('jiami'); //因为网站是最开始a.encode最开始调用加密函数的代码跟到模块，来加密的，所以直接导出e('jiami')这个模块的调用
}({jsencry:function(t, e, i) {},//模块4，就是加密函数所在模块，命名为jsencry
jiami:function(t, e, i) {//模块3，就是a.encode最开始调用加密函数的代码跟到模块，命名为加密
        var s;
        s = function(t, e, s) {
            function n() {
                "undefined" != typeof r && (this.jsencrypt = new r.JSEncrypt,
                this.jsencrypt.setPublicKey("-----BEGIN PUBLIC KEY-----MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDq04c6My441Gj0UFKgrqUhAUg+kQZeUeWSPlAU9fr4HBPDldAeqzx1UR92KJHuQh/zs1HOamE2dgX9z/2oXcJaqoRIA/FXysx+z2YlJkSk8XQLcQ8EBOkp//MZrixam7lCYpNOjadQBb2Ot0U/Ky+jF2p+Ie8gSZ7/u+Wnr5grywIDAQAB-----END PUBLIC KEY-----"))
            }
            //var r = i(4);//原来webpack中，是所有模块是存在数组中的，所以用数组下标，现在改成了对象形式，要改成下面这样调用
            var r = i("jsencry");
            n.prototype.encode = function(t, e) {
                var i = e ? e + "|" + t : t;
                return encodeURIComponent(this.jsencrypt.encrypt(i))
            }
            ,
            s.exports = n
        }
})//补全加自执行，这里可以放对象{}也可以放数组[]
```
