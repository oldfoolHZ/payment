# 支付接口

## Beyounger


#### 业务流程

##### Beyounger支付流程
  
  1、商户使用本接口文档提供的接口参数发起支付请求到BeyoungerPay系统  
  2、BeyoungerPay系统收到商户发起的支付请求后，进行一系列配置校验和风险控制校验之后上送到通道方完成授权处理  
  3、BeyoungerPay收到通道方返回支付结果后再同步返回到商户系统  
  4、商户系统根据收到的支付结果处理相关业务

####请求参数

* 接口地址：
  * 生产：https://cashier.beyounger.com/gateway/payment/transaction
  * 沙盒：https://cashier-sandbox.beyounger.com/gateway/payment/transaction
* 请求方式：POST
* 请求头：Content-Type:multipart/form-data
* 请求头参数： 

* body参数:  

> body参数

参数名| 必填 | 类型                  | 说明              |长度|备注|
--------------|------|-----------------------|-----------------|--------------|--------------
sandbox| 是 | Integer             | 是否沙盒环境             |10|1 开启 0 关闭
request_time| 是 | String              | 请求时间戳          |50|
trade_email| 是 | String              | 商户邮箱              |50|
order_id| 是 | String              | 商户订单号              |12|
amount| 是 | String              | 应付金额      |20|
currency| 是 | String              | 应付币种          |20|
pay_method| 是 | String              | 支付方式 C01 信用卡付款          |20|C01
notify_url| 是 | String              | 异步通知地址          |255|
callback_url| 是 | String              | 同步通知地址        |255|
sign| 是 | String              | 签名              |32|签名字段
customer[ip]| 是 | String              | 客户IP              |50|
customer[email]| 是 | String            | 客户邮箱              |50|
card[number]| 是 | String              | 持卡人卡号              |50|
card[holder]| 是 | String              | 持卡人姓              |50|
card[last_name]| 是 | String            | 持卡人名              |50|
card[cvv2]| 是 | String            | 持卡人cvv              |50|
card[exp_year]|是 | String            | 持卡人过期年              |50|
card[exp_month]|是 | String            | 持卡人过期月              |50|
request_type|是 | String            | 对接类型              |50|3 直连 1 form
billing[first_name]|是 | String            | 账单 姓              |50|
billing[last_name]|是 | String            | 账单 名              |50|
billing[address1]|是 | String            | 账单 地址栏1              |50|
billing[city]|是 | String            | 账单城市             |50|
billing[state]|是 | String            | 账单 省份             |50|
billing[country]| 是 | String              | 账单国家编码          |2|参考国家编码表
billing[zip_code]| 是 | String              | 账单ZIP          |50|
billing[phone]| 是 | String              | 账单手机号码          |50|
product| 是 | String              | 产品信息 jsondecode urlencode          |255|

* 商品列表里的商品参数
    
参数名|必填|类型|说明|长度|备注
--------------|--------------|--------------|--------------|--------------|--------------
sku|是|String|商品编号|64|
name|是|String|商品名称|128|
price|是|String|商品单价|16|
currency|是|String|商品单价|16|
quantity|是|String|商品数量|16|
image|是|String|商铺图片|255|
url|是|String|商品地址|255|

<aside class="success">
    请求示例, 见右侧
</aside>

  
> 请求示例

```java
public class Test{
  public static void main(String[] args){
    JSONObject jsonObject = new JSONObject();
    jsonObject.put("trade_email", "123455@123.com");
    jsonObject.put("order_id", "1641972507000");
    jsonObject.put("currency", "CNY");
    //.....除签名(sign)以外的所有的body参数
    
    //签名
    String sign = SHA256(商户密钥+request_time+req_email+req_invoice+bil_method+bil_price+bil_currency);
    jsonObject.put("sign", sign.toUpperCase());
    
    String url = "https://cashier.beyounger.com/gateway/payment/transaction";
    HttpResponse httpResponse = HttpRequest.post(url)
                    .body(jsonObject.toJSONString(), "application/json")
                    .execute();
    int status = httpResponse.getStatus();
    if(status == HttpStatus.HTTP_OK){
        String content = httpResponse.body();
    }
  }
}
```



#### 响应参数
>  响应成功示例

```json
{
  "code": "200",
  "message": "success",
  "data": {
    "sandbox":"0",
    "trade_email":"richard@billpayserv.com",
    "order_id":"9944095",
    "amount":"115.95",
    "currency":"USD",
    "pay_method":"C01",
    "notify_url":"https://highwayjoin.com/apiv2/accept/request-nft/nationalkey?pid=9944095",
    "transaction_no":"C01E314VPWTF194E",
    "status":"paid",
    "gateway_currency":"USD",
    "gateway_amount":115.95,
    "card_info":{"card_type":"visa","number":"498503******1581","card_first":"498503","card_last":"1581","holder":"Brenda Moses","hash":"c513bf7743047e6166ff794d90b0986779986f499864c9f28704b7bba113a1ce","month":"02","year":"2026","country":"US"},
    "gateway_message":"Payment successful. DESC : Movement Wears",
    "refund_amount":0,
    "request_time":1667983031,
    "sign":"841ec1d47190d7d4a9da044fa08a261074f03313684afd3ba9b788904424d0b4"}
}
```

> 响应失败示例
    
```json
{
  "code": "401",
  "message": "商户号不存在"
}
```
    
参数名|必填|类型|说明
--------------|--------------|--------------|--------------
code|是|String|错误码(参考错误码表)
message|是|String|错误描述
data|否|T|请求响应数据
    
* 响应数据参数
    
参数名| 必填 |类型|说明
--------------|---|--------------|--------------
transaction_no| 是 |String|交易流水号
status| 是 |Integer|支付状态码(0：支付成功  1：支付失败  2：支付处理中(对于支付时有效))
notify_url| 否 |String|支付时该参数为必填项(需商户重定向到该链接)
order_id| 是 |String|商户订单号
gateway_message| 否 |String| 支付信息描述
gateway_amount|是|String|原始金额
gateway_currency|是|String|原始币种
sign|是|String|签名 
  

## 同步通知&异步通知

商户持卡人在完成支付后会重定向到支付时传递的notify_url地址上，重定向时会带上订单的相关信息。  
注意：重定向携带的字段中表示订单支付状态的字段不表示最终的支付成功与否。  

#### 请求参数

参数名|必填|类型|说明
---|---|---|---
transaction_no| 是 |String|交易流水号
status| 是 |Integer|支付状态码 unpaid=未支付  paid已支付 failed=支付失败
notify_url| 否 |String|支付时该参数为必填项(需商户重定向到该链接)
order_id| 是 |String|商户订单号
gateway_message| 否 |String| 支付信息描述
gateway_amount|是|String|原始金额
gateway_currency|是|String|原始币种
sign|是|String|签名 

