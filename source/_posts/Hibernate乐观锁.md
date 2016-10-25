#理解Hibernate乐观锁

##介绍

Hibernate通过在持久化对象中增加Version属性(或者对某个属性增加@Version，采用Version属性会被Hibernate自动当做版本管理属性)来提供一套乐观锁方案，Version属性完全由Hibernate管理，开发者无需关心，只需要在你的代码中持久化类里增加一个数值型(Long，int，short)或者日期型(timestamp,calendars)的Version字段即可
。

##Version与事务
这里采用数值型的Version来说明Hibernate如果使用Version属性来实现乐观锁与事务。Version属性都是由Hibernate自动管理，当Hibernate需要创建一个事务来更新持久化对象时，会取出本次事务中最新一次查询出的持久化对象中的Version属性，来比对当前数据库中的Version属性，如果一致，则执行事务提交，一旦发现不一致的情况，将会回滚事务，这就是Hibernate通过Version属性实现的乐观锁。如果更新成功后，Hibernate会自动增加Version属性值。

##Hibernate自我管理Version VS DB自我管理
当然有些DBA喜欢使用数据库而不是Hibernate完成乐观锁策略，
Hibernate允许DB接收Version的管理，只需要在Version属性上配置generated=”always”（当然要注意，并不是所有数据库都支持管理Version）

```html
<hibernate-mapping package=”com.ffw.entity”> 
  <class name=”User”> 
    <id name=”id”> 
       <generator class=”native” /> 
    </id> 
    <version name=”version” type=”long” generated=”always”/> 
    <property name=”name” not-null=”true” /> 
  </class> 
</hibernate-mapping>
```
这么做后，当更新或者插入一条新纪录后，Hibernate需要额外的SQL来获取Version属性值，当然使用这种策略也可能存在一定风险，会出现更新或者插入记录后，额外的SQL无法查询出最新的Version变更信息结果。

##数值型Version VS 日期型Version
数值型的Version
（1）好处：不需要保证多台服务器的时间同步问题。
（2）缺点：无法记录版本变更的时间信息，这种信息有时在一些特殊场景非常有用。

日期型Version
（1）好处：可以记录版本变更的时间，这种信息在一些场景有非常重要的价值。
（2）缺点：如果多台服务器上时间不同步，将会导致乐观锁失败；又或者是时间精度不足，而此时并发量过高，同一毫秒数中有多个事务，导致事务失败。
##不使用Version启动Hibernate乐观锁机制
当然你也可以不使用Version属性来运作乐观锁策略，有另外两种方案同样可以实现乐观锁。
第一种：使用该表中的所有属性，通过设置optimistic-lock=”all” 来启动所有属性比对，确认当前记录版本。
第二种：使用上一次更新的属性来比对记录的版本，设置optimistic-lock=”dirty”。

##参考资料
[Versioning & Optimistic Locking in Hibernate] https://www.intertech.com/Blog/versioning-optimistic-locking-in-hibernate/

[Chapter 5. Locking] http://docs.jboss.org/hibernate/orm/4.0/devguide/en-US/html/ch05.html

[Chapter 11. Transactions and Concurrency] http://docs.jboss.org/hibernate/core/3.3/reference/en/html/transactions.html
