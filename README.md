# golang Web Framework  
golang搭建极简的原生WEB后台项目  

```go

  import (  
    "github.com/gkyh/gwf"  
    "net/http"  
   )  
 
   func main() {  
     
     route := gwf.New()  
     
     //实现RouteLogger接口的均可
     //type RouteLogger interface {
     //     Println(v ...interface{})
     // }
     var logger *log.Logger
     route.Logger(logger)
     
    srv := &http.Server{   
      Addr:           ":8080",   
      Handler:        route,   
      ReadTimeout:    5 * time.Second,   
      WriteTimeout:   10 * time.Second,   
      MaxHeaderBytes: 1 << 20,   
    }   

    //静态文件，参数1 请求url路径，参数2 请求文件路径
    r.Static("/public", "./public/") 

    //Favicon.ico文件路径  
    route.Favicon("./")

    //中间件  
    r.Use(ws)  
    r.Use(ws2)   
    r.Route("/test", testHandler, HandleIterceptor)  
    r.Group("/group", groupHandler)  
      
    r.Get("/login", func(req *http.Request,ctx *gwf.Context) {  

      session:= ctx.Session()
      session.Set("username", "root")
      ctx.WriteString(200, "login")   
    })  

    //注册路由必须现实 func(req *http.Request, c *gwf.Context) 格式的函数
    r.Get("/user", func(req *http.Request, c *gwf.Context) {  

      session:= c.Session()  
      user := c.Get("username")  
      if user !=nil {  
        c.WriteString(200, user.(string))   
      } else {  
        c.WriteString(404, "not found")  
      }  
    })  

    //r.Any("/any", func)  添加get 和 post 方法
    //r.Post("/post", func)  
    //r.Get("/get", func)  
    //r.Delete("/delete", func)  
   

    if err := srv.ListenAndServe(); err != nil {

      panic(err)
    }

  }
  func groupHandler(route *gts.Router){  
    
    //路由：/group/test
  	route.Get("/test",testFunc)
	  route.Post("/test",testFunc)
  }
  //中间件定义
  func ws(next gwf.HandlerFunc) gwf.HandlerFunc {  
     return func(ctx *gwf.Context) {  

     ip := getRemoteIp(ctx.Request)
      fmt.Println("request ip:" + ip)

	v := map[string]interface{}{
			"reqIP": ip,
	}
	ctx.Set("context", v)
	next(ctx)

    }  
  }  
  
  //拦截器，（也是中间件的一种)
  func HandleIterceptor(next gwf.HandlerFunc) gwf.HandlerFunc {. 
    return func(c *gwf.Context) {   
  
    ip := r.RemoteAddr   
    fmt.Println("handleIterceptor,ip:" + ip)   

    user, _ := c.SessionVal("username")
    if user != nil { 

      fmt.Println(user.(string))   
  
      v := gts.ContextValue{   
        "reqIP":    ip,   
        "username": user.(string),   
      }  
  
      c.Set("context", v)  
      next(c)  
      return  
    }  
  
    c.Redirect("/login")    
    return   
  
  }. 
  }
  ```
  
  ###TestConterller
  
  ```go 
  
  var testHandler *TestConterller = new(TestConterller)   
    
  type TestConterller struct {    
  }   
  //必须在Router方法中添加路由
  func (p *TestConterller) Router(router *gwf.Router) {

	  router.Any("/list", p.mlogHandler)  
  
  }  

 func (p *TestConterller) ListHanlder(req *http.Request,c *gwf.Context) {  
 
      req.ParseForm()
      data := req.FormValue("data")
      
      // 接收数据解析到struct
      form := &ReqForm{}
      err := gwf.Bind(req, form)
 
 }  
 ```
 
 Context 返回数据方法

```go   

  Write(b []byte)  
  
  WriteString(status int, s string)

  HTML(status int, html string)  
  
  JSON(status int, m map[string]interface{})  
  
  Result(s string)   
  
  Map(m map[string]interface{})
  
  OK()  //=〉{"code": 200, "msg": "处理成功"}
  
  Redirect(url string)   

```
