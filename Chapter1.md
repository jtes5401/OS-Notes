# OS介紹 & System type介紹

- Computer System組成
- OS架構
- OS之roles(角色)
- System types
	- Multiprogramming
	- Time-Sharing
	- Multiprocessors
	- Real-Time
	- Batch
	- Hand held


## Computer System Structures

主要由四個部分組成

1. **Hardware**
2. **Operating System**
3. **Application Programming (+ System Programs)**
	>System Programs: Compiler, Assembler, Linking loader, Debugger  
	
4. **Users**
	>其他機器、系統



#### 補充

1. **Bare Machine**: (裸機)純粹只有硬體組成，其上無任何OS及System programs
2. **Extended Machine**	: Bare Machine上加入OS、SystemPrograms、ApplicationPrograms


## OS架構

## OS之roles(扮演角色)(或目的)

1. 提供一個讓User[易於操作]()電腦之溝通介面
2. 提供一個讓[User Programs易於執行]()之環境
3. 作為一個resource(資源)的管理者，協調分配這些resource(eg CPU,Memory,I/O..)期望資源可以[有效]()利用
4. 作為一個監督者，監控所有Process執行，避免Process之有意或無意之破壞造成System重大危害

## Multiprogramming System
1. Def:系統 _允許多個Jobs(Process)同時執行_ 即是
	- 主要目的：提高CPU utilization
	- 作法：透過Job scheduling(or CPU scheduling)技術達成
	- eg：當執行中的Process在waiting for I/O-Completed，則OS可將CPU切換給另一個Process執行，避免CPU idle，即 _只要系統內有過多的Jobs存在_ ，則CPU idle機會下降

2. Multi Progrmaming Degree = 系統內之Processes數目
	- 一般而言若Degree越高，則_CPU utilization_越高

> Note:CH8 Virtual Memory Thrashing狀況除外

3. 多個Processes_同時執行_之方法有二
	- Concurrent excution 一顆CPU輪番交錯使用
	- Parallel excution (eg·多顆(核)CPU)

## Time-Sharing System(分時)
1. Def: 又叫 _Multitasking_[恐] It's a _logical extension of Multi Programming System_ 與 Multiprogramming最大差異: _CPU的切換頻率極高_ (CPU switching highly frequently)
	- Time-Sharing Features還有：
		- 強調對User的response time 要短(eg < 1 sec)
		- 適用於 _"user Interactive"_ computing/Enviroment 
		- CPU Scheduling 採RR排班法則(Ch4),對 User _公平_ 使用
		- 使用 virtual Memory 技術，擴展 "logical" memory space
		- 使用 "spooling" 技術實現I/O Device之共用(類似 "Buffering" 技術)讓每個user皆以為自己有" _專屬_ "的computer

## Multiprocessors System
1. Def:又叫 _Multiprocessing_ or _Parallel_ or _Tighty Coupled_ system
	- 主要feature如下:
		- 一個機器(or Motherboard)內有 _多個_ processors (or CPU)
		- 這些CPU彼此 _共享_ 此機器的 _Memory_, _Bus_, _I/O Device_, Power Supply, etc
		- 通常受 _同一個Clock_ 之時脈控制
		- 由 _同一個OS_ 管理
		- Processor之間的溝通,大都採用 _"Shared Memory"_ 方式

2. Benefits(好處)
	- Increased Throughput
		- 可支持 _多個工作在不同CPU上平行執行_ (Parallel Computing)
		- Note: _N顆_ CPU之產能 _絕對小於_ _1顆CPU產能 ＊ N倍_
		- 原因是有: 
			- 1. Resource contention 
			- 2. Processor 之間 Communication 
	- Increased Reliability 可靠度
		- 萬一某個CPU壞了，則System不會因而停頓，因為其他CPU仍可運行
	- Economy of scale 符合經濟效益
		- 運算能力之擴充符合經濟效益
			- N顆CPUs存在一部機器內與 _N部機器_ 相比成本較為便宜
			- 這些CPU _共享_同一部機器之 Memory, Bus, I/O-Device
	- Note:
		- Graceful degradation:
			- Def: system不致因爲某些HW/SW元件故障而停頓，仍然保持持續運作之能力，此性質稱之，又叫_Fail-Soft_
		- Fault-Tolerant System (容錯系統)
			- 具有gracefult degradation 性質之系統稱之

3. Muliprocessor sys可在細分為 _2種_ Subtypes
	1. SMP (Symmetric MultiProcessors) 對稱
		- Def:每個Processor之工作能力是相同的(identical)且每個Processor皆有 _對等的權力_ 來 _存取資源_
		- 優點:
			1. 可靠度叫ASMP高
			2. 效能較高
		- 缺點: SMP的 _OS_ 設計開發較為 _複雜_ (互斥存取之機制)  
	2. ASMP (Asymmetric MultiProcessors) 非對稱
		- Def: 每個Processor之工作能力 _不盡相同_ 通常採取 "Master-Slave"架構，Master Processor負責工作分派及資源分配，監督Slave等管理工作，其他Slave Processor負責執行工作
		- 優點:Asmp的OS設計開發較為 _simple_ 與 _Single-CPU OS版_ 類似
		- 缺點:
			1. 可靠度低 Than SMP
			2. 效能較低 (Master CPU是瓶頸)

4. MultiProcessor VS Multi Core CPU
	- 就OS而言肥有差異，即將一個Core視為一個LogicalCPU在看待 eg. 主機板裝了4顆雙核之CPUs，對OS而言即有_4 * 2 = 8_ 個CPU可用
	- 優點:
		1. Power saving
		2. 速度較快 (同一個晶片內資料傳輸速度快)


## Destributed System 分散式系統

1. Def:又叫 _Loosely-Coupled_ System (鬆散耦合)
	- 主要Feature如下:
		1. _多部機器_ 彼此透過 Network(or Bus)互相串連
		2. 每部機器之CPU有個自私有的Memory, Bus, I/O-Device, etc 並非共享
		3. 各CPU之Clock時脈控制不一定相同
		4. 各CPU上之OS也不一定相同
		5. 各Process之間的溝通大都採用 "_Message Passing_" 方式
			- Step: 
				1. 建立Communication link
				2. Message互相傳輸
				3. 釋放link

2. 構建之理由(好處)
	1. Increased Throughput (支持Parallel Computing)
	2. Increased Reliatility
	3. Resource Sharing (Cost降低)
		- 說明：支持"Client-Server" Computing Model之實施
		1. Server：提供某些服務的機器 eg. Mail-Server, File-Server, DNS, Computing Server
		2. Client：本身不提供服務且他需某些服務時，則發請求給Server，Server服務完再將結果傳回Client
		- Note: _"Peer-to-Peer"_ Model
			- Peer：同時只有 _Server_ 及 _Client_ 角色
	4. Remote sites communication 需求被滿足 via Internet

***

## Real-Time System 即時系統

1. 分為兩種
	1. Hard Real-Time
	2. Soft Real-Time 

2. Hard Real-Time System
	1. Def: "This system must ensure the critical task Complete on time" 即工作必須在規定的時間限制內完成，否則及算失敗
	2. 例:軍事防衛系統，核能安控，工廠自動化生產，機器人控制
	3. 圖示
<pre>
                                       .─────────────.                   
                                    ,─'               '─.  ◀────────────┐
                                   (     Environment     )              │
                                    `──.             _.─'  ◀──────────┐ │
                 Sensor                 `───────────'                 │ │
          .─.      .─.      .─.                                       │ │
         (   )    (   )    (   )                                      │ │
          `─'      `─'      `─'                                       │ │
           │        │        │                                        │ │
     Data  │        │        │                                        │ │
           │        │        │                                        │ │
           ▼        ▼        ▼                                        │ │
      ┌────────────────────────────┐                                  │ │
      │                            │          ┌──────────────┐        │ │
      │                            │ ────────▶│  Controller  │────────┘ │
      │  Compute,Determine,Decide  │          └──────────────┘          │
      │                            │          ┌──────────────┐          │
      │                            │ ────────▶│   Devices    │──────────┘
      │                            │          └──────────────┘           
      └────────────────────────────┘                                     
</pre>

	4. 設計考量：
		1. 所有時間延遲之因素皆需內入考量，eg: Sensor Data 傳輸速度，運算速度，Signal傳輸，etc 確保這些時間加總能滿足(小於) 時間deadline要求
		2. 所有會造成處理時過久或無法預測之設備或機制宜少採用或不用
			- 例: Disk宜少用或不用， _Virtaul Memory_ _絕不採用_
		3. 就 CPU Scheduling 設計(Ch4)而言，須先考量schedulable與否，在運行排程(eg rate-monotonic,EDF Scheduling)
		4. Time-Sharing sys _無法_ 與之並存
		5. OS 所造成的 Dispatch latency, etc宜降低
			- Note: ㄧ般實務上,Hard Real-Time sys _少有OS_ (OS,幾乎不存在)，尤其是embedded real-time sys
		6. 現行的商用OS _不支援_ Hard Real-Time feature 通常是客製化設計

3. Soft Real-Time System
	1. Def："This system must ensure the _real-time process get the highest priority_ then the others and _return this_ priority level until is complete"
	2. 例：Multimedia system,simulation system, VR system,etc
	3. 系統設計考量：
		1. 就CPU scheduling設計而言(Ch4)
			1. 必須支援Preemptive priority scheduling
			2. _不可_ 提供"Aging" 技術
		2. 盡量降低 Kernel 的 Dispatch latency time(Ch4)
		3. 可支援virtaul memory並存，但是要求 Real-Time Process 的全部 Pages 必須接待在 Memory 直到完工
		4. 與Time-sharing sys可以並存 eg:Solaris
		5. 一般商用OS，皆支持Soft Real-Time-sys 之 feature

## Batch System(批次)

1. Def: 將一些 _較不緊急_, _定期性_, _妃交談互動性_ 之Job累積成堆，再分批送入系統處理
	- 例: 庫存盤點、報稅、掃毒、磁碟重組
2. 主要目的:提高Resource utilization尤其在冷門時段，不適合用在 _Real-Time sys_, _User-Interactive application_(game)

## Hand Held System
1. 例:PDA、 _SmartPhone_
2. Hardware天生限制帶來Software必須配合之處

<table>
<tr>
	<td></td>
	<td>HW 天生限制</td>
	<td>SW 必須配合之設計</td>
</tr>
<tr>
	<td>1.</td>
	<td>Slower Processor(power供應，散熱處理)</td>
	<td>運算宜簡單，不宜複雜</td>
</tr>
<tr>
	<td>2.</td>
	<td>Memory空間有限</td>
	<td>程式Size宜小，不用的Memory space立刻釋放</td>
</tr>
<tr>
	<td>3.</td>
	<td>Display Monitor很小</td>
	<td>顯示內容宜有所刪減，eg:手機網頁</td>
</tr>
</table>
