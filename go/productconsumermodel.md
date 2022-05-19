<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-05-18 22:22:06
 * @LastEditors: Alone
 * @LastEditTime: 2022-05-18 22:40:10
-->
# go版生产者消费者模型

```go
package main

import (
	"fmt"
	"time"
)

// 生产者
func Producer(inChannel chan<- int) {
	defer close(inChannel)
	for i := 1; i < 100; i++ {
		inChannel <- i
	}
	time.Sleep(time.Second)
}

// 消费者
func ConSumer(outChannel <-chan int) {

	for i := range outChannel {
		fmt.Println(i)
	}
	time.Sleep(time.Second)
}

func main() {
	mainInChannel := make(chan int, 3)

	// 先启动消费者
	go ConSumer(mainInChannel)
	go Producer(mainInChannel)

	time.Sleep(2 * time.Second)
}

```

sync.WaitGroup版:

```go
// 带waitgroup的生产者
func ProducerWithWaitGroup(inChannel chan<- int) {
	defer close(inChannel)
	for i := 1; i < 10; i++ {
		inChannel <- i
	}

}

// 带waitgroup的消费者
func ConSumerWithWaitGroup(id int, outChannel <-chan int, wg *sync.WaitGroup) {
	for {
		// 当通道没值了，ok就为空，进入else
		if i, ok := <-outChannel; ok {
			fmt.Printf("task: %d - %d\n", id, i)
		} else {
			break
		}
	}
	wg.Done()
}

func main() {
	groupInChannel := make(chan int, 10)
	var wg sync.WaitGroup
	go ProducerWithWaitGroup(groupInChannel)
	count := 3
	wg.Add(count)
	for i := 0; i < 3; i++ {
		go ConSumerWithWaitGroup(i, groupInChannel, &wg)
	}
	wg.Wait()
}
```

Mutex锁版:

```go
package main

import (
	"fmt"
	"sync"
)

var x int64
var wg sync.WaitGroup
var lock sync.Mutex

func Producer(taskId int) {
	fmt.Printf("Producer-task: %d\n", taskId)
	for i := 0; i < 5000; i++ {
		lock.Lock() // 加锁
		x = x + 1
		lock.Unlock() // 解锁
	}
	fmt.Printf("Producer - task %d :now x = %d\n", taskId, x)
	wg.Done()
}

func Consumer(taskId int) {
	fmt.Printf("Consumer-task: %d\n", taskId)
	for i := 0; i < 2000; i++ {
		lock.Lock() // 加锁
		x = x - 1
		lock.Unlock() // 解锁
	}
	fmt.Printf("Consumer - task %d :now x = %d\n", taskId, x)
	wg.Done()
}

func main() {
	wg.Add(7)
	for i := 0; i < 2; i++ {
		go Producer(i)

	}
	for i := 0; i < 5; i++ {
		go Consumer(i)
	}
	wg.Wait()
	fmt.Printf("Final Result: %d\n", x)
}

// Consumer-task: 4
// Consumer - task 4 :now x = -2000
// Consumer-task: 2
// Consumer-task: 3
// Producer-task: 0
// Producer-task: 1
// Consumer-task: 1
// Consumer-task: 0
// Consumer - task 2 :now x = -4000
// Consumer - task 3 :now x = -6000
// Producer - task 0 :now x = -1000
// Producer - task 1 :now x = 4000
// Consumer - task 1 :now x = 2000
// Consumer - task 0 :now x = 0
// Final Result: 0


```

