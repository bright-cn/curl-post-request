# 发送 cURL POST 请求

[![推广](https://github.com/bright-cn/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://www.bright.cn/) 

[cURL](https://www.bright.cn/blog/web-data/what-is-curl#:~:text=curl%2C%20short%20for%20%E2%80%9Cclient%20URL,%2C%20cars%2C%20and%20medical%20devices.) 是一款用于传输数据的命令行工具，最常见的用途是发送 HTTP 请求。你可以用它向 REST API 发送请求并抓取网站。本指南将介绍什么是 cURL POST 请求、如何发送该请求，以及发送时可以使用哪些命令行参数。

- [什么是 POST 请求](#什么是-post-请求)
- [安装 cURL](#安装-curl)
- [发起 POST 请求](#发起-post-请求)
  - [指定 POST 方法](#指定-post-方法)
  - [设置 Content-Type](#设置-content-type)
  - [发送数据](#发送数据)
  - [上传文件](#上传文件)
  - [发送凭据](#发送凭据)
- [重要的 cURL 参数](#重要的-curl-参数)
- [总结](#总结)

## 什么是 POST 请求

POST 请求是一种广泛用于向服务器传输数据的 HTTP 方法。

GET 请求会将数据附加到 URL 中，而 POST 请求则会把传输的数据包含在请求正文中。这种方式增强了隐私性，还可以绕过浏览器施加的 URL 长度限制，从而传输更多数据。

POST 请求通常用于提交表单、上传文件以及向 API 发送 JSON 数据。与 GET 请求不同，POST 请求通常不会被缓存，并且由于数据包含在请求正文中，因此不会出现在浏览器历史记录里。

## 安装 cURL

在尝试使用 cURL 之前，先确认你的系统是否已安装它。在终端中运行以下命令：

```bash
curl --version
```

如果看到提示该命令不可用的错误消息，你需要安装 cURL。

在使用 WinGet 的 Windows 11 上：

```bash
winget install curl.curl
```

在使用 Chocolatey 的 Windows 上：

```bash
choco install curl
```

在使用 Homebrew 的 macOS 上：

```bash
brew install curl
```

在 Ubuntu/Debian Linux 上：

```bash
apt-get install curl
```

在 Red Hat Enterprise Linux、CentOS 和 Fedora Linux 上：

```bash
yum install curl
```

如果你使用其他 Linux 发行版，请使用其原生软件包管理器（例如 openSUSE 上的 `zypper` 或 Arch 上的 `pacman`）。

## 发起 POST 请求

### 指定 POST 方法

发起 HTTP 请求时，你需要指定要使用的 HTTP 方法。最常见的方法包括 GET、POST、PUT 和 DELETE。要指定方法，请使用 `-X`（或 `--request`）命令行标志。

例如，要向 `https://httpbin.org/anything` 发送 POST 请求，请执行以下操作：

```bash
curl -X POST https://httpbin.org/anything
```

[httpbin.org](httpbin.org) 会在响应中原样返回请求正文和标头。你的响应应如下所示：

```json
{
  "args": {},
  "data": "",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.1.2",
    "X-Amzn-Trace-Id": "Root=1-11111111-111111111111111111111111"
  },
  "json": null,
  "method": "POST",
  "origin": "0.0.0.0",
  "url": "https://httpbin.org/anything"
}
```

### 设置 Content-Type

发起 POST 请求时，请在请求正文中定义内容类型，以确保服务器正确解析数据。具体方法是将 `Content-Type` 标头设置为适当的 MIME 类型。

例如，如果你要在请求正文中发送 JSON，就需要通过将 `Content-Type` 标头设置为 `application/json`，告诉 Web 服务器接收的是 JSON 内容。为此，请使用 `-H`（或 `--header`）标志：

```bash
curl -X POST -H 'Content-Type: application/json' -d '{}' https://httpbin.org/anything
```

### 发送数据

使用 `-d`（或 `--data`）标志可以指定要在请求正文中发送的数据。你可以使用该标志传递任何用引号括起来的值字符串，如下所示：

```bash
curl -X POST -H 'Content-Type: application/json' -d '{
    "FirstName": "Joe", 
    "LastName": "Soap" 
}' https://httpbin.org/anything
```

如果你希望 cURL 发送某个文件中的数据作为请求正文，请指定文件名，并在前面添加 `@` 字符：

```bash
curl -X POST -H 'Content-Type: application/json' -d @body.json https://httpbin.org/anything
```

#### 发送 JSON 数据

发送 JSON 数据时，请将 `Content-Type` 和 `Accept` 标头均设置为 `application/json`：

```bash
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -d '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

cURL 提供了一个 `--json` 标志（从 7.82 版本起可用），可自动设置这些标头：

```bash
curl -X POST --json '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

#### 发送 XML 数据

要发送 XML 数据，请设置适当的内容标头，并在请求正文中提供 XML：

```bash
curl -X POST -H 'Content-Type: application/xml' -H 'Accept: application/xml' -d '<Person>
    <FirstName>Joe</FirstName>
    <LastName>Soap</LastName>
</Person>' https://httpbin.org/anything
```

#### 发送 FormData

提交表单时，请使用 `-F`（或 `--form`）标志，并提供字段名称和值对：

```bash
curl -X POST -F FirstName=Joe -F LastName=Soap https://httpbin.org/anything
```

### 上传文件

要使用 FormData 上传文件，请在文件路径前添加 `@`：

```bash
curl -X POST -F File=@Contract.pdf https://httpbin.org/anything
```

### 发送凭据

对于基本身份验证，请使用 `-u`（或 `--user`）标志，并提供用户名和密码：

```bash
curl -X POST -u 'admin:password123' --json '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

## 重要的 cURL 参数

| 参数 | 完整选项 | 简要说明 |
|----------|-------------|-------------------|
| -X | --request | 指定使用的 HTTP 方法 |
| -d | --data | 发送请求正文数据 |
| -F | --form | 发送表单数据 |
| -H | --header | 设置请求标头字段 |
| -i | --include | 在输出中包含响应标头 |
| -v | --verbose | 显示额外的调试信息 |
| -L | --location | 自动跟随重定向 |
| -o | --output | 将响应写入文件 |
| -O | --remote-name | 使用远程文件名保存 |
| -C | --continue-at | 从指定偏移量恢复传输 |
| -u | --user | 设置基本身份验证凭据 |
| -x | --proxy | 使用指定的代理服务器 |
| -k | --insecure | 跳过 SSL 证书检查 |
| -I | --head | 仅获取标头（HEAD） |
| -b | --cookie | 发送已存储的 Cookie 数据 |
| -c | --cookie-jar | 将 Cookie 写入文件 |
| -e | --referer | 设置 Referer 标头 |
| -A | --user-agent | 设置自定义 User-Agent |
| -m | --max-time | 设置最长请求时间 |
| --max-filesize | （无短选项） | 限制下载文件的大小 |
| -z | --time-cond | 仅在指定时间后修改过时下载 |
| --compressed | （无短选项） | 请求压缩响应 |
| --data-urlencode | （无短选项） | 对数据字段进行 URL 编码 |
| --interface | （无短选项） | 绑定到指定接口 |
| -w | --write-out | 输出自定义响应信息 |
| -r | --range | 请求指定的字节范围 |
| -s | --silent | 隐藏进度输出 |
| -f | --fail | 出错时不显示错误信息 |
| -g | --globoff | 禁用方括号扩展 |
| --http1.0 | （无短选项） | 使用 HTTP/1.0 协议 |
| --http1.1 | （无短选项） | 使用 HTTP/1.1 协议 |
| --http2 | （无短选项） | 使用 HTTP/2 协议 |
| --http3 | （无短选项） | 使用 HTTP/3 协议 |
| -Z | --parallel | 同时发送多个请求 |
| --proxy-user | （无短选项） | 代理身份验证凭据 |
| --digest | （无短选项） | 使用 HTTP Digest 身份验证 |
| --ntlm | （无短选项） | 使用 HTTP NTLM 身份验证 |
| --negotiate | （无短选项） | 使用 SPNEGO/Kerberos 身份验证 |

## 总结

cURL 还提供了许多值得探索的附加功能，例如使用变量、发送 Cookie 以及配合代理使用。为了避免在发起自动化 HTTP 请求时暴露你的公网 IP 地址，可以考虑使用 [Bright Data 的代理服务](https://www.bright.cn/proxy-types)来隐藏你的 IP 地址。立即创建免费的 Bright Data 账户，体验我们的代理和网页爬虫解决方案！
