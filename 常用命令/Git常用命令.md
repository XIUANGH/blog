# Git切换不同的代理
通过创建 git alias 或者写一个简单的脚本来快速切换代理。
使用 git alias ：
```bash
[alias]
    proxy7897 = "!git config --global http.proxy http://127.0.0.1:7897 && git config --global https.proxy https://127.0.0.1:7897"
    proxy10809 = "!git config --global http.proxy http://127.0.0.1:10809 && git config --global https.proxy https://127.0.0.1:10809"
    noproxy = "!git config --global --unset http.proxy && git config --global --unset https.proxy"
```
使用方法：
```bash
git proxy7897    # 切换到7897端口
git proxy10809   # 切换到10809端口
git noproxy      # 取消代理
```
创建批处理脚本