OWL
===
好用的 Chrome 重定向工具

### 安装
 - 如果你可以翻墙，建议直接访问 [Chrome Webstore]()
 - 如果你翻不了墙，可以直接下载[owl.crx]()，然后打开`扩展程序`页面，把`.crx`文件拖进去。
 - 如果前两种方法无效，请直接下载这个项目文件，再以开发者模式加载，网上很多教程，不多说。

### 使用
#### 主界面
![screen1](http://meowtec.github.io/assets/owl/screen1.png)
 - 右上角是全局开关，启用状态为蓝色，禁用状态为灰色。
 - 左侧是规则列表，目前只有一条规则。如果规则被禁用，它的颜色会变淡。
 - 下面的三个图标分别是删除、编辑和禁用按钮。

#### 编辑界面
![screen2](http://meowtec.github.io/assets/owl/screen2.png)
 - 第一个输入框输入替换前的 url 或者正则。右边的图标`[.*]`决定你输入的应该是一个普通 url （普通模式）还是一条正则表达式（正则模式），默认情况下应该输入普通的 url，此时图标是未选中的。普通模式下，只有当链接完全匹配时，url才会被重定向（或阻止）。
 正则模式下，如果浏览器访问的链接可以匹配你的正则，会被重定向。

> - 由于链接中`.`等特殊字符比较多，系统提供了url转正则的功能。即：普通模式下输入一条 url，然后点`[.*]`切换到正则模式，此 url 会自动被转化为正则表达式。
  - 正则不需要加前后的斜杠。

 - 下面的大输入框中输入替换后的链接，右边有三个按钮，分别是 `普通链接`,`文本内容`，`函数`
  1. `普通链接`：请求会被自动重定向到此链接。
  2. `文本内容`：程序将这段文本编码为 dataURL，然后将请求重定向到 此 dataURL。适合需要修改外链`css`/`js`(含jsonp)的情况。由于 `ajax` 的跨域特性，此方式并不能修改 ajax 请求返回内容。
  3. `函数`：在这里填入一个函数，函数参数为替换前的 url，函数返回值为替换后的 url。如果需要阻止请求，需要返回 `false`。

### 实例
##### 压缩后的 js 转压缩前的 js
我们以 `jQuery` 官网为例，为了节省流量，jQuery 官网使用的是压缩后的 `jQuery.min.js` 文件，地址是：
```
http://ajax.lug.ustc.edu.cn/ajax/libs/jquery/1.11.2/jquery.min.js
```
而压缩前的 `jQuery` 文件只少了一个`.min`。
如图，我们添加一条规则，然后刷新 jQuery 主页：
![screen3](http://meowtec.github.io/assets/owl/screen3.png)
我们会发现`jquery.min.js` 被重定向到 `jquery.js`


##### fc.5sing 跳转
有一次我在网上找到一个翻唱链接，`http://fc.5sing.com/5936546.html`（这只是我临时找的，之前要找的早丢了），点进去发现访问不了。
原来是 5sing 被酷狗收购了，新的链接地址应该是 `http://5sing.kugou.com/fc/5936546.html`。
于是我添加了一条规则，让所有`fc.5sing.com`域名下的链接均能正常跳转到`5sing.kugou.com`域名。
regexp:
```
^http:\/\/fc\.5sing\.com\/(\d+)\.html.*$
```
replacer:
```
function (url){
  var matchResult = url.match(/^http:\/\/fc\.5sing\.com\/(\d+)\.html.*$/)
  return 'http://5sing.kugou.com/fc/' + matchResult[1] + '.html'
}
```
![screen4](http://meowtec.github.io/assets/owl/screen4.png)


#### google web fonts 替换为 ustc.edu.cn
regexp:
```
^https?:\/\/(((ajax|fonts)\.googleapis\.com)|(themes\.googleusercontent\.com)|(fonts\.gstatic\.com))\/.*$
```
replacer:
```
function (url){
  return url.replace('googleapis.com', 'lug.ustc.edu.cn')
            .replace('themes.googleusercontent.com', 'google-themes.lug.ustc.edu.cn')
            .replace('fonts.gstatic.com', 'fonts-gstatic.lug.ustc.edu.cn')
}
```
![screen5](http://meowtec.github.io/assets/owl/screen5.png)
#### 干掉谷歌统计：
url:
```
http://www.google-analytics.com/analytics.js
```
replacer 默认置空。
![screen6](http://meowtec.github.io/assets/owl/screen6.png)

### 内置方法
 - download()
 你可以在 replacer 函数中调用 download 方法，对资源进行下载，下面的实例表示在`music.qq.com`试听音乐时自动下载音频文件：
 regexp:
 ```
 ^http:\/\/cc\.stream\.qqmusic\.qq\.com\/.*\.m4a.*$
 ```
 replacer:
 ```
 function (url){
   // 函数返回 undefined 则不会重定向
   download(url)
 }
 ```

