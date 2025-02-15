---
title: 必看！本地安装 DeepSeek 超详细教程
categories: AI
tags: AI
abbrlink: 4e00340d
date: 2025-02-15 12:45:39
---

DeepSeek最近还是很火，直接把港股带飞。以至于问个问题给DeepSeek, 经常遇到服务器异常繁忙，请稍后尝试。
<!--more-->

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eB12hbTsL4Pf9IcsGicgrt7c7niarkahyzssuPVLibaed1icPDspqsJTxSQ/0?wx_fmt=png&from=appmsg)

所以不得不考虑在本地机器上运行，这样无需网络连接，不仅能随时随地使用，还能更好地保护隐私，不用担心数据上传带来的隐私风险。而且，完全免费。

本人是在Mac M1 Pro 16GB Memory 的电脑上做的测试，Windows也是支持的。



### Step1: 下载并安装 Ollama

用Ollama 运行 DeepSeek ，Ollama 可以管理和运行大型语言模型。可以把Ollama想象成Docker，大模型想象成docker image。Ollama的用法和Docker很类似。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1euZd2SX4KiczSMMY1ia2h3Yx99wtuiblxkhzeJuNEBO5Jm4l8HrFzcRXMQ/0?wx_fmt=png&from=appmsg)




打开浏览器，访问 Ollama 官方网站(https://ollama.com/)，在网站上找到 “Download for macOS” 按钮，点击下载适用于 macOS 的版本。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eT4sn0U4t6HvnGUvic3fzOD0rTNdbMDHTV3iarHN8SsiawQQyuqG2B76aQ/0?wx_fmt=png&from=appmsg)

下载完成后，打开 Finder，进入 “Downloads” 文件夹，找到刚刚下载的.zip 文件，双击解压。

解压后，将 Ollama 应用程序拖放到 “Applications” 文件夹中，完成安装。

打开 “Applications” 文件夹，找到 Ollama 并双击启动它。如果系统弹出安全提示，点击 “Open”。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1erSAgQBiasMrRNBafDstjrn018fND44MUWlR7psX6EICbBxT8OJaIIbw/0?wx_fmt=png&from=appmsg)

按照安装提示，安装命令行版本。当提示需要输入密码时，输入你的 Mac 密码进行安装。

安装完成后，在 Ollama 界面中点击 “Copy” 按钮，复制命令 “ollama run llama3.2” 到剪贴板。

打开终端，将剪贴板中的命令粘贴到终端中，回车。这一步是为了验证 Ollama 是否安装成功。



### Step2: 安装 DeepSeek

Ollama 安装好后，再次回到 Ollama 网站。在网站的搜索框中输入 “DeepSeek” 进行搜索。

在搜索结果中，选择 “DeepSeek R1”，因为它是最新版本，并且针对 Apple Mac 尤其是 Apple Silicon Mac 进行了优化，能让你的 Mac 更流畅地运行 DeepSeek。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1ecvGTiah0Au8mMpb4FxWpGmMbBLMdRDia7Mzqr6FGIWqoyjQjhArqnctw/0?wx_fmt=png&from=appmsg)

根据你的 Mac 性能来选择合适的模型大小。需要注意的是，基本版本 8B 资源消耗较少，适合配置不是特别高的 Mac，更大的模型虽然准确性更高，但对内存的需求也更大。如果你的Mac配置特别高，可以选择8B以上的模型。

选择好模型后，点击 “Copy” 按钮复制安装命令（例如：ollama run deepseek-r1:8b)。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eQSX3x0xIW7jibMYibib4Ik29SKLgxNawomYWDuzicfoicyfwGx6f5JqesyQ/0?wx_fmt=png&from=appmsg)

打开终端，将复制的安装命令粘贴到终端中，然后按下回车 键。此时，DeepSeek 模型（约 4.9GB）将开始下载并安装到你的计算机上。



### Step3: 在终端中运行 DeepSeek

当 DeepSeek 安装完成后，你就可以立即在终端中使用它了。在终端中输入你想要问的问题。

如果想退出与 DeepSeek 的对话，在终端中输入 “/bye”，然后按下 回车 键即可。

以后如果想要重新启动 DeepSeek，在终端中运行命令 “ollama run deepseek-r1:8b” 就可以了。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eCEvSSd3iblOGq0Qyv5vOc67Viaa2Mq8WhMzK4hotpx3vuInF2tQicz2Jw/0?wx_fmt=png&from=appmsg)



### Step4: 安装用户友好的聊天界面

虽然在终端中使用 DeepSeek 很方便，但如果想要更直观、更便捷的交互方式，可以安装 Chatbox AI 应用程序。这是一款免费的聊天应用程序，提供了与 ChatGPT 非常相似的图形用户界面，可以更轻松地与 DeepSeek 交流。

打开浏览器，访问 Chatbox AI 官方网站(https://chatboxai.app/)。下载并按照chatbox。

打开 Chatbox AI 应用程序后，你会看到一个界面，这里不需要付费，只需点击 “Use My Own API key / Local model”。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1exptNm6pSFia4BTTgXRKmtsFGVfxjicMSkerQGufUrDgUZVVuLhJ8dCbw/0?wx_fmt=png&from=appmsg)

在 “Model Provider” 下拉菜单中，选择 “Ollama API”。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eF5jRMLcgSztmFeIel8JtT2qia6ldZpdwibXSfCGRwYwyh6JlzicU38Jlw/0?wx_fmt=png&from=appmsg)

然后选择 “deepseek-r1:8b” 作为模型，然后点击右下角的 “Save” 按钮。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1eyCrcVgZVhd6DP0Y0kJiaH8DRaOQ38GaAKhvJjfd8Ow8ibqymRguN18eA/0?wx_fmt=png&from=appmsg)

完成以上步骤后，你就可以开始使用 Chatbox AI 与 DeepSeek 进行聊天了。

![image.png](https://mmbiz.qpic.cn/mmbiz_png/pHa9kbkQH5iaRNWHXR6icP9qIvf12icZib1e5SFVmiacVeo8dFrapiavB5AagHsdE9YzN6C98VEUKyYLR5xF2MicAvQXw/0?wx_fmt=png&from=appmsg)

