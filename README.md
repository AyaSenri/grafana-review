# grafana-review

#### version
7.3.1  
grafana-server only  
  
#### main  
路径：/pkg/cmd/grafana-server/main.go  

1.获取入参：  
  homepath --- grafana home目录，自编译安装不出意外均要设置，grafana将在该目录下寻找脚本，配置，前端静态资源等  
  pidfile --- 绑定固定pid地址，如若设置，应注意文件权限，防篡改  
  pprof及tracing不再赘述  
  
2.初始化pprof和tracing，失败则退出  
3.启动pprof  
4.启动grafana-server  

#### executeServer
路径：/pkg/cmd/grafana-server/main.go  

1. 启动tracing  
2. 初始化server配置  
3. 起新线程signal方便接收exit  
4. 启动server  
