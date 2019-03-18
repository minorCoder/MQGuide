# RocketMQ学习笔记-简单例子

- ## 用RocketMQ去发送消息有三种方式：可靠的同步，可靠的异步，和单向传输

- ## 使用RocketMQ消费消息

  #### 1. Add Dependency

  ### maven：

  ~~~maven
  	<dependency>
          <groupId>org.apache.rocketmq</groupId>
          <artifactId>rocketmq-client</artifactId>
          <version>4.3.0</version>
      </dependency>
  ~~~

  ### Gradle:

  ~~~Gradle
  compile 'org.apache.rocketmq:rocketmq-client:4.3.0'
  ~~~

  #### 2.1 同步发送消息

  可靠的同步传输广泛应用于重要通知消息、短信通知、短信营销系统等场景。

  ~~~java
  public class SyncProducer {
      public static void main(String[] args) throws Exception {
          //Instantiate with a producer group name.
          DefaultMQProducer producer = new
              DefaultMQProducer("please_rename_unique_group_name");
          // Specify name server addresses.
          producer.setNamesrvAddr("localhost:9876");
          //Launch the instance.
          producer.start();
          for (int i = 0; i < 100; i++) {
              //Create a message instance, specifying topic, tag and message body.
              Message msg = new Message("TopicTest" /* Topic */,
                  "TagA" /* Tag */,
                  ("Hello RocketMQ " +
                      i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */
              );
              //Call send message to deliver message to one of brokers.
              SendResult sendResult = producer.send(msg);
              System.out.printf("%s%n", sendResult);
          }
          //Shut down once the producer instance is not longer in use.
          producer.shutdown();
      }
  }
  
  ~~~

  

#### 2.2 异步发送消息

异步传输通常用于响应时间敏感的业务场景。

~~~java
public class AsyncProducer {
    public static void main(String[] args) throws Exception {
        //Instantiate with a producer group name.
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
        // Specify name server addresses.
        producer.setNamesrvAddr("localhost:9876");
        //Launch the instance.
        producer.start();
        producer.setRetryTimesWhenSendAsyncFailed(0);
        for (int i = 0; i < 100; i++) {
                final int index = i;
                //Create a message instance, specifying topic, tag and message body.
                Message msg = new Message("TopicTest",
                    "TagA",
                    "OrderID188",
                    "Hello world".getBytes(RemotingHelper.DEFAULT_CHARSET));
                producer.send(msg, new SendCallback() {
                    @Override
                    public void onSuccess(SendResult sendResult) {
                        System.out.printf("%-10d OK %s %n", index,
                            sendResult.getMsgId());
                    }
                    @Override
                    public void onException(Throwable e) {
                        System.out.printf("%-10d Exception %s %n", index, e);
                        e.printStackTrace();
                    }
                });
        }
        //Shut down once the producer instance is not longer in use.
        producer.shutdown();
    }
}
~~~

#### 2.3 单向传输模式发送消息

单向传输用于需要中等可靠性的情况，例如日志收集。

~~~java
public class OnewayProducer {
    public static void main(String[] args) throws Exception{
        //Instantiate with a producer group name.
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
        // Specify name server addresses.
        producer.setNamesrvAddr("localhost:9876");
        //Launch the instance.
        producer.start();
        for (int i = 0; i < 100; i++) {
            //Create a message instance, specifying topic, tag and message body.
            Message msg = new Message("TopicTest" /* Topic */,
                "TagA" /* Tag */,
                ("Hello RocketMQ " +
                    i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */
            );
            //Call send message to deliver message to one of brokers.
            producer.sendOneway(msg);

        }
        //Shut down once the producer instance is not longer in use.
        producer.shutdown();
    }
}

~~~

#### 3.  消息消费

~~~java
public class Consumer {

    public static void main(String[] args) throws InterruptedException, MQClientException {

        // Instantiate with specified consumer group name.
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name");
         
        // Specify name server addresses.
        consumer.setNamesrvAddr("localhost:9876");
        
        // Subscribe one more more topics to consume.
        consumer.subscribe("TopicTest", "*");
        // Register callback to execute on arrival of messages fetched from brokers.
        consumer.registerMessageListener(new MessageListenerConcurrently() {

            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                ConsumeConcurrentlyContext context) {
                System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), msgs);
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        //Launch the consumer instance.
        consumer.start();

        System.out.printf("Consumer Started.%n");
    }
}
~~~

#### 4. 查看更多

作为选择的，你能从下面这个链接中获取到更多列子：

<https://github.com/apache/rocketmq/tree/master/example>