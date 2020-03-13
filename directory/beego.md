# cache 模块
## memory cache
```
bm, err := cache.NewCache("memory", `{"interval":10}`)
bm.Put("astaxie", 1, 1*time.Second)
fmt.Println(bm.Get("astaxie"),bm.IsExist("astaxie"))
bm.Delete("astaxie")
```
## 文件缓存
```
bm, err := cache.NewCache("file", `{"CachePath":"cache","FileSuffix":".cache","DirectoryLevel":"2"}`)
if err !=nil {
	fmt.Println("false")
}
bm.Put("xxx", 221, 0)
tt :=bm.Get("xxx")
fmt.Println(tt)
```
## redis缓存
```
mstr:=map[string]string{}
	mstr["key"]="ht"
	mstr["conn"]="192.168.1.8:6666"
	mstr["dbNum"]="0"
	bytes, _ := json.Marshal(mstr)
	//从redis缓存中拿数据
	cache_conn, err := cache.NewCache("redis", string(bytes))
	if err != nil {
		fmt.Println(err)
	}
	timeoutDuration := 10000000 * time.Second
	err = cache_conn.Put("test", "xxx", timeoutDuration)
	if err != nil {
		fmt.Println("数据读取出错，错误为：",err)
	}else {
		fmt.Println("redis 读取正常")
	}
	if areaData := cache_conn.Get("test");areaData!=nil{
		beego.Info("get data from cache===========")
		fmt.Println("从redis中读取出的数据为：",areaData)
	}else {
		fmt.Println("需要读取MySQL")
	}
```
## ssdb缓存
```
bm, err := cache.NewCache("ssdb", `{"conn":"192.168.1.8:8888"}`)
if err !=nil {
	fmt.Println("false")
}
bm.Put("xxx", "221", 10*time.Second)
tt :=bm.Get("xxx")
fmt.Println(tt)
```