# Ubuntu 20.04系统安装基于gitea自建git服务环境教程
# Ubuntu20 安装 Gitea

Gitea 是一个用 go 编写的快速且易于使用的自我管理 git[服务器](https://www.veidc.com/tag/%e6%9c%8d%e5%8a%a1%e5%99%a8 "【查看含有 \[服务器] 标签的文章】")[应用](https://www.veidc.com/tag/%e5%ba%94%e7%94%a8 "【查看含有 \[应用] 标签的文章】")程序。Gitea 包括存储库文件编辑器、项目问题跟踪、用户管理、通知、内置 Wiki 等。Gitea 是一个轻量级应用程序，可以安装在低功耗系统上。如果我们正在寻找内存占用较小的 gitlab 替代品，并且您不需要 gitlab 来提供复杂的函数，那么我们可以使用相对简单的 gitea。

[![](https://www.veidc.com/wp-content/uploads/2021/10/20211002_6158484ea85b1.jpg)
](https://www.veidc.com/wp-content/uploads/2021/10/20211002_6158484ea85b1.jpg)

本文，仍然以[搬瓦工](https://www.veidc.com/tag/%e6%90%ac%e7%93%a6%e5%b7%a5 "【查看含有 \[搬瓦工] 标签的文章】")[Ubuntu](https://www.veidc.com/tag/ubuntu "【查看含有 \[Ubuntu] 标签的文章】") 20.04 为例，我们在机器上安装和配置 gitea。

首先，必要的兼容环境

gitea 安装环境要求我们当前的服务器支持 SQLite、[PostgreSQL](https://www.veidc.com/tag/postgresql "【查看含有 \[PostgreSQL] 标签的文章】")和[MySQL](https://www.veidc.com/tag/mysql "【查看含有 \[MySQL] 标签的文章】")/MariaDB 作为[数据库](https://www.veidc.com/tag/%e6%95%b0%e6%8d%ae%e5%ba%93 "【查看含有 \[数据库] 标签的文章】")后端。如果我们的数据很小，我们可以使用 SQLite 数据库。如果我们的数据很大，建议使用 MySQL 或 PostgreSQL。

    sudo apt update
    sudo apt install [sql](https://www.veidc.com/tag/sql "【查看含有[sql]标签的文章】")ite3

第二，安装 gitea 服务

Gitea 提供可以从源代码、二进制文件和[软件](https://www.veidc.com/tag/%e8%bd%af%e4%bb%b6 "【查看含有 \[软件] 标签的文章】")包安装的 docker 映像。我们将从二进制文件安装 gitea。

1\. 安装 git:

    sudo apt update
    sudo apt install git

这里我们使用 Ubuntu 图像。

    git \--version

安装之后，我们使用命令检查版本。如果存在反馈数据版本，则安装已完成。

2\. 创建用户

    sudo adduser \\ \--system \\ \--shell /bin/bash \\ \--gecos 'Git Version Control' \\ \--group \\ \--disabled\-password \\ \--home /home/git \\
       git

上面的命令创建一个名为 GIT 的新用户和组，并将主目录设置为 / home/GIT。输出结果如下：

    Adding system user \`git' (UID 112) ...
    Adding new group \`git' (GID 118) ...
    Adding new user \`git'  (UID 112)  with  group  \`git' ...
    Creating home directory \`/home/git' ...

3\. 下载文件

转到 gitea 下载页面，下载适用于您的体系结构的最新二进制文件。在撰写本文时，最新版本为 1.10.2。如果有新版本可用，请在以下命令中更改版本变量。

使用 WGet 下载 / tmp 目录中的 gitea 二进制文件：

    VERSION\=1.14.1 sudo [wget](https://www.veidc.com/tag/wget "【查看含有[wget]标签的文章】")  \-O /tmp/gitea https://dl.gitea.io/gitea/${VERSION}/gitea-${VERSION}-linux-amd64

我们可以在任何地方运行 gitea 二进制文件。我们将按照约定将二进制文件移动到 / usr/local/bin 目录：

    sudo mv/tmp/gitea/usr/local/bin

使二进制文件可执行：

    sudo chmod+x/usr/local/bin/gitea

运行以下命令创建目录并设置所需的权限和所有权：

    sudo mkdir \-p /var/lib/gitea/{custom,data,log} sudo chown \-R git:git /var/lib/gitea/ sudo chmod \-R 750  /var/lib/gitea/ sudo mkdir /etc/gitea
    sudo chown root:git /etc/gitea
    sudo chmod 770  /etc/gitea

上述目录结构是 gitea 的官方文档推荐的 / etc/gitea 目录的权限设置为 770，以便安装向导可以创建配置文件。安装后，我们将设置更严格的权限。

4\. 创建系统单元文件

我们将 gitea 作为系统服务运行。

通过键入以下命令，将示例 SYSTEMd 单元文件下载到 / etc/SYSTEMd/system 目录：

    sudo wget https://raw.githubusercontent.com/go-gitea/gitea/main/contrib/systemd/gitea.service -P /etc/systemd/system/

然后我们需要开始。

    sudo systemctl daemon\-reload
    sudo systemctl enable \--now gitea

验证状态。

    sudo systemctl status gitea

看看返回值。

     gitea.service \-  Gitea  (Git  with a cup of tea)  Loaded: loaded (/etc/systemd/system/gitea.service; enabled; vendor preset: enabled)  Active: active (running) since Thu  2021\-05\-06  05:32:04 UTC;  7s ago Main PID:  77781  (gitea)  Tasks:  6  (limit:  470)  Memory:  130.6M  CGroup:  /system.slice/gitea.service └─77781  /usr/local/bin/gitea web \--config /etc/gitea/app.ini ...

5\. 配置 gitea

现在 gitea 已经下载并运行，我们可以通过 web 界面完成安装。默认情况下，gitea 侦听所有网络接口上端口 3000 上的连接。如果 UFW[防火墙](https://www.veidc.com/tag/%e9%98%b2%e7%81%ab%e5%a2%99 "【查看含有 \[防火墙] 标签的文章】")在我们的服务器上运行，我们需要打开 gitea 端口。要允许端口 3000 上的通信，请输入以下命令：

    sudo ufw allow 3000/tcp

打开[浏览器](https://www.veidc.com/tag/%e6%b5%8f%e8%a7%88%e5%99%a8 "【查看含有 \[浏览器] 标签的文章】")并输入[http://YOUR\\\_](http://YOUR\_) 域 uIRuuIP:3000，将出现以下屏幕：

[![](https://www.veidc.com/wp-content/uploads/2021/10/20211002_6158484ebd155.jpg)
](https://www.veidc.com/wp-content/uploads/2021/10/20211002_6158484ebd155.jpg)

相应地，我们需要在安装之前填写数据信息，这类似于我们的网站 CMS。

如果安装不好，您需要授权文件：

    sudo chmod 750  /etc/gitea
    sudo chmod 640  /etc/gitea/app.ini

第三，nginx 被配置为[SSL](https://www.veidc.com/tag/ssl "【查看含有 \[SSL] 标签的文章】")

是否安装 SSL 是可选的，但建议这样做。安装 SSL 后，这意味着 nginx 将充当 gitea 应用程序和 web 客户端之间的中介点，因此您可以通过[HTTP](https://www.veidc.com/tag/http "【查看含有 \[HTTP] 标签的文章】")S 访问 gitea。

首先，安装 nginx 并使用以下准则生成免费的 let’s 加密[SSL 证书](https://www.veidc.com/tag/ssl%e8%af%81%e4%b9%a6 "【查看含有 \[SSL 证书] 标签的文章】")：

完成后，打开文本编辑器并编辑域服务器块文件：

    sudo nano/etc/nginx/sites enabled/git.example.com

要配置：

    server { listen 80; server\_name git.example.com; include snippets/letsencrypt.conf;  return  301 https://git.example.com$request\_uri;  } server { listen 443 ssl http2; server\_name git.example.com; proxy\_read\_timeout 720s; proxy\_connect\_timeout 720s; proxy\_send\_timeout 720s; client\_max\_body\_size 50m;  \# Proxy headers proxy\_set\_header X\-Forwarded\-Host $host; proxy\_set\_header X\-Forwarded\-For $proxy\_add\_x\_forwarded\_for; proxy\_set\_header X\-Forwarded\-Proto $scheme; proxy\_set\_header X\-Real\-IP $remote\_addr;  \# SSL parameters ssl\_certificate /etc/letsencrypt/live/git.example.com/fullchain.pem; ssl\_certificate\_key /etc/letsencrypt/live/git.example.com/privkey.pem; ssl\_trusted\_certificate /etc/letsencrypt/live/git.example.com/chain.pem; include snippets/letsencrypt.conf; include snippets/ssl.conf;  \# log files access\_log /var/log/nginx/git.example.com.access.log; error\_log /var/log/nginx/git.example.com.error.log;  \# Handle / requests location /  { proxy\_redirect off; proxy\_pass http://127.0.0.1:3000;  }  }

根据需要修改。不要忘记用我们的 gitea 域替换 git.example.com，并设置 SSL 证书文件的正确路径。

最后，重新启动 nginx 以使其生效。

    sudo systemctl restart nginx

事实上，当我们安装 SSL 时，我们最好使用免费或付费证书，然后我们可以配置 SSL 文件。

接下来，更改 gitea 域和根 URL。我们需要打开配置文件并编辑以下行：

    sudo nano /etc/gitea/app.ini

编辑：

    \[server\] DOMAIN \= git.example.com
    ROOT\_URL \= https://git.example.com/

保存后重新启动生效

    sudo systemctl restart gitea

第四，配置电子邮件通知

如果我们希望我们的 gitea 实例发送通知电子邮件，我们可以安装 postfix 或使用一些事务性邮件服务，如 sendgrid、MailChimp、mailgun 或 SES。

要启用电子邮件通知，请打开配置文件并编辑以下行：

    sudo nano /etc/gitea/app.ini

编辑：

    \[mailer\] ENABLED \=  true HOST \=  [SMTP](https://www.veidc.com/tag/smtp "【查看含有[SMTP]标签的文章】")\_SERVER:SMTP\_PORT
    FROM \= SENDER\_EMAIL
    USER \= SMTP\_USER
    PASSWD \= YOUR\_SMTP\_PASSWORD

然后重新启动以生效

    sudo systemctl restart gitea

第五，如何升级 gitea

如果有新版本，我们如何升级 gitea。

1\. 先关闭服务

    sudo systemctl stop gitea

2\. 将最新文件下载到 / usr/local/bin 目录

    VERSION\= wget \-O /tmp/gitea https://dl.gitea.io/gitea/${VERSION}/gitea-${VERSION}-linux-amd64 sudo mv /tmp/gitea /usr/local/bin

3\. 执行文件

    sudo chmod+x/usr/local/bin/gitea

4\. 重新启动生效

    sudo systemctl restart gitea

通过这种方式，我们可以在服务器中部署 gitea。如果我们是个人或小团队，这就足够了。

## 网络测试

-   [美国 cn2 gia](https://www.veidc.com/tag/%e7%be%8e%e5%9b%bd-cn2-gia "【查看含有 \[美国 cn2 gia] 标签的文章】")：162.244.241.103\\104\\105\\106\\107
-   [日本软银](https://www.veidc.com/tag/%e6%97%a5%e6%9c%ac%e8%bd%af%e9%93%b6 "【查看含有 \[日本软银] 标签的文章】")：185.212.59.148\\149\\150\\151\\152
-   香港 cn2 gia：93.179.124.167\\168\\169\\170\\171\\172
-   荷兰联通：104.255.65.1

版权声明：本文采用知识共享 署名 4.0 国际许可协议 \[BY-NC-SA] 进行授权， 转载请注明出处。  
文章名称：《如果在搬瓦工 Ubuntu 20.04 系统安装基于 gitea 自建 git 服务环境教程》  
文章链接：[https://www.veidc.com/19773.html](https://www.veidc.com/19773.html)  
【声明】：国外主机测评仅分享信息，不参与任何交易，也非中介，所有内容仅代表个人观点，均不作直接、间接、法定、约定的保证，读者购买风险自担。一旦您访问国外主机测评，即表示您已经知晓并接受了此声明通告。  
【关于安全】：任何 IDC 商家都有倒闭和跑路的可能，备份永远是最佳选择，服务器也是机器，不勤备份是对自己极不负责的表现，请保持良好的备份习惯。 
 [https://www.veidc.com/19773.html](https://www.veidc.com/19773.html)
