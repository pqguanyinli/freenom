# freenom：freenom域名自动续期

[![Build Status](https://scrutinizer-ci.com/g/luolongfei/freenom/badges/build.png?b=master)](https://scrutinizer-ci.com/g/luolongfei/freenom/build-status/master)
[![Php Version](https://img.shields.io/badge/php-%3E=5.6-brightgreen.svg)](https://secure.php.net/)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/luolongfei/freenom/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/luolongfei/freenom/?branch=master)
[![MIT License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](https://github.com/luolongfei/freenom/blob/master/LICENSE)

docker执行命令

docker run -d --name freenom -v /yourconf:/conf pqguanyinli/freenom-automatic-renewal 

### 前言
众所周知，freenom是地球上唯一一个提供免费顶级域名的商家，不过需要每年续期，每次续期最多一年。由于我申请了一堆域名，而且不是同一时段申请的，
所以每次续期都觉得折腾，于是就写了这个自动续期的脚本。

### 效果
![邮件示例](https://ws1.sinaimg.cn/large/a4d9cbc6ly1fypxmb6lgfj20g10fh7wh.jpg "邮件内容")

无论是续期成败或者脚本执行出错，都会收到的程序发出的邮件。如果是续期成败相关的邮件，邮件会包括未续期域名的到期天数等内容。
邮件参考了微信发送的注销公众号的邮件样式，微调一把，现在看到的这个效果还算满意。

### 使用方法
一言以蔽之。将config.php中的freenom账号和freenom密码改为自己的，以及邮箱信息也改为自己的，配置文件里都有注释，根据感觉改。
然后丢服务器上，创建crontab定时任务每天自动执行。

#### 原料准备
- 邮箱信息
- 本项目源码
- 一台VPS

#### 邮箱
*支持所有SMTP协议的邮箱*

1、记得在邮箱设置里打开POP和IMAP协议（以Gmail为例）

- 对所有邮件启用 POP 
- 启用 IMAP

![gmail配置01](https://ws1.sinaimg.cn/large/a4d9cbc6ly1fypxv92xm6j20j607ydg0.jpg "gmail配置01")

然后保存更改。

2、Gmail必须允许不够安全的应用

登录谷歌邮箱后，访问[谷歌权限设置界面](https://myaccount.google.com/u/2/lesssecureapps?pli=1&pageId=none)，启用允许不够安全的应用。

![gmail配置02](https://ws1.sinaimg.cn/large/a4d9cbc6ly1fypxvusmftj20k7060wek.jpg "gmail配置02")

3、可能遇到的坑
- 如果做了上两步操作，依然无法发送邮件，就将config.php中的mail键下的debug的值改为2，然后再手动执行，观察命令行输出：
```php
'mail' => [
        'debug' => 2
    ],
```
这样可以直接看到邮件不能发送的具体原因。
- 提示不允许访问账户

不允许访问账户，登录谷歌邮箱后，去[gmail的这个界面](https://accounts.google.com/b/0/DisplayUnlockCaptcha)点击允许。这种情况较为少见。

#### VPS
*在vps上安装git和lamp环境之类的我就不多赘述了，相信玩域名和vps的人都会，不会的可以去找一键脚本。本项目使用php编写，依赖php环境，且php版本需要>=5.6。
另外，以下操作使用的是Centos7，其它操作系统命令大同小异。*
#### clone本仓库源码
```bash
$ git clone https://github.com/luolongfei/freenom.git ./
```
#### 安装crontabs以及cronie
```bash
$ yum -y install cronie crontabs
```
#### 验证
##### 验证crond是否安装及启动
```bash
$ yum list cronie && systemctl status crond
```
##### 验证crontab是否安装
```bash
$ yum list crontabs $$ which crontab && crontab -l
```
#### 添加计划任务
##### 打开任务表单，并编辑
```bash
$ crontab -e

# 任务内容如下
# 此任务的含义是在每天早上8点执行/data/www/freenom.feifei.ooo/路径下的index.php文件
# 注意将/data/www/freenom.feifei.ooo/替换为你自己index.php所在路径
00 08 * * * cd /data/www/freenom.feifei.ooo/; php index.php >/dev/null 2>&1
```
##### 重启crond守护进程
```bash
$ systemctl restart crond
```
##### 查看当前crond状态
```bash
$ systemctl status crond
```
##### 查看当前计划任务列表
```bash
$ crontab -l
```
你可以先创建一个几分钟后执行的任务，测试一下程序能否正常工作，特别是测试邮件推送能否成功：你可以先故意将freenom密码配置改错，
执行程序理论上会收到登录出错或者其它错误的通知邮件的，测完后记得改正确。
**有很多人问我为什么执行成功了也没收到邮件：因为没有需要续期的域名，程序执行也没出错。**

遇到任何问题或bug欢迎提[issues](https://github.com/luolongfei/freenom/issues)，如果freenom改变算法导致此项目失效，
请提[issues](https://github.com/luolongfei/freenom/issues)告知，我会及时修复，本项目长期维护。欢迎star~

### 捐赠

如果这个项目有帮到你，可以请作者喝杯果汁或者加个卤蛋。项目的发展离不开大家的支持，无论是star或者donate，本人都衷心的感谢各位，也会尽自己的绵薄之力一直维护下去。

![pay](https://ws1.sinaimg.cn/large/a4d9cbc6gy1fyq22ype68j20jy0e5e81.jpg)


### 捐赠列表

此处仅展示最新20条赞赏，如果你赞赏了，可以在赞赏留言中写明或者请联系mybsdc@gmail.com为你添加。

金额 | 渠道 | 捐赠者 | 留言
:-----------: | :-----------: | :-----------: | :-----------:
¥20.00|支付宝|zoe***@126.com|
¥6.66|支付宝|jenchih 393***@qq.com| 
¥8.00|微信|匿名|非常感谢开发者的辛苦付出
¥8.68|微信|匿名|
¥5.00|微信|匿名|
¥30|微信|匿名|一点点心意
¥188|微信|xiaojingdou|感谢！省了不少时间！
¥68|支付宝|asdf***@163.com|	
¥66|支付宝|zhangw***@126.com|为你的项目点赞👍
¥88|微信|匿名|
¥50|支付宝|佳南 ux_***@yahoo.cn|
¥66.00|支付宝|驰 514***@qq.com|
¥20|微信|匿名|
¥10.00|支付宝|jenchih 393***@qq.com|

### 鸣谢
- [PHPMailer](https://github.com/PHPMailer/PHPMailer/)（邮件发送功能依赖此库）
- [php-curl-class](https://github.com/php-curl-class/php-curl-class)（Curl库）

### 开源协议
[MIT](https://opensource.org/licenses/mit-license.php)
