# Golang

### GO安装

**下载安装包**

**删除旧版本的Go**（如果有）： 删除旧版本的Go文件夹（如果存在）：

```
rm -rf /usr/local/go
```

**解压安装包**： 将下载的安装包解压到 `/usr/local` 目录。例如，如果下载的文件名为 `go1.23.1.linux-amd64.tar.gz`，则可以使用以下命令：

```
tar -C /usr/local -xzf go1.23.1.linux-amd64.tar.gz
```

这将创建 `/usr/local/go` 目录。

**设置环境变量**： 打开 `~/.profile` 或 `~/.bashrc` 文件，并添加以下行：

```
export PATH=$PATH:/usr/local/go/bin
```

然后，应用更改：

```
source ~/.profile
```

或者，如果你使用的是全局配置文件：

```
source /etc/profile
```

**验证安装**： 打开终端，并输入以下命令来验证Go是否正确安装：

```
go version
```

如果安装成功，它将输出Go语言的版本号。