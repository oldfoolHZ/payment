#查询接口

#### 请求参数

* 请求地址：
  * 生产：https://cashier.beyounger.com/gateway/payment/query
  * 沙盒：https://cashier-sandbox.beyounger.com/gateway/payment/query
* 请求方式：POST
* 请求头：Content-Type:application/json
* 请求参数：


> 退款查询请求参数

```json
{
  "merNo":"104001001",
  "merOrderNo":"asdfghjkl",
  "version":"V3.0.0",
  "sign":"9D6FDF4880B00B002B1F2AB61AE9A721",
  "queryType":"refund",
  "tradeNo": "DZ13576867867",
  "refundNo": "1232324324"
}
```

> 交易订单查询请求参数

```json
{
  "merNo":"104001001",
  "merOrderNo":"asdfghjkl",
  "version":"V3.0.0",
  "sign":"9D6FDF4880B00B002B1F2AB61AE9A721",
  "tradeNo": "DZ13576867867",
  "queryType":"sales"
}
```


参数名| 必填  |类型|说明|长度|备注
--------------|-----|--------------|--------------|--------------|--------------
merNo| 是   |Integer|商户号|10|可在商户后台注册获取
merOrderNo| 是   |String|商户订单号|32|
sign| 是   |String|签名|32|(merNo、merOrderNo、tradeNo、queryType、商户秘钥) 详细查看：签名
version| 是   |String|版本号|6|默认：V3.0.0
tradeNo| 否   |String|交易流水号|50| 当queryType为refund时 必传 
queryType| 是   |String|查询类型|16|退款查询：refund 交易订单查询:sales
refundNo| 否   |String|PomeloPay系统生成的唯一退款号|50|退款查询必传






<aside class="success">
    请求示例, 见右侧
</aside>

> 请求示例

```java
public class Test{
  public static void main(String[] args){
    JSONObject jsonObject = new JSONObject();
    jsonObject.put("merNo", "123455");
    jsonObject.put("merOrderNo", "1641972507000");
    //.....除签名(sign)以外的所有的body参数
    
    //签名
    String sign = MD5(merNo+merOrderNo+tradeNo+queryType+商户秘钥);
    jsonObject.put("sign", sign.toUpperCase());
    
    String url = "https://cashier.beyounger.com/gateway/payment/query";
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

> 交易查询响应成功示例

  ```json
  {
    "code": "00000",
    "message": "SUCCESS",
    "data": {
      "tradeNo": "DZ2202101809494673",
      "merOrderNo": "1644487789447",
      "merNo": 104001002,
      "state": "3"
    }
  }
  ```
> 退款查询响应成功示例
  ```json
  {
    "code": "00000",
    "message": "SUCCESS",
    "data": {
      "tradeNo": "DZ2202101809494673",
      "merOrderNo": "1644487789447",
      "refundNo": "202202101820320002",
      "merNo": 104001002,
      "refundCurrency": "CNY",
      "state": "6",
      "refundAmount": "5.00"
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

* 退款查询响应数据参数

参数名|必填|类型|说明
  --------------|--------------|--------------|--------------
state|是|Integer|  4 退款处理中 5 退款失败  6 退款成功
refundNo|是|String| 
refundAmount|是|String| 
refundCurrency|是|String|
tradeNo|是|String| 
merNo|是|String| 
merOrderNo|是|String| 

* 订单查询响应数据参数

参数名|必填|类型|说明
  --------------|--------------|--------------|--------------
state|是|Integer|  1 交易预处理  2 交易失败 3 交易成功
tradeNo|是|String|
merNo|是|String| 
merOrderNo|是|String|






