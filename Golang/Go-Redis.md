# Go-Redis

# 一. 操作Redis的库

Go 语言中操作 Redis 的库通常有这两个：

- **redigo**
- **go-redis**

https://github.com/gomodule/redigo

https://github.com/go-redis/redis 

# 二. 安装

```shell
go get github.com/gomodule/redigo/redis
go get github.com/go-redis/redis/v8
```

# 三. 连接测试

```go
var ctx = context.Background()
```

每个 Redis 命令都接受一个上下文，您可以使用该上下文设置超时或传播某些信息，例如跟踪上下文。

## 3.1 使用域名端口号连接

```go
var ctx = context.Background()
var rdb *redis.Client

func initializeRedisClient() (err error) {
    rdb = redis.NewClient(&redis.Options{
        Addr: "localhost:6391", 
        Password: "",
        DB: 0,
        // 连接池大小
        PoolSize: 100,
    })
    _, err = rdb.Ping(ctx).Result()
    return
}
```

## 3.2 使用连接串连接

```go
func initializeRedisClient() (err error) {
    opt, err := redis.ParseURL("redis://root:@localhost:6390/0")
    if err != nil {
        panic(err) 
    }
    rdb = redis.NewClient(opt)
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    _, err = rdb.Ping(ctx).Result()
    return err
}
```

## 3.3 哨兵模式

```go
func initializeRedisClient() error {
    rdb := redis.NewFailoverClient(&redis.FailoverOptions{
        MasterName:    "master",
        SentinelAddrs: []string{
            "10.0.0.10:6379", 
            "10.0.0.11:6379", 
            "10.0.0.12:6379",
        },
    })
    _, err := rdb.Ping().Result()
    return err 
}
```

## 3.4 集群模式

```go
func initializeRedisClient() error {
    rdb := redis.NewClusterClient(&redis.ClusterOptions{
        Addrs: []string{":7000", ":7001", ":7002", ":7003", ":7004", ":7006"},
    })
    _, err := rdb.Ping().Result()
    return err 
}
```

# 四. CRUD操作

## 4.1 string

**API：**

```go
// 设置键值
// 上下文 键名 键值 过期时间 
func (c Client) Set(ctx context.Context, key string, value interface{}, expiration time.Duration) *StatusCmd
// 获取键值
func (c Client) Get(ctx context.Context, key string) *StringCmd
```

```go
var ctx = context.Background()
var rdb *redis.Client

// 连接初始化省略...
func TestString(t testing.T) {
    err := rdb.Set(ctx, "key", "value", 0).Err()
    if err != nil {
    	panic(err) 
    }
    val, err := rdb.Get(ctx, "key").Result()
    if err != nil {
    	panic(err)
    }
    fmt.Println("key", val)
    val2, err := rdb.Get(ctx, "key2").Result()
    if err == redis.Nil {
        fmt.Println("key2 does not exist")
    } else if err != nil {
        panic(err)
    } else {
        fmt.Println("key2", val2)
    } 
}
```

## 4.2 list

**API：**

```go
// 列表插入函数
func (c Client) LPush(ctx context.Context, key string, values ...interface{}) *IntCmd
// 列表弹出元素函数
func (c Client) LPop(ctx context.Context, key string) *StringCmd
// 列表遍历元素
func (c Client) LRange(ctx context.Context, key string, start, stop int64) *StringSliceCmd
```

```go
func TestList(t testing.T) {
    num := rdb.LPush(ctx, "list1", 1, 2, 3, 4, 5).Val()
    fmt.Println(num)
    num1 := rdb.LPop(ctx, "list1")
    fmt.Println(num1)
    nums := rdb.LRange(ctx, "list1", 0, -1).Val()
    fmt.Println(nums)
}
```







## 4.3 set







## 4.4 hash





## 4.5 zset





