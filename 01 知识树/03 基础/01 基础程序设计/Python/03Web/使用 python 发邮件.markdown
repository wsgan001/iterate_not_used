

看到视频中说，如果你的机器学习模型跑完了，怎么才能第一时间收到通知呢？因为你不可能一直盯着它跑的。这的确是一个应用点。。


## 要点




### 1.要想可以发送邮件，首先要设定好可用的SMTP服务


一般是使用的第三方的SMTP服务，比如 QQ 邮箱(你也可以使用 163，Gmail等)的 SMTP 服务.

登入自己的QQ邮箱，点击设置，点击账户，然后往下拉，看到如下地方，将POP3/SMTP服务点为开启，然后点击生成授权码


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab4f6318ec61.png)


然后，QQ 邮箱 SMTP 服务器地址：smtp.qq.com，ssl 端口：465。

然后，发件人邮箱就是你的QQ邮箱，**密码就是刚生成的授权码。**

至此，SMTP的服务需要的信息都已经得到了。


### 2.然后就是代码：


代码如下：


    import smtplib
    from email.mime.text import MIMEText
    from email.utils import formataddr


    mail_sender = 'xxxxxxxx@qq.com'  # 用户名
    mail_senderpass = 'xxxxxxxxxx'  # 口令
    mailto_list = ['xxxxxxxx@qq.com', ]  # 可以自己发给自己

    mail_host = 'smtp.qq.com'  # 设置服务器名
    mail_port = 465


    def send_email(to_list, sub, content):
        try:
            msg = MIMEText(content, _subtype='html', _charset='gb2312')  # 创建一个实例，这里设置为html格式邮件
            msg['From'] = formataddr(["FromEVO", mail_sender])
            msg['To'] = ';'.join(to_list)
            msg['Subject'] = sub  # 邮件的主题，也可以说是标题

            server = smtplib.SMTP_SSL(mail_host, mail_port)  # 发件人邮箱中的SMTP服务器
            server.login(mail_sender, mail_senderpass)  # 括号中对应的是发件人邮箱账号、邮箱密码
            server.sendmail(mail_sender, to_list, msg.as_string())  # 括号中对应的是发件人邮箱账号、收件人邮箱账号、发送邮件
            server.quit()  # 关闭连接
            return True
        except:
            return False


    subject = '训练信息'
    content = '已经完成训练，信息为...'
    if send_email(mailto_list, subject, content):
        print("Mail Succeed!")
    else:
        print("Mail Failed!")


输出：


    Mail Succeed!


注：**将mail_sender修改为你自己的qq邮箱，将mail_senderpass修改为你刚从自己的qq邮箱中获得的授权码，mailto_list可以写自己的地址，支持自己发给自己。**

**上述代码尝试过了，是可行的。**


## COMMENT：





## REF：


1.[Python SMTP发送邮件](http://www.runoob.com/python/python-email.html)
