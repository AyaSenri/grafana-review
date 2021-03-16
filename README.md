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
  auth初始化，包括login.Init和oauth登录social.NewOAuthService  
  all services init  
   
2. run background services  
3. 通知systemd已启动  

#### loadConfiguration  
path：/pkg/server/server.go  
  
grafana支持的配置项  
注意： 
grafana采用的包ini中Must[type]方法需值类型不匹配时才会采用默认值  
因此当需要硬编码设置固定配置的情况请直接设置值  
例：  
硬编码级将cookie_secure默认打开的情形  
.MustBool(true)      ×    
CookieSecure = true  √    

#### auth初始化  
path：/pkg/server/server.go  
login.Init ==>  bus注册反射方法  
grafana内部核心调用机制，通过bus模块反射  
  
NewOAuthService：  
根据配置的oauth服务，初始化一个OAuthService  
  
#### services init  
path：/pkg/server/server.go  
service模块会在各自的init中调用RegisterService注册自身模块  
并对以下interface做实现   
Service：  Init初始化  
CanBeDisabled:  根据配置判断isDisabled  
BackgroundService:  后台服务将被调用run   
  
#### BackgroundService  
  
HTTPServer  
  grafana web采用macaron框架  
  注意: grafana对static资源并未支持安全请求头等安全设置，且macaron自带302重定向机制,应审视其安全问题  
  https://github.com/go-macaron/macaron/issues/198  
  handlers:  
  middleware.Logger()  
  middleware.Gziper()  
  middleware.Recovery()  
  httpstatic.Static()  
  middleware.AddDefaultResponseHeaders()    
  macaron.Renderer()  
  hs.healthzHandler  
  hs.apiHealthHandler  
  hs.metricsEndpoint  
  middleware.GetContextHandler()  
  middleware.OrgRedirect()  
  middleware.ValidateHostHeader()
  middleware.HandleNoCacheHeader()
  
RenderingService  
  
manager  
  
PluginManager  
  
CleanUpService  
  
RemoteCache  
  
InternalMetricsService  
  
AlertEngine   
  
UserAuthTokenService  
  
UsageStatsService  
  
TracingService  
  
NotificationService  
  
ProvisioningService  
  
### 业务流程  
#### 登录  



