# JsWxPay
控制器中代码为:
<?php
namespace app\wx\controller;

use wxpay\JsApiPay;
use wxpay\lib\WxPayUnifiedOrder;
use wxpay\lib\WxPayApi;
use wxpay\lib\WxPayConfig;

class WeController
{
    /**
     * 支付
     * @author jhj
     */
    public function index()
    {
        $tools = new JsApiPay();
        //②、统一下单 'oO8Clwkkt-jvRRX0-TWe4tVdP3b4'
        $input = new WxPayUnifiedOrder();
        $input->SetBody("test");
        $input->SetAttach("test");
        $input->SetOut_trade_no(WxPayConfig::MCHID.date("YmdHis"));
        $input->SetTotal_fee("1");
        $input->SetTime_start(date("YmdHis"));
        $input->SetTime_expire(date("YmdHis", time() + 600));
        $input->SetGoods_tag("test");
        $input->SetNotify_url("http://mobile.meiyuzhixing.com/wx/wepay/zf");
        $input->SetTrade_type("JSAPI");
        $input->SetOpenid(session('test.openid'));
        $order = WxPayApi::unifiedOrder($input);
        $jsApiParameters = $tools->GetJsApiParameters($order);
        echo $jsApiParameters;
    }
}

html代码为:
<html>
<head>
    <meta http-equiv="content-type" content="text/html;charset=utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1"/> 
    <title>微信支付样例-支付</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
    <script type="text/javascript">
	//调用微信JS api 支付
	function jsApiCall()
	{
		$.ajax({

             type: "POST",

             url: "/wx/we/index",

             data: '',

             dataType: "json",

             success: function(data){
             	console.log(data);

				WeixinJSBridge.invoke(
					'getBrandWCPayRequest',
					data,
					function(res){
						WeixinJSBridge.log(res.err_msg);
						alert(res.err_code+res.err_desc+res.err_msg);
					}
				);
             }
             });
	}

	function callpay()
	{
		if (typeof WeixinJSBridge == "undefined"){
		    if( document.addEventListener ){
		        document.addEventListener('WeixinJSBridgeReady', jsApiCall, false);
		    }else if (document.attachEvent){
		        document.attachEvent('WeixinJSBridgeReady', jsApiCall); 
		        document.attachEvent('onWeixinJSBridgeReady', jsApiCall);
		    }
		}else{
		    jsApiCall();
		}
	}
	</script>
</head>
<body>
    <br/>
    <font color="#9ACD32"><b>该笔订单支付金额为<span style="color:#f00;font-size:50px">1分</span>钱</b></font><br/><br/>
	<div align="center">
		<button style="width:210px; height:50px; border-radius: 15px;background-color:#FE6714; border:0px #FE6714 solid; cursor: pointer;  color:white;  font-size:16px;" type="button" onclick="callpay()" >立即支付</button>
	</div>
</body>
</html>

