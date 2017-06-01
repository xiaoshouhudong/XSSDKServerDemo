# 小手互动SDK服务端说明文档

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](https://raw.githubusercontent.com/xiaoshouhudong/XSSDKServerDemo/master/LICENSE)&nbsp;





##	接口说明
###	1、用户会话验证
* 1) **请求地址：** http://sdk.xiaoshouhudong.com/api.php/open/verifySession
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
            <td>小手互动后台申请(游戏Id)</td>
            <td>string</td>
            <td>是</td>
            <td></td>
        </tr>
        <tr>
            <td>token</td>
            <td>接口名</td>
            <td>string</td>
            <td>是</td>
            <td>token令牌</td>
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

请求例子：   
```php
Public function notify(){
    $params['gameId'] = 1;
    $params['token'] = 'tokendemo';
    ksort($params);
    $params['key'] = 'key';//由小手互动颁发
    $params['sign'] = md5(implode('', $params));
    unset($params['key']);
    $res = file_get_contents(http_build_query($url.$keys));
    //处理$res数据
}
```

返回内容（ json 格式）：

```
{
    "errorCode": 0,
    "errorMsg": "",
    "data": {
        "userId": "8",
        "userName": "zxwzxw"
    }
}
```



###	2、支付结果异步通知
* 1）**请求地址：** 由游戏cp方提供
* 2）**调用方式：** HTTP Get
* 3）**接口描述：**
**“游戏客户端”**购买成功后，**“SDK 服务器”**通过该接口通知**”游戏服务器“**。支付成功一定会及时回调通知，支付失败可能不会及时通知（如支付超时等无法及时通知），如果需要知道订单状态，使用支付结果查询结构。
* 4）**请求方：** SDK 服务器  **http://sdk.xiaoshouhudong.com/api.php/pay/topay**
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
            <td></td>
        </tr>
        <tr>
            <td>userId</td>
            <td>用户Id</td>
            <td>int</td>
            <td>是</td>
            <td></td>
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
            <td>orderStatus</td>
            <td>订单状态</td>
            <td>int</td>
            <td>是</td>
            <td>1：已支付，0：未支付</td>
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
	
CP方处理例子：
```php
Public function notify(){
    $params['cpOrderNo'] = $_GET['cpOrderNo'];
    $params['XSOrderNo'] =  $_GET['XSOrderNo'];
    $params['gameId'] =  $_GET['gameId'];
    $params['serverId'] =  $_GET['serverId'];
    $params['amount'] =  $_GET['amount'];
    $params['orderStatus'] =  $_GET['orderStatus'];
    $params['userId'] = $_GET['userId'];
    $sign = $_GET['sign'];

    ksort($params);
    $params['key'] = 'dfsklfjslfksl7834853jghyg' ;
    if(md5(implode('', $params))==$sign){
        echo 'success';
        //CP方数据处理
    }
}
```

返回内容（string）：
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

