### ajax 实际项目中请求的注意：
   - 请求超时设置
   ``` javascript
    var ajaxTimeOutLuckyDraw = $.ajax({
                                type: 'GET',
                                cache: false,
                                async: true, // 使用异步加载
                                url: lotteryRandom,
                                timeout: 10000,
                                data: {
                                    roomId: roomId,
                                    rowId: rowId,
                                    cutType: 'x' + str
                                },
                                headers: {
                                    "token": token
                                },
                                complete: function(XMLHttpRequest, status) { //当请求完成时调用函数
                                    // console.log(XMLHttpRequest, status);
                                    if (status == 'timeout') { 
                                    //status == 'timeout'意为超时,status的可能取值：success,notmodified,nocontent,error,timeout,abort,parsererror 
                                        hideLoadingDialog(null);
                                        ajaxTimeOutLuckyDraw.abort(); //取消请求
                                        bridge.call("app.showMsg", '请求超时，请稍后重试！');
                                    }
                                },
                                success: function(res) {
                                    hideLoadingDialog(null);
                                    chouzhongPrize = (JSON.parse(res)).data;
                                    chouzhongAllPrizeManys = (JSON.parse(res));
                                    console.log(chouzhongAllPrizeManys);
                                    if (chouzhongAllPrizeManys.status == 0) {
                                        // 抽中时执行事件：
                                        toSuccessLuckyDraw();
                                        roll(); //转圈过程不响应click事件，会将click置为false
                                        click = true; //一次抽奖完成后，设置click为true，可继续抽奖
                                        return false;
                                    } else if (chouzhongAllPrizeManys.status == 400) {
                                        // 钻石不足 $("#showLuckyDraw").fadeIn();
                                        $('.showIsNoMoneyToast').fadeIn();
                                        $('.showIsNoMoneyToast .waiButton input').eq(0).off('click').on('click', function() {
                                            $(".showIsNoMoneyToast").fadeOut();
                                        })
                                        $('.showIsNoMoneyToast .waiButton input').eq(1).off('click').on('click', function() {
                                            $(".showIsNoMoneyToast").fadeOut();
                                            showRecharge(null);
                                        })
                                    }
                                },
                                error: function(result, type, err) {
                                    hideLoadingDialog(null);
                                    bridge.call("app.showMsg", '服务器异常！');
                                }
                            });
   ```
   - err设置
   - 微信、qq的二次分享处理 https://www.cnblogs.com/heavenYJJ/p/9445521.html
   
# 1.讲讲输入完网址按下回车，到看到网页这个过程中发生了什么

a. 域名解析

b. 发起TCP的3次握手

c. 建立TCP连接后发起http请求

d. 服务器端响应http请求，浏览器得到html代码

e. 浏览器解析html代码，并请求html代码中的资源

f. 浏览器对页面进行渲染呈现给用户

2.谈谈你对前端性能优化的理解

a. 请求数量：合并脚本和样式表，CSS Sprites，拆分初始化负载，划分主域

b. 请求带宽：开启GZip，精简JavaScript，移除重复脚本，图像优化，将icon做成字体

c. 缓存利用：使用CDN，使用外部JavaScript和CSS，添加Expires头，减少DNS查找，配置ETag，使AjaX可缓存

d. 页面结构：将样式表放在顶部，将脚本放在底部，尽早刷新文档的输出

e. 代码校验：避免CSS表达式，避免重定向

3.前端 MV*框架的意义

早期前端都是比较简单，基本以页面为工作单元，内容以浏览型为主，也偶尔有简单的表单操作，基本不太需要框架。

随着 AJAX 的出现，Web2.0的兴起，人们可以在页面上可以做比较复杂的事情了，然后前端框架才真正出现了。

如果是页面型产品，多数确实不太需要它，因为页面中的 JavaScript代码，处理交互的绝对远远超过处理模型的，但是如果是应用软件类产品，这就太需要了。

长期做某个行业软件的公司，一般都会沉淀下来一些业务组件，主要体现在数据模型、业务规则和业务流程，这些组件基本都存在于后端，在前端很少有相应的组织。

从协作关系上讲，很多前端开发团队每个成员的职责不是很清晰，有了前端的 MV框架，这个状况会大有改观。

之所以感受不到 MV*框架的重要性，是因为Model部分代码较少，View的相对多一些。如果主要在操作View和Controller，那当然 jQuery 这类库比较好用了。

4.请简述盒模型

IE6盒子模型与W3C盒子模型。

文档中的每个元素被描绘为矩形盒子。盒子有四个边界：外边距边界margin, 边框边界border, 内边距边界padding与内容边界content。

CSS3中有个box-sizing属性可以控制盒子的计算方式，

content-box：padding和border不被包含在定义的width和height之内。对象的实际宽度等于设置的width值和border、padding之和。（W3C盒子模型）

border-box：padding和border被包含在定义的width和height之内。对象的实际宽度就等于设置的width值。（IE6盒子模型）

5.请你谈谈Cookie的弊端

a. 每个特定的域名下最多生成的cookie个数有限制

b. IE和Opera 会清理近期最少使用的cookie，Firefox会随机清理cookie

c. cookie的最大大约为4096字节，为了兼容性，一般不能超过4095字节

d. 安全性问题。如果cookie被人拦截了，那人就可以取得所有的session信息。

6.浏览器本地存储

在HTML5中提供了sessionStorage和localStorage。

sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁，是会话级别的存储。

localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

7.web storage和cookie的区别

a. Cookie的大小是受限的

b. 每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽

c. cookie还需要指定作用域，不可以跨域调用

d. Web Storage拥有setItem,getItem等方法，cookie需要前端开发者自己封装setCookie，getCookie

e. Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生

f. IE7、IE6中的UserData通过简单的代码封装可以统一到所有的浏览器都支持web storage

8.对BFC规范的理解

BFC全称是Block Formatting Context，即块格式化上下文。它是CSS2.1规范定义的，关于CSS渲染定位的一个概念。

BFC是页面CSS 视觉渲染的一部分，用于决定块盒子的布局及浮动相互影响范围的一个区域。

BFC的一个最重要的效果是，让处于BFC内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。

利用BFC可以闭合浮动，防止与浮动元素重叠。

9.线程与进程的区别

a. 一个程序至少有一个进程，一个进程至少有一个线程

b. 线程的划分尺度小于进程，使得多线程程序的并发性高

c. 进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率

d. 每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制

e. 多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配

10.请说出三种减少页面加载时间的方法

a. 尽量减少页面中重复的HTTP请求数量

b. 服务器开启gzip压缩

c. css样式的定义放置在文件头部

d. Javascript脚本放在文件末尾

e. 压缩合并Javascript、CSS代码

f. 使用多域名负载网页内的多个文件、图片

11.你都使用哪些工具来测试代码的性能？

JSPerf, Dromaeo

12.你遇到过比较难的技术问题是？你是如何解决的？

13.你常使用的库有哪些？常用的前端开发工具？开发过什么应用或组件？

14.列举IE与其他浏览器不一样的特性？

a. IE的排版引擎是Trident （又称为MSHTML）

b. Trident内核曾经几乎与W3C标准脱节（2005年）

c. Trident内核的大量 Bug等安全性问题没有得到及时解决

d. JS方面，有很多独立的方法，例如绑定事件的attachEvent、创建事件的createEventObject等

e. CSS方面，也有自己独有的处理方式，例如设置透明，低版本IE中使用滤镜的方式

15.什么叫优雅降级和渐进增强？

渐进增强 progressive enhancement：

针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。

优雅降级 graceful degradation：

一开始就构建完整的功能，然后再针对低版本浏览器进行兼容。

区别：

a. 优雅降级是从复杂的现状开始，并试图减少用户体验的供给

b. 渐进增强则是从一个非常基础的，能够起作用的版本开始，并不断扩充，以适应未来环境的需要

c. 降级（功能衰减）意味着往回看；而渐进增强则意味着朝前看，同时保证其根基处于安全地带

16.WEB应用从服务器主动推送Data到客户端有那些方式？

a. html5 websoket

b. WebSocket 通过 Flash

c. XHR长时间连接

d. XHR Multipart Streaming

e. 不可见的Iframe

f. 标签的长时间连接(可跨域)

17.对前端界面工程师这个职位是怎么样理解的？

a. 前端是最贴近用户的程序员，前端的能力就是能让产品从 90分进化到 100 分，甚至更好

b. 参与项目，快速高质量完成实现效果图，精确到1px；

c. 与团队成员，UI设计，产品经理的沟通；

d. 做好的页面结构，页面重构和用户体验；

e. 处理hack，兼容、写出优美的代码格式；

f. 针对服务器的优化、拥抱最新前端技术。

18.你在现在的团队处于什么样的角色，起到了什么明显的作用？

请自行根据自己情况做回答，这个没有统一标准答案。

19.你的优点是什么？缺点是什么？

请自行根据自己情况做回答，这个没有标准答案。

20.如何管理前端团队?

请自行根据自己情况做回答，这个没有标准答案。

21.最近在学什么？能谈谈你未来3，5年给自己的规划吗？

请自行根据自己情况做回答，这个没有标准答案。

22.平时如何管理你的项目？

a. 先期团队必须确定好全局样式（globe.css），编码模式(utf-8) 等；

b. 编写习惯必须一致（例如都是采用继承式的写法，单样式都写成一行）；

c. 标注样式编写人，各模块都及时标注（标注关键样式调用的地方）；

d. 页面进行标注（例如 页面 模块 开始和结束）；

e. CSS跟HTML 分文件夹并行存放，命名都得统一（例如style.css）；

f. JS 分文件夹存放 命名以该JS功能为准的英文翻译。

g. 图片采用整合的 images.png png8 格式文件使用 尽量整合在一起使用方便将来的管理

23.说说最近最流行的一些东西吧？常去哪些网站？

CSDN、SegmentFault、php.net、MDN、css参考手册、iconfont、

underscore、github、Bootstrap、W3Shool、W3Cplus、caniuse

24.请解释一下 JavaScript 的同源策略

同源策略指的是：协议，域名，端口相同，同源策略是一种安全协议。

指一段脚本只能读取来自同一来源的窗口和文档的属性。

25.AMD和CMD 规范的区别？

AMD 提前执行依赖 - 尽早执行，requireJS 是它的实现

CMD 按需执行依赖 - 懒执行，seaJS 是它的实现

26.网站重构的理解

重构：在不改变外部行为的前提下，简化结构、添加可读性，而在网站前端保持一致的行为。

a. 使网站前端兼容于现代浏览器(针对于不合规范的CSS、如对IE6有效的)

b. 对于移动平台的优化，针对于SEO进行优化

c. 减少代码间的耦合，让代码保持弹性

d. 压缩或合并JS、CSS、image等前端资源

27.浏览器的内核分别是什么?

IE浏览器的内核Trident；

Mozilla的Gecko；

Chrome的Blink（WebKit的分支）；

Opera内核原为Presto，现为Blink；

28.请介绍下cache-control

每个资源都可以通过 Cache-Control HTTP 头来定义自己的缓存策略

Cache-Control 指令控制谁在什么条件下可以缓存响应以及可以缓存多久

Cache-Control 头在 HTTP/1.1 规范中定义，取代了之前用来定义响应缓存策略的头（例如 Expires）。

29.前端页面有哪三层构成，分别是什么？作用是什么？

a. 结构层：由 HTML 或 XHTML 之类的标记语言负责创建，仅负责语义的表达。解决了页面“内容是什么”的问题。

b. 表示层：由CSS负责创建，解决了页面“如何显示内容”的问题。

c. 行为层：由脚本负责。解决了页面上“内容应该如何对事件作出反应”的问题。

30.知道的网页制作会用到的图片格式有哪些？

png-8，png-24，jpeg，gif，svg

Webp：谷歌（google）开发的一种旨在加快图片加载速度的图片格式。图片压缩体积大约只有JPEG的2/3，并能节省大量的服务器带宽资源和数据空间。Facebook Ebay等知名网站已经开始测试并使用WebP格式。

Apng：全称是“Animated Portable Network Graphics”, 是PNG的位图动画扩展，可以实现png格式的动态图片效果。04年诞生，但一直得不到各大浏览器厂商的支持，直到日前得到 iOS safari 8的支持，有望代替GIF成为下一代动态图标准。

31.一次js请求一般情况下有哪些地方会有缓存处理？

a. 浏览器端存储

b. 浏览器端文件缓存

c. HTTP缓存304

d. 服务器端文件类型缓存

e. 表现层&DOM缓存

32.一个页面上有大量的图片（大型电商网站），加载很慢，你有哪些方法优化这些图片的加载，给用户更好的体验[性能优化]。

a. 图片懒加载，滚动到相应位置才加载图片。

b. 图片预加载，如果为幻灯片、相册等，将当前展示图片的前一张和后一张优先下载。

c. 使用CSSsprite，SVGsprite，Iconfont、Base64等技术，如果图片为css图片的话。

d. 如果图片过大，可以使用特殊编码的图片，加载时会先加载一张压缩的特别厉害的缩略图，以提高用户体验。

33.谈谈以前端角度出发做好SEO需要考虑什么？

a. 了解搜索引擎如何抓取网页和如何索引网页

b. meta标签优化

c. 关键词分析

d. 付费给搜索引擎

e. 链接交换和链接广泛度（Link Popularity）

f. 合理的标签使用

``` javascript
   
   function lfAjax(config, callbackS, callbackF) {
    var url = config.url || '/';
    var method = config.method || 'GET';
    var async = config.async === undefined ? true : config.async;
    var contentType = config.contentType || 'application/x-www-form-urlencoded';
    var header = config.header || {};
    var data = config.data;
    // 创建XMLHttpRequest对象
    var xhr = new XMLHttpRequest();
    // 初始化请求
    xhr.open(method, url, async);
    // 设置header的默认值
    // xhr.setRequestHeader('Content-Type', value);
    // 设置其它header
    for (var item in header) {
        console.log(item, header[item]);
        xhr.setRequestHeader(item, header[item]);
        // 发送请求

        xhr.send(data);
        // 处理响应
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4) {
                if (xhr.status == 200) {
                    callbackS && callbackS(xhr.responseText);
                } else { callbackF && callbackF(xhr.status); }
            }
        }
    }
}
//银行卡号校验
//Description: 银行卡号Luhm校验
//Luhm校验规则：16位银行卡号（19位通用）:
// 1.将未带校验位的 15（或18）位卡号从右依次编号 1 到 15（18），位于奇数位号上的数字乘以 2。
// 2.将奇位乘积的个十位全部相加，再加上所有偶数位上的数字。
// 3.将加法和加上校验位能被 10 整除。
function luhmCheck(bankno) {

    var num = /^\d*$/; //全数字
    if (!num.exec(bankno)) {
        //$("#banknoInfo").html("银行卡号必须全为数字");
        return false;
    }
    //开头6位
    var strBin = "10,18,30,35,37,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,58,60,62,65,68,69,84,87,88,94,95,98,99";
    if (strBin.indexOf(bankno.substring(0, 2)) == -1) {
        //$("#banknoInfo").html("银行卡号开头6位不符合规范");
        return false;
    }
    var lastNum = bankno.substr(bankno.length - 1, 1); //取出最后一位（与luhm进行比较）
    var first15Num = bankno.substr(0, bankno.length - 1); //前15或18位
    var newArr = new Array();
    for (var i = first15Num.length - 1; i > -1; i--) { //前15或18位倒序存进数组
        newArr.push(first15Num.substr(i, 1));
    }
    var arrJiShu = new Array(); //奇数位*2的积 <9
    var arrJiShu2 = new Array(); //奇数位*2的积 >9
    var arrOuShu = new Array(); //偶数位数组
    for (var j = 0; j < newArr.length; j++) {
        if ((j + 1) % 2 == 1) { //奇数位
            if (parseInt(newArr[j]) * 2 < 9) {
                arrJiShu.push(parseInt(newArr[j]) * 2);
            } else {
                arrJiShu2.push(parseInt(newArr[j]) * 2);
            }
        } else { //偶数位
            arrOuShu.push(newArr[j]);
        }
    }
    var jishu_child1 = new Array(); //奇数位*2 >9 的分割之后的数组个位数
    var jishu_child2 = new Array(); //奇数位*2 >9 的分割之后的数组十位数
    for (var h = 0; h < arrJiShu2.length; h++) {
        jishu_child1.push(parseInt(arrJiShu2[h]) % 10);
        jishu_child2.push(parseInt(arrJiShu2[h]) / 10);
    }
    var sumJiShu = 0; //奇数位*2 < 9 的数组之和
    var sumOuShu = 0; //偶数位数组之和
    var sumJiShuChild1 = 0; //奇数位*2 >9 的分割之后的数组个位数之和
    var sumJiShuChild2 = 0; //奇数位*2 >9 的分割之后的数组十位数之和
    var sumTotal = 0;
    for (var m = 0; m < arrJiShu.length; m++) {
        sumJiShu = sumJiShu + parseInt(arrJiShu[m]);
    }
    for (var n = 0; n < arrOuShu.length; n++) {
        sumOuShu = sumOuShu + parseInt(arrOuShu[n]);
    }
    for (var p = 0; p < jishu_child1.length; p++) {
        sumJiShuChild1 = sumJiShuChild1 + parseInt(jishu_child1[p]);
        sumJiShuChild2 = sumJiShuChild2 + parseInt(jishu_child2[p]);
    }
    //计算总和
    sumTotal = parseInt(sumJiShu) + parseInt(sumOuShu) + parseInt(sumJiShuChild1) + parseInt(sumJiShuChild2);
    //计算Luhm值
    var k = parseInt(sumTotal) % 10 == 0 ? 10 : parseInt(sumTotal) % 10;
    var luhm = 10 - k;
    if (lastNum == luhm) {
        //$("#banknoInfo").html("Luhm验证通过");
        return true;
    } else {
        //$("#banknoInfo").html("银行卡号必须符合Luhm校验");
        return false;
    }
    if (bankno.length < 15 && bankno.length > 20) {
        //$("#banknoInfo").html("银行卡号长度必须在16到19之间");
        return false;
    }
}

// 创建并存储cookie
function setCookie(cname, cvalue, expiredays) {
    var exdate = new Date();
    exdate.setTime(exdate.getTime() + expiredays);
    var cookieItem = cname + "=" + cvalue + (expiredays ? "; expires=" + exdate.toGMTString() : "");
    document.cookie = cookieItem;
}

// 获取cookie
function getCookie(cname) {
    if (document.cookie.length > 0) {
        var name = cname + "=";
        // split() 方法用于把一个字符串分割成字符串数组。
        var ca = document.cookie.split(';');
        for (var i = 0; i < ca.length; i++) {
            // trim() 方法用于去除字符串两端的空白字符
            var c = ca[i].trim();
            // indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。
            // 如果要检索的字符串值没有出现，则该方法返回 -1。
            if (c.indexOf(name) == 0) {
                // substring() 方法用于提取字符串中介于两个指定下标之间的字符。
                var cookieItem = c.substring(name.length, c.length);
                return cookieItem;
            }
        }
    }
    return ""
}

// 删除cookie
function deleteCookie(cname) {
    document.cookie = cname + "=; expires=Thu, 01 Jan 1970 00:00:00 GMT";
}
// 创建cookie
        setCookie('username', 'sleepwalker');
        setCookie('password', '123', 60 * 1000);
        // 读取cookie
        var username = getCookie('username');
        var password = getCookie('password');
        console.log(username);
        console.log(password);
        // 删除 cookie
        deleteCookie('username');
        deleteCookie('password');
```
