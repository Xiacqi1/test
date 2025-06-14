# go笔记

go初始化： go mod init 

go导入包：

```
go get -u github.com/gin-gonic/gin //路由
go get github.com/spf13/viper //管理viper
```

viper用法:

```
// Appconfig用于存储配置信息，使配置信息在整个应用程序中可用
var Appconfig *Config

// InitConfig用于初始化配置信息，读取配置文件并解析到Appconfig
func InitConfig() {
    // 设置配置文件的名称和类型，以及配置文件的路径
    viper.SetConfigName("config")
    viper.SetConfigType("yml")
    viper.AddConfigPath("./config")

    // 尝试读取配置文件，如果失败则记录错误日志并终止程序
    if err := viper.ReadInConfig(); err != nil {
       log.Fatalf("Error reading config file： %v", err)
    }
    // 初始化Appconfig，并尝试将读取到的配置信息解析到Appconfig
    Appconfig = &Config{}
    if err := viper.Unmarshal(&Appconfig); err != nil {
       log.Fatalf("Unable to decode into struct, %v", err)
    }
}
```

gin用法：

```
func main() {
    // 初始化配置信息
    config.InitConfig()

    // 创建一个默认的gin路由器
    r := gin.Default()

    // 注册一个GET路由处理函数
    // 当客户端请求"/ping"路径时，服务器会响应HTTP状态码200和一个简单的JSON消息"pong"
    //封装了整个请求的上下文信息c 是这个上下文对象的指针，你可以通过它来读取请求内容
    r.GET("/ping", func(c *gin.Context) {
       //gin.H 这是 Gin 提供的一个快捷方式，用于创建一个 map[string]interface{}，可以非常方便地构造 JSON 对象
       c.JSON(200, gin.H{
          "message": "pong",
       })
    })

    // 启动服务器，监听配置文件中指定的端口
    r.Run(config.Appconfig.App.Port)
}
```