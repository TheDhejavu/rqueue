# RQueue
Bull-like job queue system in golang

## Queue instance 

```go

// new queue
func NewQueue( queueName string) *rqueue.Queue {
    return rqueue.NewRQueue(queueName,opts.RQConfig{
         // how many concurrent workers to use
         Concurrency: 5,
         // Multiple queues with different priority.
         QueueGroup: map[string]int{
	    "critical": 6,
	    "default":  3,
	    "low":      1,
          },
    })
}

// new scheduler queue
func NewSchedulerQueue( queueName string) *rqueue.Queue {
    return rqueue.NewSchedulerQueue(queueName,opts.RQConfig{
         // how many concurrent workers to use
         Concurrency: 5,
         // Multiple queues with different priority.
         QueueGroup: map[string]int{
	    "critical": 6,
	    "default":  3,
	    "low":      1,
          },
    })
}

queue1 := NewQueue("queue-name1")
queue2 := NewQueue("queue-name2")
queue3 := NewQueue("queue-name3")
schedulerQueue1 := NewSchedulerQueue("scheduler-queue-1")

```

### Run Queue on 1 or more instance
```go
// queue 1
queue1.Run(func(ctx context.Context){
   // do something..
})

// queue 2
queue2.Run(func(ctx context.Context){
   // do something..
})

```


### Publish to queue
```go
// queue 1
queue1.Publish(
     payload,
     rqueue.QueueGroup("critical"),
     rqueue.Unique(20*time.Minute),
     rqueue.MaxRetry(2),
     rqueue.Timeout(5*time.Minute),
     rqueue.Retention(20*time.Minute),
)

// queue 2
queue2.Publish(
     payload,
     rqueue.QueueGroup("low"),
     rqueue.Unique(20*time.Minute),
     rqueue.MaxRetry(2),
     rqueue.Timeout(5*time.Minute),
     rqueue.Retention(20*time.Minute),
)

```

### Scheduler
```go
schedulerQueue1.Schedule("@every 1m", func(ctx context.Context){
   // do something
  queue2.Publish(
       payload,
       rqueue.QueueGroup("low"),
       rqueue.Unique(20*time.Minute),
       rqueue.MaxRetry(2),
       rqueue.Timeout(5*time.Minute),
       rqueue.Retention(20*time.Minute),
   )
 } )
```
###
