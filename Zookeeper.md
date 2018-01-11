### Paxos

#### 个人理解

---
Proposers 
Acceptors 
Learners
 
#####Acceptors遵循的规则： 
Acceptor 接收到的首次提案必须同意。
prepare请求阶段：
如果后续来的编号new_id大于目前已经通过的old_value值的编号old_id，则Acceptor更新自己同意的编号为new_id
，同意的提案值为new_value。Acceptor只需要记录当前自己通过的最大编号的max_id提交提案value。
如果后续来的编号new_id小于目前已经通过的old_value值的编号old_id，则舍弃新的prepare请求。
accept请求阶段：
Acceptor收到一个编号为new_id的accept请求，只要它还未对编号大于new_id的prepare请求作出响应，它就可以通过这个提案。


#####Proposers遵循的规则：
prepare请求阶段：
每个Proposer都会有个唯一编号，先prepare 阶段，获取目前已知的最大编号max_id的提议value值
，如果没有则自己生成任意value，如果存在max_id，则Proposer的提案也是value
accept请求阶段：
如果Proposer 接收到超过半数以上的Acceptor对于它的prepare请求的响应（也就是同意了该提案，及时后续有同意编号更大的prepare请求的值）

```
如何浅显易懂地解说 Paxos 的算法？
https://www.zhihu.com/question/19787937/answer/107750652
```

```
【译】Paxos Made Simple
http://dsdoc.net/paxosmadesimple/index.html
```
