# RocketMQ



 RocketMQ + Docker + SpringBoot

> 介绍文档：[https://cloud.tencent.com/developer/article/1739416](https://cloud.tencent.com/developer/article/1739416)
>
> 官方文档：[https://github.com/apache/rocketmq/tree/master/docs/cn](https://github.com/apache/rocketmq/tree/master/docs/cn)
>
> 详细介绍：[https://zhuanlan.zhihu.com/p/94662788](https://zhuanlan.zhihu.com/p/94662788)
>
> 术语概念：[https://blog.csdn.net/tototuzuoquan/article/details/78325192](https://blog.csdn.net/tototuzuoquan/article/details/78325192)

### 架构

NameServer：注册中心，管理Topic信息和路由信息

Broker：存储消息、过滤和转发（以Topic为维度）

Producer：生产者，负责投递消息

Consumer：消费者，负责接收消息

### 安装

...

### SpringBoot 整合 RocketMQ

#### 创建消息主题和订阅组

SpringBoot 快速上手

> 快速上手文档：[https://www.baeldung.com/apache-rocketmq-spring-boot](https://www.baeldung.com/apache-rocketmq-spring-boot)

```text
    /**
     * 测试RocketMQ
     * @return
     */
    @GetMapping(value = "/testMQ", produces = { "application/json;charset=utf-8" })
    public String testMQ() {
        LocalDateTime currentTime = LocalDateTime.now();
        rocketMQTemplate.convertAndSend("ROCKET_TOPIC_1", currentTime.toString());
        return currentTime.toString();
    }
​
    @Service
    @RocketMQMessageListener(consumerGroup = "CONSUMER_GROUP_1", topic = "ROCKET_TOPIC_1", nameServer = "192.168.145.34:9876")
    public static class Consumer implements RocketMQListener<String> {
​
        @Override
        public void onMessage(String s) {
            log.info("consumer rocket 收到消息：{}", s);
        }
    }
```

```text
package com.carnation.rocketmq;
​
import com.alibaba.fastjson.JSON;
import com.carnation.domain.dto.SysUserDto;
import lombok.extern.slf4j.Slf4j;
import org.apache.rocketmq.client.producer.SendCallback;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.client.producer.TransactionSendResult;
import org.apache.rocketmq.spring.core.RocketMQTemplate;
import org.apache.rocketmq.spring.support.RocketMQHeaders;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.messaging.Message;
import org.springframework.messaging.support.MessageBuilder;
import org.springframework.stereotype.Service;
​
import java.util.List;
import java.util.UUID;
​
/**
 * @author zy
 * @date 2020/6/10
 * @since 1.0.0
 */
@Slf4j
@Service
public class RocketMQTemplateProducer {
    @Autowired
    private RocketMQTemplate template;
​
    /**
     * 发送普通消息
     *
     * @param topic   topic
     * @param message 消息体
     */
    public void sendMessage(String topic, Object message) {
        this.template.convertAndSend(topic, message);
        log.info("普通消息发送完成：message = {}", message);
    }
​
    /**
     * 发送同步消息
     *
     * @param topic   topic
     * @param message 消息体
     */
    public void syncSendMessage(String topic, Object message) {
        SendResult sendResult = this.template.syncSend(topic, message);
        log.info("同步发送消息完成：message = {}, sendResult = {}", message, sendResult);
    }
​
    /**
     * 发送异步消息
     *
     * @param topic   topic
     * @param message 消息体
     */
    public void asyncSendMessage(String topic, Object message) {
        this.template.asyncSend(topic, message, new SendCallback() {
            @Override
            public void onSuccess(SendResult sendResult) {
                log.info("异步消息发送成功，message = {}, SendStatus = {}", message, sendResult.getSendStatus());
            }
​
            @Override
            public void onException(Throwable e) {
                log.info("异步消息发送异常，exception = {}", e.getMessage());
            }
        });
    }
​
    /**
     * 发送单向消息
     *
     * @param topic   topic
     * @param message 消息体
     */
    public void sendOneWayMessage(String topic, Object message) {
        this.template.sendOneWay(topic, message);
        log.info("单向发送消息完成：message = {}", message);
    }
​
    /**
     * 同步发送批量消息
     *
     * @param topic       topic
     * @param messageList 消息集合
     * @param timeout     超时时间（毫秒）
     */
    public void syncSendMessages(String topic, List<Message<?>> messageList, long timeout) {
        this.template.syncSend(topic, messageList, timeout);
        log.info("同步发送批量消息完成：message = {}", JSON.toJSONString(messageList));
    }
​
    /**
     * 发送事务消息
     *
     * @param topic   topic
     * @param message 消息对象
     */
    public void sendMessageInTransaction(String topic, SysUserDto message) {
        String transactionId = UUID.randomUUID().toString();
        TransactionSendResult result = this.template.sendMessageInTransaction(topic, MessageBuilder.withPayload(message)
                .setHeader(RocketMQHeaders.TRANSACTION_ID, transactionId)
                .build(), message);
        log.info("发送事务消息（半消息）完成：result = {}", result);
    }
​
    /**
     * 发送携带 tag 的消息（过滤消息）
     *
     * @param topic   topic，RocketMQTemplate将 topic 和 tag 合二为一了，底层会进行
     *                拆分再组装。只要在指定 topic 时跟上 {:tags} 就可以指定tag
     *                例如 test-topic:tagA
     * @param message 消息体
     */
    public void syncSendMessageWithTag(String topic, Object message) {
        this.template.syncSend(topic, message);
        log.info("发送带 tag 的消息完成：message = {}", message);
    }
​
    /**
     * 同步发送延时消息
     *
     * @param topic      topic
     * @param message    消息体
     * @param timeout    超时
     * @param delayLevel 延时等级：现在RocketMq并不支持任意时间的延时，需要设置几个固定的延时等级，
     *                   从1s到2h分别对应着等级 1 到 18，消息消费失败会进入延时消息队列
     *                   "1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h";
     */
    public void syncSendDelay(String topic, Object message, long timeout, int delayLevel) {
        this.template.syncSend(topic, MessageBuilder.withPayload(message).build(), timeout, delayLevel);
        log.info("已同步发送延时消息 message = {}", message);
    }
​
    /**
     * 异步发送延时消息
     *
     * @param topic      topic
     * @param message    消息对象
     * @param timeout    超时时间
     * @param delayLevel 延时等级
     */
    public void asyncSendDelay(String topic, Object message, long timeout, int delayLevel) {
        this.template.asyncSend(topic, MessageBuilder.withPayload(message).build(), new SendCallback() {
            @Override
            public void onSuccess(SendResult sendResult) {
                log.info("异步发送延时消息成功，message = {}", message);
            }
​
            @Override
            public void onException(Throwable throwable) {
                log.error("异步发送延时消息发生异常，exception = {}", throwable.getMessage());
            }
        }, timeout, delayLevel);
        log.info("已异步发送延时消息 message = {}", message);
    }
​
    /**
     * 发送单向顺序消息
     *
     * @param topic topic
     */
    public void sendOneWayOrderly(String topic) {
        for (int i = 0; i < 30; i++) {
            this.template.sendOneWayOrderly(topic, MessageBuilder.withPayload("message - " + i).build(), "topic");
            log.info("单向顺序发送消息完成：message = {}", "message - " + i);
        }
    }
​
    /**
     * 同步发送顺序消息
     *
     * @param topic topic
     */
    public void syncSendOrderly(String topic) {
        for (int i = 0; i < 30; i++) {
            SendResult sendResult = this.template.syncSendOrderly(topic, MessageBuilder.withPayload("message - " + i).build(), "syncOrderlyKey");
            log.info("同步顺序发送消息完成：message = {}, sendResult = {}", "message - " + i, sendResult);
        }
    }
}
​
```

### 手动创建Topic

> [https://blog.csdn.net/weixin\_44204615/article/details/103787745](https://blog.csdn.net/weixin_44204615/article/details/103787745)

### 同步、异步、单向消息区别

| 发送方式 | 发送 TPS | 发送结果反馈 | 可靠性 |
| :--- | :--- | :--- | :--- |
| 同步发送 | 快 | 有 | 不丢失 |
| 异步发送 | 快 | 有 | 不丢失 |
| 单向发送 | 最快 | 无 | 可能丢失 |

### 问题

#### rocketmq 自动创建topic的过程分析以及为什么自动创建topic可能会导致负载不均衡的情况

> [https://blog.csdn.net/yifouhu2947/article/details/87714047](https://blog.csdn.net/yifouhu2947/article/details/87714047)

#### 使用rocketmq-spring-boot-starter发送消息如何指定tag与key?

> [https://blog.csdn.net/lym0721/article/details/105702801](https://blog.csdn.net/lym0721/article/details/105702801)

