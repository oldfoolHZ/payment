#退款接口

#### 请求参数

* 请求地址：
  * 生产：https://cashier.beyounger.com/gateway/payment/refund
  * 沙盒：https://cashier-sandbox.beyounger.com/gateway/payment/refund
* 请求方式：POST
* 请求头：Content-Type:application/json

* body参数:

```json
{
  "merNo":"104001001",
  "merOrderNo":"asdfghjkl",
  "amount":"10",
  "version":"V3.0.0",
  "tradeNo":"DZ1234567890000",
  "sign":"9D6FDF4880B00B002B1F2AB61AE9A721",
  "notifyUrl":"https://www.test.com",
  "remark":"12323425345"
}
```


参数名|必填|类型|说明|长度|备注
  --------------|--------------|--------------|--------------|--------------|--------------
merNo|是 |Integer|商户号|10|可在商户后台注册获取
merOrderNo|是|String|商户订单号|32|
amount|是|String|金额|12|保留两位小数
notifyUrl|否|String|异步通知地址|512|用来接收PomeloPay向商户推送退款结果
sign|是|String|签名|32|(merNo、merOrderNo、amount、tradeNo、商户秘钥),详情请参阅：签名
version|是|String|版本号|6|默认：V3.0.0
tradeNo|是|String|交易流水号|50|商家支付时平台生成的订单号
remark|否|String|备注|1000|


<aside class="success">
    请求示例, 见右侧
</aside>

```java
public class Test{
  public static void main(String[] args){
    JSONObject jsonObject = new JSONObject();
    jsonObject.put("merNo", "123455");
    jsonObject.put("merOrderNo", "1641972507000");
    //.....除签名(sign)以外的所有的body参数
    
    //签名
    String sign = MD5(merNo+merOrderNo+amount+tradeNo+商户密钥);
    jsonObject.put("sign", sign.toUpperCase());
    
    String url = "https://cashier.beyounger.com/gateway/payment/refund";
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

> 响应成功示例

  ```json
  {
  "code": "00000",
  "message": "SUCCESS",
  "data": {
    "refundNo": "12312432423",
    "tradeNo": "DZ1234567890123",
    "merNo": "104001001",
    "merOrderNo": "abc12323424234",
    "refundAmount": "10",
    "refundCurrency": "CNY"
  }
}
  ```
> 响应失败示例

  ```json
  {
  "code": "10004",
  "message": "商户号不存在"
}
  ```
参数名|必填|类型|说明
  --------------|--------------|--------------|--------------
code|是|String|业务状态码(参考业务状态码)
message|是|String|业务请求状态描述
data|否|T|请求响应数据

* 响应数据参数

参数名|必填|类型|说明
  --------------|--------------|--------------|--------------
refundNo|是|String|退款申请成功生成的唯一ID
tradeNo|是|String|交易生成的唯一订单号
merNo|是|String|退款申请商户号
merOrderNo|是|String|退款商户订单号
refundAmount|是|String|退款金额
refundCurrency|是|String|退款币种



## 退款异步通知

* 如果退款时没有传递notifyUrl参数,则通道方通知到PomeloPay后,PomeloPay不会通知到商户系统
* 如果商户系统接收到PomeloPay通知后没有返回SUCCESS(区分大小写)字符串,PomeloPay会认为商户系统没有收到通知，PomeloPay会在第1，2，4，8，16，32，64，128分钟之后再次发起通知，PomeloPay总共会发起9次通知，如果商户系统在9次通知中都没有返回SUCCESS，则PomeloPay不会再发起通知。

#### 请求参数

> 请求body参数

  ```json
  {
    "tradeNo": "DZ2201111806024151",
    "merOrderNo": "1641972507000",
    "refundNo": "1641972507000",
    "state": "0",
    "message": "SUCCESS",
    "refundAmount": "10",
    "refundCurrency": "CNY"
  }
  ```

<aside class="success">
    请求示例, 见右侧
</aside>


> 请求示例

  ```java
  public class Test{
    public static void main(String[] args){
      JSONObject jsonObject = new JSONObject();
      jsonObject.put("tradeNo", "DZ2201111806024151");
      jsonObject.put("merOrderNo", "1641972507000");
      jsonObject.put("refundNo", "2334543543534");
      jsonObject.put("code", "0000");
      jsonObject.put("message", "SUCCESS");
      jsonObject.put("refundAmount", "10");
      jsonObject.put("refundCurrency", "CNY");
      
      
      String url = "退款时传递的notifyUrl参数地址";
      HttpResponse httpResponse = HttpRequest.post(url)
                            .body(jsonObject.toJSONString(), "application/json")
                            .execute();
      int status = httpResponse.getStatus();
      if(status == HttpStatus.HTTP_OK){
          String content = httpResponse.body();
          if("SUCCESS".equals(content)){
            //通知成功
          } else{
            //通知失败
          }
      } else{
        //通知失败
      }
    }
  }
  ```

* 请求地址：退款时传递的notifyUrl参数地址
* 请求方式：POST
* 请求头：Content-Type:application/json
* 请求参数：

参数名|必填|类型|说明
  --------------|--------------|--------------|--------------
tradeNo|是|String|交易流水号
merOrderNo|是|String|商户订单号
refundNo|是|String|向PomeloPay发起退款生成的唯一退款号
state|是|Integer| 0 退款成功 1 退款失败  
message|是|String|退款结果描述
refundAmount|是|String|退款金额
refundCurrency|是|String|退款币种






