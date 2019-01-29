XSS(cross-site scripting 跨域脚本攻击)

注入方法：
* 在 HTML 中内嵌的文本中，恶意内容以 script 标签形成注入。
* 在内联的 JavaScript 中，拼接的数据突破了原本的限制（字符串，变量，方法名等）。
* 在标签属性中，恶意内容包含引号，从而突破属性值的限制，注入其他属性或者标签。
* 在标签的 href、src 等属性中，包含 javascript: 等可执行代码。
* 在 onload、onerror、onclick 等事件中，注入不受控制代码。
* 在 style 属性和标签中，包含类似 background-image:url("javascript:..."); 的代码（新版本浏览器已经可以防范，并不执行url中的代码，而是直接请求）。
* 在 style 属性和标签中，包含类似 expression(...) 的 CSS 表达式代码（新版本浏览器已经可以防范，废弃expression，使用calc，只支持简单的运算，且并不会调用js引擎）。

哪些信息无法信任：
* 来自用户的 UGC 信息
* 来自第三方的链接
* URL 参数
* POST 参数
* Referer （可能来自不可信的来源）
* Cookie （可能来自其他子域注入）

XSS分类
1. 存储型XSS
    1. 攻击者将恶意代码提交到目标网站的数据库中。
    1. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
    1. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
    1. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
    ```
        需要特殊的转义方法，可以输出HTML的tag，同时又过滤非法脚本
        对于jsonp的xss防范，主要是callback参数，限定callback函数名词长度，函数名只允许[]a-zA-Z0-9_$.
    ```

1. 反射型XSS
    1. 攻击者构造出特殊的 URL，其中包含恶意代码。
    1. 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
    1. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
    1. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
    ```
        对于需要直接输出用户输入的结果的，转义掉<>&"'
        对于需要在js中输出变量的，转义掉/[\x00-\x2f\x3a-\x40\x5b-\x60\x7b-\x7f]/，转为ascii码，前加\\x
    ```

区别：存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。

3. DOM型XSS
    1. 攻击者构造出特殊的 URL，其中包含恶意代码。
    1. 用户打开带有恶意代码的 URL。
    1. 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
    1. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

措施
1. 预防攻击者提交恶意代码（不确定性，容易出现乱码）
1. 预防浏览器执行恶意代码：
    1. 改成纯前端渲染，把代码和数据分隔开（仍然有DOM型XSS攻击问题）
    1. 对HTML做充分转义
1. 预防DOM型XSS攻击：
    1. 在使用 .innerHTML、.outerHTML、document.write() 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，而应尽量使用 .textContent、.setAttribute() 等。
    1. 如果用 Vue/React 技术栈，并且不使用 v-html/dangerouslySetInnerHTML 功能，就在前端 render 阶段避免 innerHTML、outerHTML 的 XSS 隐患。
    1. DOM 中的内联事件监听器，如 location、onclick、onerror、onload、onmouseover 等，\<a\> 标签的 href 属性，JavaScript 的 eval()、setTimeout()、setInterval() 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 API，很容易产生安全隐患，请务必避免。
1. 其他
   1. [Content Security Policy](https://developers.google.com/web/fundamentals/security/csp/?hl=zh-cn#_1)
   1. 输入内容长度控制
   1. HTTP-only Cookie
   1. 验证码
   1. X-Download-Options:noopen 禁用 IE 下下载框Open按钮，防止 IE 下下载文件默认被打开 XSS
   1. X-Content-Type-Options:nosniff 禁用 IE8 自动嗅探 mime 功能例如 text/plain 却当成 text/html 渲染，特别当本站点 serve 的内容未必可信的时候。
   1. X-XSS-Protection IE 提供的一些 XSS 检测与防范


