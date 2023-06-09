[**中文**](./README.md) | **English**

<h1 align="center">
    <br>
    OpenAI Forward
    <br>
</h1>
<p align="center">
    <b> OpenAI API 接口转发服务 <br/>
    The fastest way to deploy openai api forwarding </b>
</p>


[//]: # (    <a href="https://github.com/beidongjiedeguang">)

[//]: # (        <img alt="name" src="https://img.shields.io/badge/author-@kunyuan-orange.svg?style=flat-square&logo=appveyor">)

[//]: # (    </a>)

[//]: # (    <a href="https://github.com/beidongjiedeguang/openai-forward/stargazers">)

[//]: # (        <img alt="starts" src=https://img.shields.io/github/stars/beidongjiedeguang/openai-forward?style=social>)

[//]: # (    </a>)

[//]: # ([![Downloads]&#40;https://static.pepy.tech/badge/openai-forward/month&#41;]&#40;https://pepy.tech/project/openai-forward&#41;)

<p align="center">
    <a href="https://pypi.org/project/openai-forward/"><img src="https://img.shields.io/pypi/v/openai-forward?color=brightgreen" alt="PyPI version" ></a>
    <a href="https://github.com/beidongjiedeguang/openai-forward/blob/main/LICENSE">
        <img alt="License" src="https://img.shields.io/github/license/beidongjiedeguang/openai-forward.svg?color=blue&style=flat-square">
    </a>
    <a href="https://github.com/beidongjiedeguang/openai-forward/releases">
        <img alt="Release (latest by date)" src="https://img.shields.io/github/v/release/beidongjiedeguang/openai-forward">
    </a>
    <a href="https://github.com/beidongjiedeguang/openai-forward">
        <img alt="GitHub repo size" src="https://img.shields.io/github/repo-size/beidongjiedeguang/openai-forward">
    </a>
    <a href="https://hub.docker.com/r/beidongjiedeguang/openai-forward">
        <img alt="docer image size" src="https://img.shields.io/docker/image-size/beidongjiedeguang/openai-forward?style=flat&label=docker image">
    </a>
    <a href="https://github.com/beidongjiedeguang/openai-forward/actions/workflows/ci.yml">
        <img alt="tests" src="https://img.shields.io/github/actions/workflow/status/beidongjiedeguang/openai-forward/ci.yml?label=tests">
    </a>
    <a href="https://pypistats.org/packages/openai-forward">
        <img alt="pypi downloads" src="https://img.shields.io/pypi/dm/openai_forward">
    </a>
    <a href="https://codecov.io/gh/beidongjiedeguang/openai-forward">
        <img alt="codecov" src="https://codecov.io/gh/beidongjiedeguang/openai-forward/branch/dev/graph/badge.svg">
    </a>

</p>


<div align="center">

[Features](#Features) |
[Usage](#Usage) |
[Deploy](#Deploy) |
[Service Usage](#Service-Usage) |
[Configuration](#Configuration) |
[Chat Log](#Chat-log) 

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/tejCum?referralCode=U0-kXv)  
[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/beidongjiedeguang/openai-forward)

</div>

This project is designed to solve the problem of some regions being unable to directly access OpenAI. The service is deployed on a server that can access the OpenAI API, and OpenAI requests are forwarded through the service, i.e. a reverse proxy service is set up. 

---

由本项目搭建的长期代理地址：
> https://api.openai-forward.com  


# Features

**Basic Features**
- [x] Support forwarding all OpenAI interfaces.
- [x] Support streaming responses.
- [x] Support specifying the forwarding route prefix.
- [x] Docker deployment.
- [x] Pip installation deployment.
- [x] Cloudflare deployment.
- [x] ~~Vercel one-click deployment (not recommended)~~
- [x] Railway one-click deployment.
- [x] Render one-click deployment.

**Advanced Features**
- [x] Real-time recording of chat logs (including chat content from streaming responses).
- [x] Support default OpenAI API key (round-robin invocation of multiple API keys).
- [x] Custom forward API key instead of OpenAI API key (see advanced configuration).


# Usage

> Here, the proxy address set up by the individual, https://api.openai-forward.com, is used as an example

### [Chat app](https://chat.beidongjiedeguang.top)

Build your own ChatGPT service based on the open source
project [ChatGPT-Next-Web](https://github.com/Yidadaa/ChatGPT-Next-Web).
Replace `BASE_URL` in the docker startup command with the address of the proxy service we set up:

```bash 
docker run -d \
    -p 3000:3000 \
    -e OPENAI_API_KEY="sk-******" \
    -e BASE_URL="https://api.openai-forward.com" \
    -e CODE="kunyuan" \
    yidadaa/chatgpt-next-web 
``` 

Access to  https://chat.beidongjiedeguang.top. access code: `kunyuan`.

### Using in a module

**Python**

```diff
  import openai
+ openai.api_base = "https://api.openai-forward.com/v1"
  openai.api_key = "sk-******"
```

**JS/TS**

```diff
  import { Configuration } from "openai";
  
  const configuration = new Configuration({
+ basePath: "https://api.openai-forward.com/v1",
  apiKey: "sk-******",
  });
```

### Image Generation (DALL-E):

```bash 
curl --location 'https://api.openai-forward.com/v1/images/generations' \ 
--header 'Authorization: Bearer sk-******' \ 
--header 'Content-Type: application/json' \ 
--data '{ 
    "prompt": "A photo of a cat", 
    "n": 1, 
    "size": "512x512"
}' 
``` 

# Deploy

Just choose one.

## Use `pip`

**Installation**

```bash 
pip install openai-forward 
``` 

**Run forwarding service**
The port number can be specified through `--port`, which defaults to `8000`, and the number of worker processes can be
specified through `--workers`, which defaults to `1`.

```bash 
openai_forward run --port=9999 --workers=1 
``` 

The service is now set up, and the usage is to replace `https://api.openai.com` with the port number of the
service `http://{ip}:{port}`.

Of course, OPENAI_API_KEY can also be passed in as an environment variable as the default API key, so that the client
does not need to pass in the Authorization in the header when requesting the relevant route.
Startup command with default API key:

```bash 
OPENAI_API_KEY="sk-xxx" openai_forward run --port=9999 --workers=1 
``` 

Note: If both the default API key and the API key passed in the request header exist, the API key in the request header
will override the default API key.

## Use Docker (Recommended)

```bash 
docker run --name="openai-forward" -d -p 9999:8000 beidongjiedeguang/openai-forward:latest 
``` 

The 9999 port of the host is mapped, and the service can be accessed through `http://{ip}:9999`.
Note: You can also pass in the environment variable OPENAI_API_KEY=sk-xxx as the default API key in the startup command.

# Service Usage

Simply replace the OpenAI API address with the address of the service we set up, such as `Chat Completion`

```bash 
https://api.openai.com/v1/chat/completions 
``` 

Replace with

```bash 
http://{ip}:{port}/v1/chat/completions 
```

# Configuration

**`openai-forward run` Parameter Configuration Options**

| Configuration Item | Description | Default Value |
|-----------------|-------------------|:----------------------:|
| --port | Server port number | 8000 |
| --workers | Number of worker processes | 1 |
| --base_url | Same as OPENAI_BASE_URL | https://api.openai.com |
| --api_key | Same as OPENAI_API_KEY | `None` |
| --forward_key | Same as FORWARD_KEY | `None` |
| --route_prefix | Same as ROUTE_PREFIX | `None` |
| --log_chat | Same as LOG_CHAT | `False` |

**Environment Variable Configuration Options**  
refer to the `.env` file in the project root directory

| Environment Variable | Description                                                                                                                                                                                                                                                                                                |         Default Value        |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------:|
| OPENAI_BASE_URL      | Default OpenAI API address                                                                                                                                                                                                                                                                                 |    https://api.openai.com    |
| OPENAI_API_KEY       | Default OpenAI API key(s), support multiple default API keys starting with `sk-`, separated by spaces                                                                                                                                                                                                      |             None            |
| FORWARD_KEY          | Allow the caller to use this key instead of the OpenAI API key, support multiple forward keys separated by spaces; If OPENAI_API_KEY is set but FORWARD_KEY is not set, the key does not need to be provided when the client calls, it is not recommended to set FORWARD_KEY to empty for security reasons |             None            |
| ROUTE_PREFIX         | Route prefix                                                                                                                                                                                                                                                                                               |             None            |
| LOG_CHAT             | Whether to log the chat content                                                                                                                                                                                                                                                                            |           `false`           |
# Chat Log

The saved path is in the `Log/` directory under the current directory.  
The chat log starts with `chat_` and is written to the file every 5 rounds by default.  
The recording format is as follows:

```text
{'host': xxx, 'model': xxx, 'message': [{'user': xxx}, {'assistant': xxx}]}
{'assistant': xxx}

{'host': ...}
{'assistant': ...}

...
```

# Advanced Configuration

**Set the api_key to your own forward key**  
You need to configure OPENAI_API_KEY and FORWARD_KEY, for example:

```bash
OPENAI_API_KEY=sk-*******
FORWARD_KEY=fk-******
```
If you set FORWARD_KEY to `fk-******`, then the client only needs to set the OPENAI_API_KEY to `fk-******` when calling the API later.

**Examples:**  

**Python**
```diff
  import openai
+ openai.api_base = "https://api.openai-forward.com/v1"
- openai.api_key = "sk-******"
+ openai.api_key = "fk-******"
```
**Web application**
```bash 
docker run -d \
    -p 3000:3000 \
    -e OPENAI_API_KEY="fk-******" \
    -e BASE_URL="https://api.openai-forward.com/openai" \
    -e CODE="<your password>" \
    yidadaa/chatgpt-next-web 
``` 

# Backer and Sponsor

<a href="https://www.jetbrains.com/?from=beidongjiedeguang/openai-forward" target="_blank">
<img src=".github/images/jetbrains.svg" width="100px" height="100px">
</a>

# License

Openai-forward is licensed under the [MIT](https://opensource.org/license/mit/) license.
