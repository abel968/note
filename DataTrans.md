## 数传P900
### mesh
- 每个数传可以分为Primary Coordinator
  1. Primary Coordinator 保证同步, 只有一个. 所有单元在mesh网络中, 必须能接收到某种coordinator的同步. 
  2. Standby Coordinator, 备用, 当Primary offline时启用. (Primary不应该和终端设备end device接通, 仅用于网络同步. 否则当Primary offline时, Standby和Primary有不同的唯一单位地址, 发送给Primary的任何数据都不会发给Standby)
  3. Sencondary Coordinator 扩大Primary的范围. 理论上所有单元都可以被设置为Sencondary, 但所有Sencondary都会广播sync包, 所以要注意数量. 
  4. Remote 就是slave 一般链接终端, 可以提供路由服务, 但会增加带宽而影响效率    
  **Standy 和 Primary 都应该只有1个. 并都不连接终端设备. 每个mesh network应该有一个唯一的network ID, mesh network中所有设备有相同的network id**
- mesh配置
  P39
- 
