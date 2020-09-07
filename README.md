## Mix Worker Pool

通用的工作池类库

A common workerpool class library

> 该库还有 php 版本：https://github.com/mix-php/worker-pool

## Usage

- 安装

```
go get -u github.com/mix-go/workerpool
```

- 使用

先创建一个 Worker 结构体

~~~
type FooWorker struct {
    workerpool.WorkerTrait
}

func (t *FooWorker) Do(data interface{}) {
    // do something
}

func (t *FooWorker) Error(err interface{}) {
    // handle err
}

func NewFooWorker() workerpool.Worker {
    return &FooWorker{}
}
~~~

调度任务

~~~
jobQueue := make(chan interface{}, 200)
d := workerpool.NewDispatcher(jobQueue, 15, NewFooWorker)

go func() {
    // 投放任务
    for i := 0; i < 10000; i++ {
        jobQueue <- i
    }

    // 投放完停止调度
    d.Stop()
}()

d.Run() // 等待任务全部执行完成并停止全部 Worker
~~~

注意：Worker Do 方法中，不可以使用 jobQueue 将任务又直接归还，会导致 Worker 全部阻塞，应该归还到 jobQueue 前面的逻辑，比如：jobQueue 是读取的 redis 的数据，那就可以归还到 redis 中。

## License

Apache License Version 2.0, http://www.apache.org/licenses/
