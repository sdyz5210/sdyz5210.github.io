---
layout: post  
title: jupyter在mac无法启动的问题
date: 2018-2-11
tags: [linux,sftp]

--- 

### 1、问题描述

```

➜  ~ jupyter notebook
The Jupyter HTML Notebook.

这将启动一个基于tornado的HTML笔记本服务器，它提供一个html5/
javascript笔记本客户端。

Subcommands
-----------

Subcommands are launched as `jupyter-notebook cmd [args]`. For information on
using subcommand 'cmd', do: `jupyter-notebook cmd -h`.

stop
    Stop currently running notebook server for a given port
password
    Set a password for the notebook server.
list
    列出当前运行的Notebook服务.

Options
-------

Arguments that take values are actually convenience aliases to full
Configurables, whose aliases are listed on the help line. For more information
on full configurables, see '--help-all'.

--script
    DEPRECATED, IGNORED
--pylab
    DISABLED: use %pylab or %matplotlib in the notebook to enable matplotlib.
--debug
    set log level to logging.DEBUG (maximize logging output)
--no-browser
    在启动服务以后不在浏览器中打开一个窗口.
--allow-root
    允许notebook在root用户下运行.
-y
    Answer yes to any questions instead of prompting.
--no-mathjax
    Disable MathJax

    MathJax is the javascript library Jupyter uses to render math/LaTeX. It is
    very large, so you may want to disable it if you have a slow internet
    connection, or for offline use of the notebook.

    When disabled, equations etc. will appear as their untransformed TeX source.
--no-script
    DEPRECATED, IGNORED
--generate-config
    generate default config file
--certfile=<Unicode> (NotebookApp.certfile)
    Default: u''
    SSL/TLS 认证文件所在全路径.
--ip=<Unicode> (NotebookApp.ip)
    Default: 'localhost'
    notebook服务会监听的IP地址.
--pylab=<Unicode> (NotebookApp.pylab)
    Default: 'disabled'
    DISABLED: use %pylab or %matplotlib in the notebook to enable matplotlib.
--log-level=<Enum> (Application.log_level)
    Default: 30
    Choices: (0, 10, 20, 30, 40, 50, 'DEBUG', 'INFO', 'WARN', 'ERROR', 'CRITICAL')
    Set the log level by value or name.
--port-retries=<Integer> (NotebookApp.port_retries)
    Default: 50
    如果指定的端口不可用，则要尝试其他端口的数量.
--notebook-dir=<Unicode> (NotebookApp.notebook_dir)
    Default: u''
    用于笔记本和内核的目录。
--keyfile=<Unicode> (NotebookApp.keyfile)
    Default: u''
    SSL/TLS 私钥文件所在全路径.
--client-ca=<Unicode> (NotebookApp.client_ca)
    Default: u''
    用于ssl/tls客户端身份验证的证书颁发证书的完整路径.
--config=<Unicode> (JupyterApp.config_file)
    Default: u''
    Full path of a config file.
--port=<Integer> (NotebookApp.port)
    Default: 8888
    notebook服务会监听的IP端口.
--transport=<CaselessStrEnum> (KernelManager.transport)
    Default: 'tcp'
    Choices: [u'tcp', u'ipc']
--browser=<Unicode> (NotebookApp.browser)
    Default: u''
    Specify what command to use to invoke a web browser when opening the
    notebook. If not specified, the default browser will be determined by the
    `webbrowser` standard library module, which allows setting of the BROWSER
    environment variable to override it.

To see all available configurables, use `--help-all`

Examples
--------

    jupyter notebook                       # start the notebook
    jupyter notebook --certfile=mycert.pem # use SSL/TLS certificate
    jupyter notebook password              # enter a password to protect the server

[C 20:45:20.914 NotebookApp] Bad config encountered during initialization:
[C 20:45:20.915 NotebookApp] Could not decode '\xe6\x9c\xaa\xe5\x91\xbd\xe5\x90\x8d' for unicode trait 'untitled_notebook' of a LargeFileManager instance.


```

### 解决办法

在启动命令之前加上 LANG=zn 即命令为：

```

LANG=zn jupyter notebook

```