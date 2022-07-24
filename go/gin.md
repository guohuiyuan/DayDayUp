# gin

- [gin](#gin)
  - [gin获取响应体内容](#gin获取响应体内容)
    - [1. 定义一个新的CustomResponseWriter，通过组合方式持有一个gin.ResponseWriter和response body缓存](#1-定义一个新的customresponsewriter通过组合方式持有一个ginresponsewriter和response-body缓存)
    - [2. 然后在中间件中进行替换和使用](#2-然后在中间件中进行替换和使用)
    - [3. 使用中间件](#3-使用中间件)

## gin获取响应体内容

参考: [如何在gin中获取响应体内容？](https://cloud.tencent.com/developer/article/1811436)

### 1. 定义一个新的CustomResponseWriter，通过组合方式持有一个gin.ResponseWriter和response body缓存

```
type CustomResponseWriter struct {
   gin.ResponseWriter
   body *bytes.Buffer
}

func (w CustomResponseWriter) Write(b []byte) (int, error) {
   w.body.Write(b)
   return w.ResponseWriter.Write(b)
}

func (w CustomResponseWriter) WriteString(s string) (int, error) {
   w.body.WriteString(s)
   return w.ResponseWriter.WriteString(s)
}
```

### 2. 然后在中间件中进行替换和使用

```
func AccessLogHandler() gin.HandlerFunc {
   return func(c *gin.Context) {
      blw := &CustomResponseWriter{body: bytes.NewBufferString(""), ResponseWriter: c.Writer}
      c.Writer = blw
      c.Next()
      fmt.Sprintf("url=%s, status=%d, resp=%s", c.Request.URL, c.Writer.Status(), blw.body.String())
   }
}
```

### 3. 使用中间件

```
//初始化中间件
func InitMiddleware(r *gin.Engine) {
   // 生成请求唯一id
   r.Use(setUUIDContext())
   //access log
   r.Use(AccessLogHandler())
   // 跨域请求处理
   r.Use(Cors())
   // 异常保护
   r.Use(Recover)
}
```