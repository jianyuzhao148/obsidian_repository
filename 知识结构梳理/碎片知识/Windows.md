- 查看端口占用：netstat -ano
- 查看建立的网络连接并显示对应进程：netstat -nb
- 打开微软工具：msconfig
- PID结束进程：taskkill /T /F /PID <pid>
- 终端设置http(s)代理：set HTTP(S)_PROXY=http://user:password@proxy.domain.com:port
- powershell代理：
    - `netsh winhttp show proxy`
    - `netsh winhttp set proxy "127.0.0.1:7890"`
bat脚本变量赋值要不能有空格