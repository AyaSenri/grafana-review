# grafana-review

### version
7.3.1  
grafana-server only  
  
### 启动流程
#### main    
path：/pkg/cmd/grafana-server/main.go  

1.获取入参：  
  homepath --- grafana home目录，自编译安装不出意外均要设置，grafana将在该目录下寻找脚本，配置，前端静态资源等  
  pidfile --- 绑定固定pid地址，如若设置，应注意文件权限，防篡改  
  pprof及tracing不再赘述  
  
2.初始化pprof和tracing，失败则退出  
3.启动pprof  
4.executeServer   

#### executeServer  
path：/pkg/cmd/grafana-server/main.go  

1. 启动tracing  
2. 初始化server配置  
3. 起新线程signal方便接收exit  
4. (s *Server) Run启动server  

#### (s *Server) Run  
path：/pkg/server/server.go  

1. init  
  锁和初始化判断，频繁重启引发问题
  loadConfiguration加载配置
  metrics.SetEnvironmentInformation注册一个prometheus指标: grafana状态
  登录模块初始化，包括前端login.Init和oauth登录social.NewOAuthService
  初始化所有service
   
2. 启动注册的service    
3. 通知systemd已启动  

### 业务流程  
#### 登录  
