### http状态码


> 此处记录部分常用的HttpStatusCode便于查询，如果遇见的code不包含在此文档，可以访问[Http状态码](http://www.runoob.com/http/http-status-codes.html)或者直接访问[百度](http://www.baidu.com)或者[Google](http://www.google.com)
- 1** 
信息，服务器收到请求，需要请求者继续执行操作
    - 100 (Continue) 继续。客户端应继续其请求
    - 101 (Switching Protocols) 切换协议。服务器根据客户端的请求切换协议。只能切换到更高级的协议，例如，切换到HTTP的新版本协议
- 2** 成功，操作被成功接收并处理
    - 200 (OK)请求成功
    - 201 (Created)已创建。成功请求并创建了新的资源
    - 202 (Accepted)已接受。已经接受请求，但未处理完成
    - 203 (Non-Authoritative Information)非授权信息。请求成功。但返回的meta信息不在原始的服务器，而是一个副本
    - 204 (	No Content) 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下，可确保浏览器继续显示当前文档
- 3** 重定向，需要进一步的操作以完成请求
    - 301 资源被永久转移到其他url
- 4** 客户端错误，请求包含语法错误或无法完成请求
    - 400 (Bad Request)客户端请求的语法错误，服务器无法理解
    - 401 (Unauthorized)请求要求用户的身份认证
    - 404 (Not Found)请求的资源不存在
    - 408 (Request Time-out) 服务器等待客户端发送的请求时间过长，超时
    - 411 (Length Required) 服务器无法处理客户端发送的不带Content-Length的请求信息
    - 414 (Request-URI Too Large) 请求的URI过长（URI通常为网址），服务器无法处理
    - 415 (Unsupported Media Type) 服务器无法处理请求附带的媒体格式
    - 417 (	Expectation Failed) 服务器无法满足Expect的请求头信息
- 5** 服务器错误，服务器在处理请求的过程中发生了错误
    - 500 (	Internal Server Error)服务器内部错误
    - 502 (Bad Gateway) 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应
    - 504 (	Service Unavailable) 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中


### http请求/回复携带信息

HTTP请求报文由3部分组成（请求行+请求头+请求体）： 