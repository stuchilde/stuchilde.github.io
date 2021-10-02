---
layout: post
title: IOS IKEv2/IPsec 安装教程
date: 2018-02-20 14:26:40
tags:
    - vpn
    - IKEv2/IPsec
---

对不起，此内容只适用于[英文](https://nordvpn.com/en/tutorials/ios/ikev2/)。 For the sake of viewer convenience, the content is shown below in the alternative language. You may click the link to switch the active language.

This is a tutorial on how to manually connect to NordVPN servers on iOS (iPhone/iPad), using the IKEv2 protocol.

1. First, **download the NordVPN IKEv2 certificate** to your device. The easiest way would be to open this link on the device itself: <https://downloads.nordvpn.com/certificates/root.der>

<!--more-->

1. The certificate installation dialog will appear. Tap **Install **in each of the steps.

   ![](https://ws1.sinaimg.cn/large/006tNc79gy1fomwxqbh1rj30tu0hqgm2.jpg)



![](https://ws1.sinaimg.cn/large/006tNc79gy1fomwy502xej30tu0fuq3l.jpg)

 

![](https://ws1.sinaimg.cn/large/006tNc79gy1fomwyj1xdgj30to0l8wf6.jpg)

 

3. Once installed, tap **Done**.

![](https://ws3.sinaimg.cn/large/006tNc79gy1fomwyytnvgj30tq0hmaag.jpg)

 

4. Open the **VPN **menu in your device **Settings**, and tap **Add VPN Configuration…**

![](https://ws3.sinaimg.cn/large/006tNc79gy1fomwz873etj316o0q2mya.jpg)

 

5. Type in these settings:

**Type**: IKEv2
**Description**: (any name you want, this field does not matter)
**Server**: The hostname of your chosen NordVPN server. You can find our server list with the hostnames here: <https://nordvpn.com/servers/> (in our case, we chose us333.nordvpn.com)

**Remote ID**: The same hostname as the Server field.
**Local ID**: Leave empty.
**User Authentication**: Username
**Username**: Your NordVPN account username.
**Password**: Your NordVPN account password.
**Proxy**: Off.

Afterwards tap **Done**.

![](https://ws3.sinaimg.cn/large/006tNc79gy1fomwzjuu82j30ts0yat9w.jpg)

 

6. The VPN configuration will appear in the VPN menu. Tap the **Status slider **to connect.

![](https://ws2.sinaimg.cn/large/006tNc79gy1fomwzwhnhbj316o0q2gmw.jpg)

 

7. Once the slider turns green, you will be successfully connected to NordVPN servers!