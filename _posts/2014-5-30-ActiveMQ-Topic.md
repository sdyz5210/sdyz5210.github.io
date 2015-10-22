---
layout: post
title: ActiveMQ之Topic
category: program
tags: [activemq]
---

发送消息

```
package com.nova.jms.topic;

import javax.jms.DeliveryMode;
import javax.jms.MapMessage;
import javax.jms.Session;
import javax.jms.Topic;
import javax.jms.TopicConnection;
import javax.jms.TopicConnectionFactory;
import javax.jms.TopicPublisher;
import javax.jms.TopicSession;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

/**
 * 方式消息发送者
 * 
 * @version 1.0
 */
public class TopicSender {

	// 发送次数
	public static final int SEND_NUM = 5;
	// tcp 地址
	public static final String BROKER_URL = "tcp://localhost:61616";
	// 目标，在ActiveMQ管理员控制台创建 http://localhost:8161/admin/queues.jsp
	public static final String DESTINATION = "fee.topic";

	public static void sendMessage(TopicSession session,
			TopicPublisher publisher) throws Exception {
		for (int i = 0; i < SEND_NUM; i++) {
			String message = "发送消息第" + (i + 1) + "条";

			MapMessage map = session.createMapMessage();
			map.setString("text", message);
			map.setLong("time", System.currentTimeMillis());
			System.out.println(map);

			publisher.send(map);
		}
	}

	public static void run() throws Exception {

		TopicConnection connection = null;
		TopicSession session = null;
		try {
			// 创建链接工厂
			TopicConnectionFactory factory = new ActiveMQConnectionFactory(
					ActiveMQConnection.DEFAULT_USER,
					ActiveMQConnection.DEFAULT_PASSWORD, BROKER_URL);
			// 通过工厂创建一个连接
			connection = factory.createTopicConnection();
			// 启动连接
			connection.start();
			// 创建一个session会话
			session = connection.createTopicSession(Boolean.TRUE,
					Session.AUTO_ACKNOWLEDGE);
			// 创建一个消息队列
			Topic topic = session.createTopic(DESTINATION);
			// 创建消息发送者
			TopicPublisher publisher = session.createPublisher(topic);
			// 设置持久化模式
			publisher.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
			sendMessage(session, publisher);
			// 提交会话
			session.commit();

		} catch (Exception e) {
			throw e;
		} finally {
			// 关闭释放资源
			if (session != null) {
				session.close();
			}
			if (connection != null) {
				connection.close();
			}
		}
	}

	public static void main(String[] args) throws Exception {
		TopicSender.run();
	}
}
```

接收消息

```
package com.nova.jms.topic;

import javax.jms.JMSException;
import javax.jms.MapMessage;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.Session;
import javax.jms.Topic;
import javax.jms.TopicConnection;
import javax.jms.TopicConnectionFactory;
import javax.jms.TopicSession;
import javax.jms.TopicSubscriber;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

public class TopicReceiver {

	// tcp 地址
	public static final String BROKER_URL = "tcp://localhost:61616";
	// 目标，在ActiveMQ管理员控制台创建 http://localhost:8161/admin/queues.jsp
	public static final String TARGET = "fee.topic";

	public static void run() throws Exception {

		TopicConnection connection = null;
		TopicSession session = null;
		try {
			// 创建链接工厂
			TopicConnectionFactory factory = new ActiveMQConnectionFactory(
					ActiveMQConnection.DEFAULT_USER,
					ActiveMQConnection.DEFAULT_PASSWORD, BROKER_URL);
			// 通过工厂创建一个连接
			connection = factory.createTopicConnection();
			// 启动连接
			connection.start();
			// 创建一个session会话
			session = connection.createTopicSession(Boolean.TRUE,
					Session.AUTO_ACKNOWLEDGE);
			// 创建一个消息队列
			Topic topic = session.createTopic(TARGET);
			// 创建消息制作者
			TopicSubscriber subscriber = session.createSubscriber(topic);

			subscriber.setMessageListener(new MessageListener() {
				public void onMessage(Message msg) {
					if (msg != null) {
						MapMessage map = (MapMessage) msg;
						try {
							System.out.println(map.getLong("time") + "接收#"
									+ map.getString("text"));
						} catch (JMSException e) {
							e.printStackTrace();
						}
					}
				}
			});
			// 休眠100ms再关闭
			Thread.sleep(1000 * 100);

			// 提交会话
			session.commit();

		} catch (Exception e) {
			throw e;
		} finally {
			// 关闭释放资源
			if (session != null) {
				session.close();
			}
			if (connection != null) {
				connection.close();
			}
		}
	}

	public static void main(String[] args) throws Exception {
		TopicReceiver.run();
	}
}
```