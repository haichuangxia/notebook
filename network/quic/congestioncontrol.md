**QUIC**使用**BBR**算法来进行流量控制。

 BBR：通过测量**连接的数据传输率**和**RTT**

优点：
1. 瓶颈层需要很窄的缓冲区就能获得比较大的吞吐量
2. 使用更深的缓冲区来减少排队时延
3. 易用性：可以被其他传输层协议使用
# 传统的算法的问题
## 缓冲区膨胀
缓冲区膨胀:通信设备制造商在网络产品中不必要地设计了过大的缓冲区.这种设备中，当网络链路拥塞时，就会发生缓冲膨胀，从而导致数据包在这些超大缓冲区中长时间排队。在先进先出队列系统中，过大的缓冲区会导致更长的队列和更高的延迟，并且不会提高网络吞吐量。

缓冲区较小时,当链路达到拥塞时,缓冲区很快装满,开始丢弃一些数据包,发送端很快发现收不到确认,因此开始进行拥塞控制.

缓冲区很大时候,当链路拥塞时,网络设备的缓冲区需要更长的时间才可以充满,待到缓冲区充满之后丢包,再到发送端检测到发生拥塞并开始控制时,已经过了比较长的时间,延迟较高.

## BBR的改进
因为存在***缓冲膨胀***等问题,基于***丢包***的传统拥塞控制算法难以解决,因此BBR使用***延迟***来作为是否发生拥塞,并进一步决定发送速率的主要因素.

正因为BBR算法是基于延迟的,其可以获得更低的延迟和更高吞吐量,尤其是唉出现轻微丢包的情况下.(在理想的情况下,两者并无多大区别,甚至cubic等算法还可能优于BBR算法,但是只要出现了一定的丢包,如1.5%的丢包率,cubic算法的吞吐量就会急剧下降,而BBR下降幅度则较小)

BBR适合用于"长肥网络"(时延带宽积,丢包率较高的网络)

# 算法思路
## 网络路径模型
## 目标操作点
## 控制参数

| 参数         | 意义                                   |
| ------------ | -------------------------------------- |
| pacing rate  | 一个窗口的数据以多大的时间间隔发送     |
| send quantum |                                        |
| cwnd         | 滑动窗口的大小，可以发送的数据的最大值 |

## State Machine
**State Machine**是**有限状态自动机**的简称。BBR使用有限状态机来调整控制参数的值。

设计目标
1. 高带宽
2. 低延迟
3. 大概公平的带宽共享

### 有限状态机的状态转移

有限状态机有以下几种状态：

| state    | 中文       | 触发         | 意义                                           |
| -------- | ---------- | ------------ | ---------------------------------------------- |
| Startup  | 起步       | 起步阶段     | 初始状态，随后快速提高数据发送速率（倾斜上升） |
| Drain    | 排干，放干 | 信道满了     | 排空队列                                       |
| ProbeBW  | 探测带宽   | Steady state | 通过短期内发送数据测试瓶颈层的带宽(BBR.BtlBW)  |
| ProbeRTT | 探测RTT    | Steady state | 通过短期内发送数据测试RTT(BBR.RTprop)          |


不同状态之间的转移图为：

![StateTransitionDiagram](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221018154436.png)

## 算法组织
算法的执行步骤由一些参数决定：
1. 连接初始化
2. ACK
3. 数据传输

### 初始化
 依靠参数1：连接初始化时（transport connection initialization）时触发

```
BBROnConnectionInit():
    BBRInit()
```

 ### Per-ACK Steps

 ```
BBRUpdateOnACK():
    BBRUpdateModelAndState()
    BBRUpdateControlParameters()

BBRUpdateModelAndState():
    BBRUpdateBtlBw()
    BBRCheckCyclePhase()
    BBRCheckFullPipe()
    BBRCheckDrain()
    BBRUpdateRTprop()
    BBRCheckProbeRTT()

BBRUpdateControlParameters()
    BBRSetPacingRate()
    BBRSetSendQuantum()
    BBRSetCwnd()
 ```

# 算法详细
## 维持网络路径模型
### BBR.BtlBw
#### BBR.BtlBw Max Filter
#### BBR.BtlBw Max Filter的追踪时间

**BtlBw filter**窗口的tracks time使用的是虚拟的时间，该虚拟时间被**BBR.round_count**（
定时数据包往返的次数）追踪。这种做法的好处是：相对于实际的时间，其拥有更强的健壮性


**BBR.round_count**记录定时数据包往返轮次的方法：使用一个**哨兵数据包**（简称哨兵），记录其状态，在**哨兵**发送之后，等待后续到达的任意数据包的**ACK**，不同条件下，该过程为：

1. 连接初始化时
``` C++
BBRInitRoundCounting():
    BBR.next_round_delivered = 0
    BBR.round_start = false
    BBR.round_count = 0
```

2. 传输每个数据时
``` C++
packet.delivered = BBR.delivered
```

3. 接收到ACK时

``` C++
BBRUpdateRound():
    BBR.delivered += packet.size
    if (packet.delivered >= BBR.next_round_delivered)
        BBR.next_round_delivered = BBR.delivered
        BBR.round_count++
        BBR.round_start = true
    else
        BBR.round_start = false
```
#### Application-limited Delivery Rate Samples和BBR.BtlBw之间的关系

可以影响数据传输速率的两个因素：
1. 应用限制（application limited)
2. 拥塞控制算法

因此，如果需要测算**BBR.BtlBw**的大小，就需要排除应用限制对数据传输速率的影响。要实现这一点，就需要**准确的采样**，确定将哪些样本加入网络路径模型中，以便于使得测算的**BtlBw**放映的是实际网络环境下的带宽，而不是应用限制的带宽。

默认是丢弃被应用限制带宽的样本，但是如果使用应用限制了带宽的样本测算的**BtlBw**比当前值还高，那么**application limited samples**就可以使用。
#### 更新BBR.BtlBw Max Filter

每当收到ACK，BBR就会更新**BtlBw**的值，该过程的算法为：

``` C++
# rs.delivery_rate通过已经被确认的数据包的来确定
BBRUpdateBtlBw()
    BBRUpdateRound()
    if (rs.delivery_rate >= BBR.BtlBw || ! rs.is_app_limited)
        BBR.BtlBw = update_windowed_max_filter(
                                                filter=BBR.BtlBwFilter,
                                                value=rs.delivery_rate,
                                                time=BBR.round_count,
                                                window_length=BtlBwFilterLen)
```

### BBR.Rtprop

**BBR.RTprop**简单来说就是对**往返传播时延**的估计值，其使用**Round-Trip Time Samples**来计算**BBR。RTprop**的值。

#### Round-Trip Time Samples for Estimating BBR.RTprop
BBR RTT Samples也使用常规的RTT计算机制，同样使用了**累积确认**和**选择确认**机制。

RTT估计值同超时重传时间不同的情况：ACK不只是确认单独的一个数据包，一个ACK可以确认多个数据包已正确达到

#### BBR.RTprop Min Filter
#### RTprop Min Filter的更新
``` C++
packet.send_time = Now()
```

``` C++
packet.rtt = Now() - packet.send_time
```

``` C++
BBRUpdateRTprop()
    BBR.rtprop_expired =Now() > BBR.rtprop_stamp + RTpropFilterLen
    if (packet.rtt >= 0 and (packet.rtt <= BBR.RTprop or BBR.rtprop_expired))
        BBR.RTprop = packet.rtt
        BBR.rtprop_stamp = Now()
```

## BBR控制参数
### pacing rate
初始化时，pacing rate的赋值为：
``` C++
BBRInitPacingRate():
    nominal_bandwidth = InitialCwnd / (SRTT ? SRTT : 1ms)
    BBR.pacing_rate = BBR.pacing_gain * nominal_bandwidth
```

pacing rate的更新：
1. ACK时更新

更新条件(满足任一条件即可)
1. BBR估计其已经充满了整个信道（pipe），即`BBR.filled_pipe==true`

更新算法：
``` C++
BBRSetPacingRateWithGain(pacing_gain):
    rate = pacing_gain * BBR.BtlBw
    if (BBR.filled_pipe || rate > BBR.pacing_rate)
        BBR.pacing_rate = rate

BBRSetPacingRate():
    BBRSetPacingRateWithGain(BBR.pacing_gain)
```

### Send Quantum
为了分摊在发送过程中每个数据包的主机的开销,高性能传输发送器实现方案经常将多个数据包的聚合值作为一个量子.(`In order to amortize per-packet host overheads involved in the sending process, high-performance transport sender implementations often schedule an aggregate containing multiple packets (multiple MSS) worth of data as a single quantum(using TSO, GSO, or other offload mechanisms) `)

TSO,GSO设计思想:将大块数据（远超MTU）传给网络设备，由网络设备按照MTU来分段，从而释放CPU资源

BBR算法,使用**BBR.send_quantum**控制参数来指明传输的最大单位.其大小设置需要考虑的因素为:

若设置的的值**过小**则会导致**低数据传输速率**,其主要原因为:
  - 少排队
  - 少排队时延
  - 数据突发传送能力低
    -
  - 低丢包率

若设置的值**过大**,则**数据传输率**较高,原因为:其减少了CPU负载，因此一次可以发送更多的数据到网络上


**BBR.send_quantum**的更新算法为:每当没收ACK时,BBR执行`BBRSetSendQuantum()`算法来更新**BBR.send_quantum**的值
``` C++
BBRSetSendQuantum():
    if (BBR.pacing_rate < 1.2 Mbps)
        BBR.send_quantum = 1 * MSS
    else if (BBR.pacing_rate < 24 Mbps)
        BBR.send_quantum = 2 * MSS
    else
        BBR.send_quantum = min(BBR.pacing_rate * 1ms, 64KBytes)
```




### congestion Window(cwnd)
BBR算法调整拥塞窗口的依据有:
1. 其网络路径模型
2. 有限状态机

默认情况下,BBR会`cwnd=BBR.target_cwnd`,令其cwnd的值增长到其target cwnd的大小.(BBR.target_cwnd:与从模型中计算而得的BDP(时延带宽积)估计值相适应)

#### Initial cwnd

cwnd的初始值并不依靠BBR算法给出,而是依靠发送方自己设置初始的拥塞窗口.

#### target cwnd
target cwmd是:BBR允许发送的数据的上限,当其他条件被满足时起支配作用,条件包括:
1. 数据流不处于loss recovery状态
2. 不需要探测`BBR.RTprop`
3. 获得了足够的ACK来建立模型

每个ACK,计算`BBR.target_cwnd`算法为:
``` C++
BBRInflight(gain):
    if (BBR.RTprop == Inf)
        return InitialCwnd /* no valid RTT samples yet */
    quanta = 3*BBR.send_quantum
    estimated_bdp = BBR.BtlBw * BBR.RTprop
    return gain * estimated_bdp + quanta

BBRUpdateTargetCwnd():
    BBR.target_cwnd = BBRInflight(BBR.cwnd_gain)
```

#### Minimum cwnd for pipelining
使用最小的cnd,保证有足够的数据包在传送,以维持完整的流水线传输
#### Modulating cwnd in Loss Recovery
**loss**:可以看作时一个线索,它在网络路径模型没有及时反映网络路径变化时用来表现网络路径变化.因此,通常来说,**loss**需要保守一些

当发生丢包,且信道上还有数据包时,调整cwnd的策略:
1. 刚发生时,cwnd的值调整为ACKs到达的时的数据传输率
2. 后面,令cwnd的值不超过ACKs到达时,数据传输率的两倍

在处于**loss recovery**阶段时,BBR将cwnd的值设置为`last known good value`(这个值在进入loss recovery阶段之前),算法实现为:
1. RTO时(retransmission timeout)

``` C
BBR.prior_cwnd = BBRSaveCwnd()
cwnd = 1
```

2. 进入快恢复时(fast recovery)

将窗口值,设置为还在传输中的数据包的个数

``` C++
BBR.prior_cwnd = BBRSaveCwnd()
cwnd = packets_in_flight + max(packets_delivered, 1)
BBR.packet_conservation = true
```

3. 快恢复阶段收到ACK时
执行`BBRModulateCwndForRecovery()`算法
``` C++
BBRModulateCwndForRecovery():
    if (packets_lost > 0)
        cwnd = max(cwnd - packets_lost, 1)
    if (BBR.packet_conservation)
        cwnd = max(cwnd, packets_in_flight + packets_delivered)
```

4. 快恢复每一轮之后
`BBR.packet_conservation = false`

#### Modulating cwnd in ProbeRTT
ProbeRTT状态的目标是: 快速减少信道上还在传输的数据,排空瓶颈层.BBR为了使用ProbeRTT状态,将cwnd同BBRMinPipeCwnd相绑定.其算法为:
``` C++
BBRModulateCwndForProbeRTT():
    if (BBR.state == ProbeRTT)
        cwnd = min(cwnd, BBRMinPipeCwnd)
```
#### Core cwnd Adjustment Mechanism
网络路径模型以及在路径上传输的数据有可能会发生剧变.为了平稳应对变化,减少丢包,BBR使用了一些比较保守的策略:
``` C++
if(cwnd>target_cwnd)
    cwnd=target_cwnd
else if(cwnd < BBR.target_cwnd)
    cwnd+
```

cwnd更新的核心机制为:
``` C++
BBRSetCwnd():
BBRUpdateTargetCwnd()
    BBRModulateCwndForRecovery()
    if (not BBR.packet_conservation) {
        if (BBR.filled_pipe)
            cwnd = min(cwnd + packets_delivered, BBR.target_cwnd)
        else if (cwnd < BBR.target_cwnd || BBR.delivered < InitialCwnd)
            cwnd = cwnd + packets_delivered
        cwnd = max(cwnd, BBRMinPipeCwnd)
    }
    BBRModulateCwndForProbeRTT()
```

## 状态机
### Initialization Steps
当连接初始化时,执行`BBRInit()`方法:
```
BBRInit():
    init_windowed_max_filter(filter=BBR.BtlBwFilter, value=0, time=0)
    BBR.rtprop = SRTT ? SRTT : Inf
    BBR.rtprop_stamp = Now()
    BBR.probe_rtt_done_stamp = 0
    BBR.probe_rtt_round_done = false
    BBR.packet_conservation = false
    BBR.prior_cwnd = 0
    BBR.idle_restart = false
    BBRInitRoundCounting()
    BBRInitFullPipe()
    BBRInitPacingRate()
    BBREnterStartup()
```

### Startup
#### Startup Dynamics
使用二进制指数级增长,以便快速找到`BtlBW`值
``` C++
BBREnterStartup():
BBR.state = Startup
    BBR.pacing_gain = BBRHighGain
    BBR.cwnd_gain = BBRHighGain
```


#### Estimating When Startup has Filled the Pipe
当连接刚建立时,执行`BBRInitFullPipe()`方法
``` C++
BBRInitFullPipe():
    BBR.filled_pipe = false
    BBR.full_bw = 0
    BBR.full_bw_count = 0
```

当收到新的ACK时.需要信道是否已经充满.
``` C++
BBRCheckFullPipe():
    if BBR.filled_pipe or
        not BBR.round_start or rs.is_app_limited
        return // no need to check for a full pipe now
    if (BBR.BtlBw >= BBR.full_bw * 1.25) // BBR.BtlBw still growing?
        BBR.full_bw = BBR.BtlBw // record new baseline level
        BBR.full_bw_count = 0
        return
    BBR.full_bw_count++ // another round w/o much growth
    if (BBR.full_bw_count >= 3)
        BBR.filled_pipe = true
```

### Drain
在Startup阶段,当检测到信道充满之后(full pipe),BBR算法就会切换到Drain状态.

``` C++
BBREnterDrain():
    BBR.state = Drain
    BBR.pacing_gain = 1/BBRHighGain // pace slowly
    BBR.cwnd_gain = bbr_high_gain // maintain cwnd
```

``` C++
BBRCheckDrain():
    if (BBR.state == Startup and BBR.filled_pipe)
        BBREnterDrain()
    if (BBR.state == Drain and packets_in_flight <= BBRInflight(1.0))
        BBREnterProbeBW() // we estimate queue is drained
```

### probeBW
#### Gain Cyling Dynamics
**BBR**算法大量的时间都在**ProbeBW**状态,其使用**gain cycling**方法来探测带宽.其使用8个值作为相,分别是`5/4,
3/4, 1, 1, 1, 1, 1, 1`,如此循环,每个相持续一个BBR.RTprop的时间.

#### Gain Cycling Algorithm
1. Upon entering ProbeBW

``` C++
BBREnterProbeBW():
    BBR.state = ProbeBW
    BBR.pacing_gain = 1
    BBR.cwnd_gain = 2
    BBR.cycle_index = BBRGainCycleLen - 1 - random_int_in_range(0..6)
    BBRAdvanceCyclePhase()
```

2. On each ACK BBR runs BBRCheckCyclePhase(),
``` C++
BBRCheckCyclePhase():
    if (BBR.sate == ProbeBW and BBRIsNextCyclePhase()
        BBRAdvanceCyclePhase()

BBRAdvanceCyclePhase():
    BBR.cycle_stamp = Now()
    BBR.cycle_index = (BBR.cycle_index + 1) % BBRGainCycleLen
    pacing_gain_cycle = [5/4, 3/4, 1, 1, 1, 1, 1, 1]
    BBR.pacing_gain = pacing_gain_cycle[BBR.cycle_index]

BBRIsNextCyclePhase():
    is_full_length = (Now() - BBR.cycle_stamp) > BBR.RTprop
    if (BBR.pacing_gain == 1)
        return is_full_length
    if (BBR.pacing_gain > 1)
        return is_full_length and(packets_lost > 0 or prior_inflight >= BBRInflight(BBR.pacing_gain))
    else // (BBR.pacing_gain < 1)
        return is_full_length or prior_inflight <= BBRInflight(1)
```

#### Restarting From Idle

#### ProbeRTT



# 缩写词
| 缩写 | 全名                    | 含义                   |
| ---- | ----------------------- | ---------------------- |
| BDP  | Bandwidth delay product | 带宽时延积(时延带宽积) |

# 名词解释
| 词汇                 | 含义                                                                              |
| -------------------- | --------------------------------------------------------------------------------- |
| Tracking Time        | 追踪时间，追溯时间                                                                |
| BBR.round_count      |
| wall clock time      | 墙上时钟时间，指的是实际的物理时间                                                |
| sentinel packet      | 哨兵数据包                                                                        |
| congestion algorithm | 拥塞控制算法                                                                      |
| Transmission rate    | （数据）传输速度                                                                  |
| propagation delay    | 电磁波在信道中传播用时，从一端到另一端，传播时延=信道长度(m)/信道上信号的传输速率 |
| RTpropFilterLen      | RTprop filter窗口的大小（是一个时间单位）                                         |
# REFERENCE
1. [缓冲膨胀-wiki](https://zh.m.wikipedia.org/zh-hans/%E7%BC%93%E5%86%B2%E8%86%A8%E8%83%80)
2. [从流量控制算法谈网络优化 – 从 CUBIC 到 BBRv2 算法](https://aws.amazon.com/cn/blogs/china/talking-about-network-optimization-from-the-flow-control-algorithm/)