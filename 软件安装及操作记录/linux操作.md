干掉微信

```bash
ps aux | grep wechat
kill -9 PID
```



清理cpu缓存

```
echo 1 > /proc/sys/vm/drop_caches

1：仅清除页面缓存。
2：清除目录项（dentries）和 inode。
3：清除页面缓存、目录项以及 inode。
```



文件格式转换  pdf 转 markdown

```bash
sudo dnf install poppler-utils

pdftotext -layout input.pdf output.txt  #提取pdf文本

sudo pandoc -f gfm -t markdown -o output.md output.txt

```



























