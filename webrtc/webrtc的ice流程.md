# webrtc的ice流程
> 来自Johnny回答hjj的问题

## 问题
1. 各位大佬，想问下webrtc peerconnection 如果有多个iceservice，如果有多个TURN是如何选择的，是那个先连接成功先用哪个吗？

## 答案
1. 当ice connection收到 stun response，说明connection可写，纳入连接池，并保持周期性的stun ping/response
2. 这个ice connection的过程，会对所有的connection打分，当然，打分包括rtt的比较
3. 连接池更新可能会触发ice switch
4. 这些还依赖一些参数配置，决定后面如何走向
5. ice connection 排序依据
   - State: CRWS
   - 提名
   - 接收时间 
   - Candidate
     - network_preference（network cast相关）
     - priority
     - generation
     - pruned
   - rtt
