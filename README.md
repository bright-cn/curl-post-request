# 使用 cURL 发送 POST 请求

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://www.bright.cn) 

[cURL](https://www.bright.cn/blog/web-data/what-is-curl) 是一款用于传输数据的命令行工具，最常见的用途是发送 HTTP 请求。它可以用来向 REST API 发送请求并抓取网页。本文将介绍什么是 cURL POST 请求、如何发送 POST 请求，以及在此过程能使用到的命令行参数。

- [什么是 POST 请求](#什么是-post-请求)
- [安装 cURL](#安装-curl)
- [发起 POST 请求](#发起-post-请求)
  - [指定 POST 方法](#指定-post-方法)
  - [设置 Content-Type](#设置-content-type)
  - [发送数据](#发送数据)
    - [发送 JSON 数据](#发送-json-数据)
    - [发送 XML 数据](#发送-xml-数据)
    - [发送 FormData](#发送-formdata)
  - [上传文件](#上传文件)
  - [发送凭证](#发送凭证)
- [重要的 cURL 参数](#重要的-curl-参数)
- [结论](#结论)

## 什么是 POST 请求

POST 请求是一种被广泛使用的 HTTP 方法，用于向服务器传输数据。

与 GET 请求不同，GET 请求会将数据直接附加在 URL 之后，而 POST 请求会将要传输的数据放在请求正文（body）里。这样更能保护隐私，并且能够传输更多数据，不受浏览器对 URL 长度的限制。

POST 请求通常用于提交表单、上传文件以及向 API 发送 JSON 数据。与 GET 请求相比，它们通常不会被缓存，而且数据也不会显示在浏览器历史中（因为数据位于请求体中）。

## 安装 cURL

在开始使用 cURL 之前，先检查系统中是否已经安装。可以在终端运行以下命令：

```bash
curl --version
```

如果出现错误提示，说明无法使用 curl 命令，需要先进行安装。

在 Windows 11（通过 WinGet）：

```bash
winget install curl.curl
```

在 Windows（通过 Chocolatey）：

```bash
choco install curl
```

在 macOS（通过 Homebrew）：

```bash
brew install curl
```

在 Ubuntu/Debian Linux：

```bash
apt-get install curl
```

在 Red Hat Enterprise Linux、CentOS 和 Fedora Linux：

```bash
yum install curl
```

如果使用其他 Linux 发行版，请使用相应的原生包管理器（例如 openSUSE 的 `zypper` 或 Arch 的 `pacman`）。

## 发起 POST 请求

### 指定 POST 方法

在发起 HTTP 请求时，需要指定所用的 HTTP 方法。最常见的方法包括 GET、POST、PUT 和 DELETE。可以使用 `-X`（或 `--request`）选项来指定想要使用的方法。

例如，向 `https://httpbin.org/anything` 发送 POST 请求，可以执行：

```bash
curl -X POST https://httpbin.org/anything
```

[httpbin.org](httpbin.org) 会在响应中回显请求体和请求头，你应当会看到类似以下内容：

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

发送 POST 请求时，最好在请求头里指定内容类型（Content-Type），以便服务器正确解析数据。可以通过设置 `Content-Type` 请求头与正确的 MIME 类型来实现。

例如，如果要在请求体中发送 JSON，则需要将 `Content-Type` 设置为 `application/json`，可使用 `-H`（或 `--header`）选项：

```bash
curl -X POST -H 'Content-Type: application/json' -d '{}' https://httpbin.org/anything
```

### 发送数据

`-d`（或 `--data`）选项用于在请求体中指定要发送的数据。可以使用字符串形式（加上引号）：

```bash
curl -X POST -H 'Content-Type: application/json' -d '{
    "FirstName": "Joe", 
    "LastName": "Soap" 
}' https://httpbin.org/anything
```

如果有一个文件中包含要发送的数据，可以用 `@` 前缀加文件名的方式发送：

```bash
curl -X POST -H 'Content-Type: application/json' -d @body.json https://httpbin.org/anything
```

#### 发送 JSON 数据

若要发送 JSON 数据，需要同时设置 `Content-Type` 和 `Accept` 请求头为 `application/json`：

```bash
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -d '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

cURL 还提供了 `--json` 选项（7.82 版本后可用），它会自动设置这两个头：

```bash
curl -X POST --json '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

#### 发送 XML 数据

若要发送 XML 数据，同样需要正确设置请求头并在请求体中提供 XML：

```bash
curl -X POST -H 'Content-Type: application/xml' -H 'Accept: application/xml' -d '<Person>
    <FirstName>Joe</FirstName>
    <LastName>Soap</LastName>
</Person>' https://httpbin.org/anything
```

#### 发送 FormData

对于表单提交，可以使用 `-F`（或 `--form`）选项并附带表单字段名和对应值：

```bash
curl -X POST -F FirstName=Joe -F LastName=Soap https://httpbin.org/anything
```

### 上传文件

要用 FormData 上传文件，可以在字段值之前加上 `@`：

```bash
curl -X POST -F File=@Contract.pdf https://httpbin.org/anything
```

### 发送凭证

对于基本（Basic）身份验证，可以使用 `-u`（或 `--user`）选项，后跟用户名和密码：

```bash
curl -X POST -u 'admin:password123' --json '{
    "FirstName": "Joe",
    "LastName": "Soap"
}' https://httpbin.org/anything
```

## 重要的 cURL 参数

| 参数 | 完整写法 | 简要说明 |
|----------|-------------|-------------------|
| -X | --request | 指定要使用的 HTTP 方法 |
| -d | --data | 发送请求体数据 |
| -F | --form | 发送表单数据 |
| -H | --header | 设置请求头字段 |
| -i | --include | 在输出中包含响应头 |
| -v | --verbose | 显示更多调试信息 |
| -L | --location | 自动跟随重定向 |
| -o | --output | 将响应输出写入文件 |
| -O | --remote-name | 以远程文件名保存 |
| -C | --continue-at | 从指定偏移继续传输 |
| -u | --user | 设置基本身份验证凭据 |
| -x | --proxy | 使用指定的代理服务器 |
| -k | --insecure | 跳过 SSL 证书检查 |
| -I | --head | 仅获取响应头（HEAD 方法） |
| -b | --cookie | 发送存储的 Cookie 数据 |
| -c | --cookie-jar | 将 Cookie 写入文件 |
| -e | --referer | 设置 Referer 请求头 |
| -A | --user-agent | 设置自定义 User-Agent |
| -m | --max-time | 请求的最长时限 |
| --max-filesize | (无短选项) | 限制下载文件大小 |
| -z | --time-cond | 按修改时间下载 |
| --compressed | (无短选项) | 请求压缩响应 |
| --data-urlencode | (无短选项) | 对数据进行 URL 编码 |
| --interface | (无短选项) | 绑定到指定网络接口 |
| -w | --write-out | 输出自定义响应信息 |
| -r | --range | 请求指定的字节范围 |
| -s | --silent | 隐藏进度输出 |
| -f | --fail | 在出错时静默退出 |
| -g | --globoff | 禁用花括号扩展 |
| --http1.0 | (无短选项) | 使用 HTTP/1.0 协议 |
| --http1.1 | (无短选项) | 使用 HTTP/1.1 协议 |
| --http2 | (无短选项) | 使用 HTTP/2 协议 |
| --http3 | (无短选项) | 使用 HTTP/3 协议 |
| -Z | --parallel | 并行发送请求 |
| --proxy-user | (无短选项) | 为代理服务器设置身份验证 |
| --digest | (无短选项) | 使用 HTTP Digest 身份验证 |
| --ntlm | (无短选项) | 使用 HTTP NTLM 身份验证 |
| --negotiate | (无短选项) | 使用 SPNEGO/Kerberos 身份验证 |

## 结论

cURL 还有很多额外功能值得探索，例如使用变量、发送 Cookies 以及与代理服务配合使用。若想在进行自动化 HTTP 请求时隐藏真实 IP，可以考虑使用 [Bright Data 的代理服务](https://www.bright.cn/proxy-types) 来隐藏 IP 地址。立即创建一个免费的 Bright Data 账号，测试其代理与抓取解决方案吧！
