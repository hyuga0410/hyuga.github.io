---
layout:       post
title:        "企业微信发短信"
subtitle:     "如何使用企业微信发送短信"
date:         2019-12-27 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-12.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - WeChat
---

#### 相关网址

- [注册企业微信](https://work.weixin.qq.com/wework_admin/register_wx?from=myhome)
  - 需要手机号、以及实名认证

- [企业微信首页](https://work.weixin.qq.com/wework_admin/frame#index) 
  - 注册企业微信

- [企业微信应用管理](https://work.weixin.qq.com/wework_admin/frame#apps)
  - 创建应用
  - 获取已创建应用的`AgentId`和`Secret`
  - 可在应用后台直接发送消息等操作

- [我的企业](https://work.weixin.qq.com/wework_admin/frame#profile)
  - 获取企业ID
  - 查看企业信息
    
- [企业微信API](https://work.weixin.qq.com/api/doc/90001/90143/90372)
    
#### 创建企业微信应用步骤

- 注册企业微信
- 创建一个企业微信应用
- 记录下`企业ID`【字符串】、`应用ID`【数值型】、`应用密钥`【字符串】

#### 发送消息简易功能

- 获取tokenUrl：https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=%s&corpsecret=%s
- 发送消息url：https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=%s

{% highlight java %}
package hyuga.util.wechat.base;

/**
 * AbstractWeChatData
 *
 * @author hyuga
 * @date 2019-12-27 16:16
 */
public abstract class AbstractWeChatData {

    private long applicationId;
    /**
     * 成员账号（接收者的企业微信成员账号）
     */
    private String receiverMemberAccount;

    /**
     * 创建请求内容Json
     *
     * @return jsonBody
     */
    public abstract String createRequestJsonBody();

    public AbstractWeChatData(long applicationId) {
        this.applicationId = applicationId;
    }

    public long getApplicationId() {
        return applicationId;
    }

    public void setApplicationId(long applicationId) {
        this.applicationId = applicationId;
    }

    public String getReceiverMemberAccount() {
        return receiverMemberAccount;
    }

    public void setReceiverMemberAccount(String receiverMemberAccount) {
        this.receiverMemberAccount = receiverMemberAccount;
    }
}
{% endhighlight %}

> 纯文本 
{% highlight java %}
package hyuga.util.wechat.data;

import hyuga.util.collection.MapUtil;
import hyuga.util.json.JsonFastUtil;
import hyuga.util.wechat.base.AbstractWeChatData;

import java.util.Map;

/**
 * WeChatTextData
 *
 * @author hyuga
 * @date 2019-12-27 16:16
 */
public class WeChatTextData extends AbstractWeChatData {

    /**
     * 实际接收map类型数据
     */
    private String text;

    public WeChatTextData(long applicationId) {
        super(applicationId);
    }

    public String getText() {
        return text;
    }

    public void setText(String text) {
        this.text = text;
    }

    /**
     * 创建微信发送请求post数据 touser发送消息接收者 ，msgtype消息类型（文本/图片等）， application_id应用编号。
     * 本方法适用于text型微信消息，contentKey和contentValue只能组一对
     *
     * @return 创建发送消息
     */
    @Override
    public String createRequestJsonBody() {
        Map<Object, Object> weChatData = MapUtil.NEW();
        weChatData.put("touser", super.getReceiverMemberAccount());
        weChatData.put("msgtype", "text");
        weChatData.put("safe", 0);
        weChatData.put("agentid", super.getApplicationId());
        weChatData.put("text", MapUtil.NEW("content", this.text));

        return JsonFastUtil.toJson(weChatData);
    }

}
{% endhighlight %}

> 发射器
{% highlight java %}
package hyuga.util.wechat.transmitter;

import hyuga.base.constants.HyugaCharsets;
import hyuga.util.http.HyugaHttpSingleton;
import hyuga.util.http.base.HyugaHttp;
import hyuga.util.wechat.base.AbstractWeChatData;

/** 
 * WeChatDataTransmitter
 *
 * @author hyuga
 * @date 2019-12-27 16:15
 */
public class WeChatDataTransmitter {

    private static final HyugaHttp WECHAT_HTTP = HyugaHttpSingleton.init().charset(HyugaCharsets.UTF8).needSSL(true).printLog(true);

    /**
     * 用于获取token的url
     */
    private static final String WECHAT_TOKEN_URL = "https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=%s&corpsecret=%s";
    /**
     * 用于发送消息请求的url
     */
    private static final String WECHAT_MESSAGE_SEND_URL = "https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=";

    /**
     * 企业微信Id
     */
    private String enterpriseId;

    /**
     * 企业微信应用密钥
     */
    private String applicationSecret;

    public WeChatDataTransmitter(String enterpriseId, String applicationSecret) {
        this.enterpriseId = enterpriseId;
        this.applicationSecret = applicationSecret;
    }

    public String send(AbstractWeChatData weChatData) {
        String postData = weChatData.createRequestJsonBody();
        String url = WECHAT_MESSAGE_SEND_URL + this.getAccessToken();
        return WECHAT_HTTP.post(url, postData).getContent();
    }


    /**
     * 获取ToKen的请求
     */
    private String getTokenUrl() {
        return String.format(WECHAT_TOKEN_URL, this.enterpriseId, this.applicationSecret);
    }

    /**
     * 通过tokenUrl调用微信接口鉴权，获取鉴权令牌
     *
     * @return access_token
     */
    private String getAccessToken() {
        String tokenUrl = getTokenUrl();
        HyugaHttp.HttpResult httpResult = WECHAT_HTTP.get(tokenUrl);
        return httpResult.isOkAndHasContext().parsingObject("access_token").getString();
    }

}
{% endhighlight %}

> demo:
{% highlight java %}
package hyuga.util.wechat;

import hyuga.util.wechat.data.WeChatNewsData;
import hyuga.util.wechat.data.WeChatTextData;
import hyuga.util.wechat.data.WeChatTextcardData;
import hyuga.util.wechat.transmitter.WeChatDataTransmitter;

/**
 * WeChatSendUtil
 *
 * @author hyuga
 * @date 2019-12-27 14:41
 */
public class WeChatSendUtil {

    public static void main(String[] args) { 
        // 应用id 
        long applicationId = 1; 
        // 企业id 
        String enterpriseId = "xx1"; 
        // 应用编号 
        String applicationSecret = "xx2"; 
        String receiverMemberAccount = "xx3";

        WeChatDataTransmitter weChatDataTransmitter = WeChatSendUtil.init(enterpriseId, applicationSecret);

        //纯文本消息
        WeChatTextData weChatTextData = new WeChatTextData(applicationId);
        weChatTextData.setReceiverMemberAccount(receiverMemberAccount);
        weChatTextData.setText("这是一条纯文本测试信息");

        String weChatTextDataResp = weChatDataTransmitter.send(weChatTextData);
        System.out.println("发送微信纯文本消息的响应数据======>" + weChatTextDataResp);
    }

    public static WeChatDataTransmitter init(String enterpriseId, String applicationSecret) {
        return new WeChatDataTransmitter(enterpriseId, applicationSecret);
    }

}
{% endhighlight %}

#### PS
可以根据api文档继续拓展，微信消息支持 纯文本、卡片、图文、声音、文件等等，感兴趣的自行查阅。