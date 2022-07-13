# 加锁

- [加锁](#加锁)
	- [单飞模式](#单飞模式)
		- [普通版本，无归并操作](#普通版本无归并操作)
		- [单飞模式版本](#单飞模式版本)
	- [redis分布式锁](#redis分布式锁)
## 单飞模式
参考文档[Go里面竟然还有单飞](https://zhuanlan.zhihu.com/p/415585420)

在数据查询过程中经常会遇到先读redis,然后redis没读到就读db,然后有好几个协程进行此操作,为了防止redis出问题,然后疯狂读db,然后就有了单飞模式。


### 普通版本，无归并操作

```
package main

import (
  "errors"
  "log"
  "sync"
)

var errorNotExist = errors.New("redis: key not found")

func main() {
  var wg sync.WaitGroup
  wg.Add(10)

  
  for i := 0; i < 10; i++ {
    go func() {
      defer wg.Done()
      data, err := getData("2000")
      if err != nil {
        log.Print(err)
        return
      }
      log.Println(data)
    }()
  }
  wg.Wait()
}


func getData(key string) (string, error) {
  data, err := getDataFromCache(key)
  if err == errorNotExist {
    
    data, err = getDataFromDB(key)
    if err != nil {
      log.Println(err)
      return "", err
    }

    
  } else if err != nil {
    return "", err
  }
  return data, nil
}


func getDataFromCache(key string) (string, error) {
  return "", errorNotExist
}


func getDataFromDB(key string) (string, error) {
  log.Printf("get %s from database", key)
  return "2000 in db", nil
}
```

### 单飞模式版本

```
package main

import (
	"errors"
	"log"
	"sync"

	"golang.org/x/sync/singleflight"
)

var singleFlightTest singleflight.Group
var errorNotExist = errors.New("redis: key not found")

func main() {
	var wg sync.WaitGroup
	wg.Add(10)

	for i := 0; i < 10; i++ {
		go func() {
			defer wg.Done()
			data, err := getDataBySingleFlight("2000")
			if err != nil {
				log.Print(err)
				return
			}
			log.Println(data)
		}()
	}
	wg.Wait()
}

func getDataBySingleFlight(key string) (string, error) {
	data, err := getDataFromCache(key)
	if err == errorNotExist {

		v, err, _ := singleFlightTest.Do(key, func() (interface{}, error) {
			return getDataFromDB(key)

		})
		if err != nil {
			log.Println(err)
			return "", err
		}

		data = v.(string)

	} else if err != nil {
		return "", err
	}
	return data, nil
}

func getDataFromCache(key string) (string, error) {
	return "", errorNotExist
}

func getDataFromDB(key string) (string, error) {
	log.Printf("get %s from database", key)
	return "2000 in db", nil
}
```

## redis分布式锁

采用Redis的原子性命令“SET key value EX expire-time NX”可以实现分布式锁的基本功能，其中的NX（Not Exist）即判断是否已存在锁，如果不存在key则可进行操作，SET key value 等同于加锁，EX expire-time即设置超时时间，可以避免死锁，但是超时时间的设置需要根据具体业务设置一个合理的经验值，避免锁超时时间到了，业务没执行完的问题

[Redis分布式锁原理及go的实现](https://blog.csdn.net/WonderThink/article/details/106008588)

