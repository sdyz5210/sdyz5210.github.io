---
layout: post
title: ActiveMQ之Queue
category: program
tags: [activemq]
---

消息发送

```
package com.nova.jms.queue;

import javax.jms.DeliveryMode;
import javax.jms.MapMessage;
import javax.jms.Queue;
import javax.jms.QueueConnection;
import javax.jms.QueueConnectionFactory;
import javax.jms.QueueSession;
import javax.jms.Session;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

public class QueueSender {

	// 发送次数
	public static final int SEND_NUM = 5;
	// tcp 地址
	public static final String BROKER_URL = "tcp://localhost:61616";
	// 目标
	public static final String DESTINATION = "fee.queue";

	/**
	 * <b>function:</b> 发送消息
	 * 
	 * @param session
	 * @param sender
	 * @throws Exception
	 */
	public static void sendMessage(QueueSession session,
			javax.jms.QueueSender sender) throws Exception {
		for (int i = 0; i < SEND_NUM; i++) {
			String message = "发送消息第" + (i + 1) + "条";

			MapMessage map = session.createMapMessage();
			map.setString("text", message);
			map.setLong("time", System.currentTimeMillis());
			System.out.println(map);

			sender.send(map);
		}
	}

	public static void run() throws Exception {

		QueueConnection connection = null;
		QueueSession session = null;
		try {
			// 创建链接工厂
			QueueConnectionFactory factory = new ActiveMQConnectionFactory(
					ActiveMQConnection.DEFAULT_USER,
					ActiveMQConnection.DEFAULT_PASSWORD, BROKER_URL);
			// 通过工厂创建一个连接
			connection = factory.createQueueConnection();
			// 启动连接
			connection.start();
			// 创建一个session会话
			session = connection.createQueueSession(Boolean.TRUE,
					Session.AUTO_ACKNOWLEDGE);
			// 创建一个消息队列
			Queue queue = session.createQueue(DESTINATION);
			// 创建消息发送者
			javax.jms.QueueSender sender = session.createSender(queue);
			// 设置持久化模式
			sender.setDeliveryMode(DeliveryMode.NON_PERSISTENTpackage com.nova.jms.queue;

import javax.jms.JMSException;

public class QueueReceiver {

	// tcp 地址
	public static final String BROKER_URL = "tcp://localhost:61616";
	// 目标
	public static final String TARGET = "fee.queue";

	public static void run() throws Exception {

		QueueConnection connection = null;
		QueueSession session = null;
		try {
			// 创建链接工厂
			QueueConnectionFactory factory = new ActiveMQConnectionFactory(
					ActiveMQConnection.DEFAULT_USER,
					ActiveMQConnection.DEFAULT_PASSWORD, BROKER_URL);
			// 通过工厂创建一个连接
			connection = factory.createQueueConnection();
			// 启动连接
			connection.start();
			// 创建一个session会话
			session = connection.createQueueSession(Boolean.TRUE,
					Session.AUTO_ACKNOWLEDGE);
			// 创建一个消息队列
			Queue queue = session.createQueue(TARGET);
			// 创建消息制作者
			javax.jms.QueueReceiver receiver = session.createReceiver(queue);

			receiver.setMessageListener(new MessageListener() {
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
		QueueReceiver.run();
	}
});
			sendMessage(session, sender);
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
		QueueSender.run();
	}
}
```

消息接受

```
package com.nova.jms.queue;

import javax.jms.JMSException;
import javax.jms.MapMessage;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.Queue;
import javax.jms.QueueConnection;
import javax.jms.QueueConnectionFactory;
import javax.jms.QueueSession;
import javax.jms.Session;

import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

public class QueueReceiver {

	// tcp 地址
	public static final String BROKER_URL = "tcp://localhost:61616";
	// 目标
	public static final String TARGET = "fee.queue";

	public static void run() throws Exception {

		QueueConnection connection = null;
		QueueSession session = null;
		try {
			// 创建链接工厂
			QueueConnectionFactory factory = new ActiveMQConnectionFactory(
					ActiveMQConnection.DEFAULT_USER,
					ActiveMQConnection.DEFAULT_PASSWORD, BROKER_URL);
			// 通过工厂创建一个连接
			connection = factory.createQueueConnection();
			// 启动连接
			connection.start();
			// 创建一个session会话
			session = connection.createQueueSession(Boolean.TRUE,
					Session.AUTO_ACKNOWLEDGE);
			// 创建一个消息队列
			Queue queue = session.createQueue(TARGET);
			// 创建消息制作者
			javax.jms.QueueReceiver receiver = session.createReceiver(queue);

			receiver.setMessageListener(new MessageListener() {
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
		QueueReceiver.run();
	}
}
```
