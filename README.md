# 小手互动SDK服务端说明文档
小手互动服务端Demo

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](https://raw.githubusercontent.com/seven/HGSDKServerDemo/master/LICENSE)&nbsp;


##	概要</br>
本部分主要提供互冠游戏中心**”SDK服务器”**和互冠游戏合作商**“游戏服务器”**之间交互的接口规范.
##	协议说明
###	通信协议

**"SDK 服务器"** 采用 HTTP 协议作为通信协议，**"游戏服务器"** 通过构造HTTP 请求（ POST 方式）向**"SDK 服务器"** 发起接口请求。
**"游戏服务器"** HTTP 请求数据示例：
```
//JSON:
{
    "cp_id":4,
    "gameId":4,
    "server_id":4
    "sid" : "xxxxxxx",
    "sign":"edf8015c9eae9515849bd1d06b8183f8",
    "method" : "verifySession",
}
```

**SDK 服务器** 响应数据示例：</br>
200 OK</br>
Content-Type: application/json:charset=utf-8
```
//JSON:
{    
    "errorcode": 0,
    "data":
    {
        "huowu_id": "xxxxxxxx"
    }
}
```

####	数据协议
* 1)	**数据格式**
请求消息和响应消息的内容都使用 json 格式表示数据，具体请参考下文的例子。
* 2)	**字符编码**
请求与响应内容（ json 格式数据）须采用 utf-8 字符编码。
* 3)	**签名规则**
使用 md5 哈希对请求内容进行签名。
    * 算法：md5(签名内容+api_key)</br>
    * 说明: md5编码后需要转换回全小写，表达式中的 "+"好表示连接字符串，拼接式需去掉，</br>
    * 签名内容：post请求中取请求数据json的data中的字段，get请求取所有参数字段。各字段名及其字段值进行拼接，字段名和字段值之间使用等号(=)连接。拼接时需要对字段名进行排序，排序方式是按字段名进行升序排列。</br>
    * 签名示例:post请求的签名示例</br>
    * 请求：path: /g/users/reg
```
//JSON:
body:
{
    "channelId":”1”, 
    “cpId”:”4”,
    “gameId”:”1”,
    “method”:” verifySession”,
    “sessionId”:” 57e8de309b165”,
    "sign":"f578be068e280397f4f1455782ebee85"
}  
```

排序拼接后得出的要签名内容串为：		
```
channelId=1cpId=4gameId=1method=verifySessionsessionId=57e8de309b165
假设 app_key=69a782fdc493bbd2d7d615ed24fe2d8b
要进行md5哈希的字符串为：
channelId=1cpId=4gameId=1method=verifySessionsessionId=57e8de309b16569a782fdc493bbd2d7d615ed24fe2d8b执行md5哈希后，得到最终签名值	sign=f578be068e280397f4f1455782ebee85
```

##	接口说明
###	服务器地址
线上服务器地址：http://hwsdk.huguangame.com/
###	用户会话验证
* 1) **请求地址：** http://hwsdk.huguangame.com/api.php
* 2) **调用方式：** HTTP POST
* 3) **接口描述：**
验证 sessionId 是否为有效的登录用户会话，若有效则返回其 huowu_id。**“游戏客户端”**通过“SDK 客户端”获取到sessionId（详细参考对应 API 分册），传到**“游戏服务器”**，**“游戏服务器”**到**“SDK 服务器”**验证用户会话sessionId的有效性，获取用户的huowu_id，供游戏使用。</br>**注意：进行接口调用前请确认sessionId是否具备值，如sessionId值为空时请勿调用此接口。**
* 4) **请求方：** 游戏服务器
* 5) **响应方：** SDK 服务器
* 6) **请求内容：** JSON 格式

<table>
    <thead>
        <tr>
            <th>字段名称</th>
            <th>字段说明</th>
            <th>类型</th>
            <th>必填</th>
            <th>备注</th> 
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>channelId</td>
            <td>服务器处理标识</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>cpId</td>
            <td>合作商id</td>
            <td>string</td>
            <td>是</td>
            <td>4</td>
        </tr>
        <tr>
            <td>method</td>
            <td>接口名</td>
            <td>string</td>
            <td>是</td>
            <td>verifySession</td>
        </tr>
        <tr>
            <td>sign</td>
            <td>签名参数</td>
            <td>string</td>
            <td>是</td>
            <td>MD5(cpId+签名内容+apiKey)</td>
        </tr>
    </tbody>
</table>



签名内容：
sid=........

请求例子：   
```
//JSON
{
    "channelId":”1”, 
    “cpId”:”4”,
    “gameId”:”1”,
    “method”:” verifySession”,
    “sessionId”:” 57e8de309b165”,
    "sign":"f578be068e280397f4f1455782ebee85"
}
```

7) 返回内容（ json 格式）：

<table>
    <thead>
        <tr>
            <th>字段名称</th>
            <th>字段说明</th>
            <th>类型</th>
            <th>必填</th>
            <th>备注</th> 
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>errorCode</td>
            <td>服务器处理标识</td>
            <td>string</td>
            <td>是</td>
            <td>0：表示没任何错误，其他值表示出错</td>
        </tr>
        <tr>
            <td>id</td>
            <td>用户id</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>userName</td>
            <td>用户帐号</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>mobile</td>
            <td>用户绑定手机帐号</td>
            <td>string</td>
            <td>否</td>
            <td></td>
        </tr>
        <tr>
            <td>password</td>
            <td>用户密码</td>
            <td>string</td>
            <td>否</td>
            <td></td>
        </tr>
    </tbody>
</table>

正确返回例子：
```
//JSON
{    
    "errorCoder": 0,
    "data":
    {
        "id": “2000”,
        "userName": "huowu",
        "mobile": "12345678911",
        "password": "abc123"
    }
}
```

错误返回例子：
```
{    
    "errorCoder": 4,
    "data":
    {
        "msg": “错误信息”
    }
}
```

###	支付结果异步通知
* 1）**请求地址：** 由游戏cp方提供
* 2）**调用方式：** HTTP POST
* 3）**接口描述：**
**“游戏客户端”**购买成功后，**“SDK 服务器”**通过该接口通知**”游戏服务器“**。支付成功一定会及时回调通知，支付失败可能不会及时通知（如支付超时等无法及时通知），如果需要知道订单状态，使用支付结果查询结构。
* 4）**请求方：** SDK 服务器
* 5）**响应方：** 游戏服务器
* 6）**请求内容：** JSON 格式
<table>
    <thead>
        <tr>
            <th>字段名称</th>
            <th>字段说明</th>
            <th>类型</th>
            <th>必填</th>
            <th>备注</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>data</td>
            <td>请求数据</td>
            <td>json</td>
            <td>是</td>
            <td>JSON格式</td>
        </tr>
        <tr>
            <td>sign</td>
            <td>签名参数</td>
            <td>string</td>
            <td>是</td>
            <td>MD5(cpId+签名内容+apiKey)</td>
        </tr>
        <tr>
            <td>order_no</td>
            <td>订单号</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>game_id</td>
            <td>游戏ID</td>
            <td>int</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>server_id</td>
            <td>服务器ID</td>
            <td>int</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>huowu_id</td>
            <td>用户ID</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>pay_way</td>
            <td>支付方式</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>amount</td>
            <td>支付金额</td>
            <td>string</td>
            <td>是</td>
            <td>单位是元</td>
        </tr>
        <tr>
            <td>custom_info</td>
            <td>客户信息</td>
            <td>string</td>
            <td>是</td>
            <td>创建订单时传给SDK，这里原样回传</td>
        </tr>
        <tr>
            <td>orderStatus</td>
            <td>支付状态</td>
            <td>int</td>
            <td>是</td>
            <td>1:支付成功，2:支付失败，3:支付超时</td>
        </tr>
        <tr>
            <td>failed_msg</td>
            <td>支付失败信息</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>cp_order_no</td>
            <td>cp订单ID</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>company</td>
            <td>公司id</td>
            <td>string</td>
            <td>是</td>
            <td>1 互冠</td>
        </tr>
    </tbody>
</table>
	
请求例子：
```
//JSON
{
    "data":
    {
        "order_no":"abcf1330",
        "game_id":123,
        "server_id":654,
        "huowu_id":"123456",
        "pay_way":"6",
        "amount":"100.00",
        "custom_info":"123",
        "orderStatus":1,   
        "failed_msg":"",
        "cp_order_no":"1234567",
        "company":1
    },
    "sign":"1f96be69a821405d7357847f30bcda81"
}
```

7）返回内容（string）：
<table>
    <thead>
        <tr>
            <th>输出值</th>
            <th>备注</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>success</td>
            <td>成功,表示游戏服务器成功接收了该次充值结果通知,对于充值结果为失败的,只要能成功接收,也应返回 success。</td>
        </tr>
        <tr>
            <td>failed</td>
            <td>失败,表示游戏服务器无法接收或识别该次充值结果通知,如:签名检验不正确、游戏服务器接收失败</td>
        </tr>
    </tbody>
</table>
	
	
	

许可证
==============
HGSDK 使用 MIT 许可证，详情见 LICENSE 文件。

