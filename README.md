# 小手互动SDK服务端说明文档

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](https://raw.githubusercontent.com/xiaoshouhudong/XSSDKServerDemo/master/LICENSE)&nbsp;





##	接口说明
###	服务器地址
线上服务器地址：http://sdk.xiaoshouhudong.com
###	1、用户会话验证
* 1) **请求地址：** http://sdk.xiaoshouhudong.com/api.php
* 2) **调用方式：** HTTP Get
* 3) **接口描述：**
验证 token 是否为有效的登录用户会话，若有效则返回其 userId。**“游戏客户端”**通过“SDK 客户端”获取到token，传到**“游戏服务器”**，**“游戏服务器”**到**“SDK 服务器”**验证用户会话token的有效性，获取用户的userId，供游戏使用。</br>**注意：进行接口调用前请确认token是否具备值，如token值为空时请勿调用此接口。**
* 4) **请求方：** 游戏服务器
* 5) **响应方：** SDK 服务器
* 6) **请求内容：** 

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
            <td>gameId</td>
            <td>游戏Id</td>
            <td>string</td>
            <td>是</td>
            <td></td>
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
            <td>所有参数key(不包括sign)按照A-Z字段升序拼接key之后md5加密</td>
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
            <td>userId</td>
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
    </tbody>
</table>


###	2、支付结果异步通知
* 1）**请求地址：** 由游戏cp方提供
* 2）**调用方式：** HTTP Get
* 3）**接口描述：**
**“游戏客户端”**购买成功后，**“SDK 服务器”**通过该接口通知**”游戏服务器“**。支付成功一定会及时回调通知，支付失败可能不会及时通知（如支付超时等无法及时通知），如果需要知道订单状态，使用支付结果查询结构。
* 4）**请求方：** SDK 服务器  '''http://sdk.xiaoshouhudong.com/api.php/pay/topay'''
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
            <td>cpOrderNo</td>
            <td>CP方订单</td>
            <td>int</td>
            <td>是</td>
            <td>JSON格式</td>
        </tr>
        <tr>
            <td>XSOrderNo</td>
            <td>小手互动平台订单</td>
            <td>int</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>gameId</td>
            <td>游戏Id</td>
            <td>int</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>serverId</td>
            <td>游戏区服</td>
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
            <td>sign</td>
            <td>所有参数key(不包括sign)按照A-Z字段升序拼接key之后md5加密</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
    </tbody>
</table>
	
请求例子：
```php
Public function notify(){
    $params['cpOrderNo'] = $_GET['cpOrderNo'];
    $params['XSOrderNo'] =  $_GET['XSOrderNo'];
    $params['gameId'] =  $_GET['gameId'];
    $params['serverId'] =  $_GET['serverId'];
    $params['amount'] =  $_GET['amount'];
    $params['orderStatus'] = $_GET['orderStatus'];
    $sign = $_GET['sign'];

    ksort($data);
    $params['key'] = 'dfsklfjslfksl7834853jghyg' ;
    if(md5(implode('', $params))==$sign){
        echo 'success';
        //CP方数据处理
    }
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
            <td>成功,表示游戏服务器成功接收了该次充值结果通知</td>
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

