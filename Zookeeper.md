### Paxos

#### 个人理解

---
Proposers 
Acceptors 
Learners
 
##### Acceptors遵循的规则： 
~~~
Acceptor 接收到的首次提案必须同意。
prepare请求阶段：
如果后续来的编号new_id大于目前已经通过的old_value值的编号old_id，则Acceptor更新自己同意的编号为new_id
，同意的提案值为new_value。Acceptor只需要记录当前自己通过的最大编号的max_id提交提案value。
如果后续来的编号new_id小于目前已经通过的old_value值的编号old_id，则舍弃新的prepare请求。
accept请求阶段：
Acceptor收到一个编号为new_id的accept请求，只要它还未对编号大于new_id的prepare请求作出响应，它就可以通过这个提案。
~~~

##### Proposers遵循的规则：
~~~
prepare请求阶段：
每个Proposer都会有个唯一编号，先prepare 阶段，获取目前已知的最大编号max_id的提议value值
，如果没有则自己生成任意value，如果存在max_id，则Proposer的提案也是value
accept请求阶段：
如果Proposer 接收到超过半数以上的Acceptor对于它的prepare请求的响应（也就是同意了该提案，及时后续有同意编号更大的prepare请求的值）
~~~

一次性触发：
~~~
一个watch事件将会在数据发生变更时发送给客户端。
后续变更不会再发送，如果想监听后续变更，需要再次添加一个监听。
注册监听有时间延迟，期间可能会有事件发生，所以要考虑这种情况。
~~~

ZooKeeper version number 作用
~~~
Each time a znode's data changes, the version number increases.
For instance, whenever a client retrieves data, it also receives the version of the data. 
And when a client performs an update or a delete, it must supply the version of the data of the znode it is changing. 
If the version it supplies doesn't match the actual version of the data, the update will fail.
~~~
Ephemeral Nodes
~~~
Because of this behavior ephemeral znodes are not allowed to have children.
~~~
Sequence Nodes -- Unique Naming
~~~
the counter used to store the next sequence number is a signed int (4bytes) maintained by the parent node
~~~
ZooKeeper Sessions
~~~
session id and session password
session timeout scope 2 tickTime 20tickTime
default wathcer session connection event
~~~
ZooKeeper Watches
~~~
all of read operations : getData(), getChildren(), and exists()
1. One-time trigger

2. Sent to the client:
    ZooKeeper provides an ordering guarantee: a client will never see a change for which it has set a watch until it first sees the watch event.
3. The data for which the watch was set.
    getData() and exists() set data watches. getChildren() sets child watches.
    setData() will trigger data watches 
    create() will trigger a data watch for the znode being created and  a child watch for the parent znode
    delete() will trigger both a data watch and a child watch
    ** There is one case where a watch may be missed: a watch for the existance of a znode not yet created will be missed if the znode is created and deleted while disconnected.**
~~~


1. [如何浅显易懂地解说 Paxos 的算法？](https://www.zhihu.com/question/19787937/answer/107750652)
2. [【译】Paxos Made Simple](http://dsdoc.net/paxosmadesimple/index.html)
3. [Apache ZooKeeper Watcher 机制源码解释](https://www.ibm.com/developerworks/cn/opensource/os-cn-apache-zookeeper-watcher/index.html)
4. [zookeeper 丢失事件/miss event](http://leibnitz.iteye.com/blog/1880577) 
5. [ZooKeeper Programmer's Guide](https://zookeeper.apache.org/doc/r3.1.2/zookeeperProgrammers.html#_introduction) 


