## 需求

实时检测当前系统CPU使用频率，如果超过指定阈值，则发送警示邮件。



## 实现

```python
--Config.py
# -*- coding: UTF-8 -*-
from enum import Enum
from getpass import getpass

class Config(Enum):
    # 刷新次数
    NUMBER_OF_REFRESH = 3
    # CPU阈值
    CPU_THRESHOLD = 40
    # 邮箱服务器地址
    EMAIL_SERVER_ADDRESS = 'smtp.163.com'
    # 邮箱服务器端口
    EMAIL_SERVER_PORT = 465
    # 发送邮箱 可以是列表['to_someone@qq.com']且直接使用
    EMAIL_FROM_ADDRESS = getpass('From Email: ')
    # 发送邮箱密码
    EMAIL_FROM_PASSWORD = getpass('From Email Password: ')
    # 目标邮箱
    EMAIL_TO_ADDRESS = getpass('To Email: ')


```

```python
--Cpu.py
# -*- coding: UTF-8 -*-

import psutil


class Cpu(object):

    def __init__(self):
        pass

    # 获取CPU平均使用率
    def get_cpu_svg(self):
        cpu_list = psutil.cpu_percent(interval=1, percpu=True)
        size = len(cpu_list)
        count = 0
        for num in cpu_list:
            count += num

        svg = count/size
        # print('CPU总占比 %s, 核心数 %s, 平均使用率为 %s' % (count, size, svg))
        return round(svg, 2)



    # 获取CPU核数
    def get_cpu_auditing(self):
        return psutil.cpu_count(logical=False)



if __name__ == '__main__':
    cpu = Cpu()
    print(cpu.get_cpu_svg())
```

```python
--Email.py
# -*- coding: UTF-8 -*-
# 选择pyEmail 而不是 smtplib
import ssl
import smtplib
from Config import Config
# from email.message import EmailMessage
from email.mime.text import MIMEText

class Email(object):

    def __init__(self):
        # self.smtp = smtplib.SMTP(Config.EMAIL_SERVER_ADDRESS.value, Config.EMAIL_SERVER_PORT.value)
        # self.context = ssl.create_default_context()
        pass


    def send_email(self, subject, body):
        message = MIMEText(body, 'plain', 'utf-8')  # 内容, 格式, 编码
        message['From'] = "{}".format(Config.EMAIL_FROM_ADDRESS.value)
        message['To'] = ",".join(Config.EMAIL_TO_ADDRESS.value)
        message['Subject'] = subject

        try:
            smtpObj = smtplib.SMTP_SSL(Config.EMAIL_SERVER_ADDRESS.value, 465)  # 启用SSL发信, 端口一般是465
            smtpObj.login(Config.EMAIL_FROM_ADDRESS.value, Config.EMAIL_FROM_PASSWORD.value)  # 登录验证
            smtpObj.sendmail(Config.EMAIL_FROM_ADDRESS.value, Config.EMAIL_TO_ADDRESS.value, message.as_string())  # 发送
            print("mail has been send successfully.")
        except smtplib.SMTPException as e:
            print(e)

        # msg['subject'] = subject
        # msg['From'] = Config.EMAIL_FROM_ADDRESS.value
        # msg['To'] = Config.EMAIL_TO_ADDRESS.value
        # msg.set_content(body)
        #
        # with smtplib.SMTP_SSL(Config.EMAIL_SERVER_ADDRESS.value, Config.EMAIL_SERVER_PORT.value, context=self.context) as smtp:
        #     smtp.login(Config.EMAIL_FROM_ADDRESS.value, Config.EMAIL_FROM_PASSWORD.value)
        #     smtp.send_message(msg)
        #     smtp.close()

    def send_email_with_annex(self, subject, body, file_addr, file_type):
        pass
        # msg = EmailMessage()
        # msg['subject'] = subject
        # msg['From'] = Config.EMAIL_FROM_ADDRESS.value
        # msg['To'] = Config.EMAIL_TO_ADDRESS.value
        # msg.set_content(body)
        # with open(file_addr, 'rb') as f:
        #     file_data = f.read()
        #     f.close()
        #
        # msg.add_attachment(file_data, maintype=file_type, filename=file_addr)
        #
        # with smtplib.SMTP_SSL(Config.EMAIL_SERVER_ADDRESS.value, Config.EMAIL_SERVER_PORT.value, context=self.context) as smtp:
        #     smtp.login(Config.EMAIL_FROM_ADDRESS.value, Config.EMAIL_FROM_ADDRESS.value)
        #     smtp.send_message(msg)
        #     smtp.close()



if __name__ == '__main__':
    email = Email()
    email.send_email('测试发送邮件标题', '测试发送邮件内容')
```

```python
--main.py
# -*- coding: UTF-8 -*-

import time
import schedule
from Cpu import Cpu
from Email import Email
from Config import Config


cpu = Cpu()
email = Email()

def watch_cpu():
    svg = cpu.get_cpu_svg()
    if svg > Config.CPU_THRESHOLD.value:
        print('CPU使用率已超过阈值 [%d%%], 目前使用率为 [%s%%]' % (Config.CPU_THRESHOLD.value, svg))
        # 发送邮件
        email.send_email('[⚠️]警告: Mac CPU 飚高', 'CPU使用率已超过阈值 [%d%%], 目前使用率为 [%s%%]' % (Config.CPU_THRESHOLD.value, svg))

schedule.every(Config.NUMBER_OF_REFRESH.value).seconds.do(watch_cpu)

while True:
    schedule.run_pending()
    time.sleep(1)
```



