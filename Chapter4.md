# Process Management 與 Thread Managemnet
- Process 定義與 Program 比較
- PCB 內容(Process Control Block)
- Process State Transition Diagram(STD)
- Scheduler 種類(3種 長/短/中期)
- Context switching
- Dispatch, Dispatch Lantency
- Process control operation(以 System call 為例:程式追蹤)
- 評估CPU Scheduling performace 之5個 criteria
- 各種CPU Scheduling 法則介紹及相關名詞(7個)
- 特定系統之排班設計
	- Multiprocessors
	- Real-Time sys (Hard/Soft)
	- Thread Management
- Thread


</br></br>
## Process

1. Def: A Program in excution[恐]，Process建立後，其主要主成有
	- Process No (ID)
	- Process State
	- Code Section, Data Section
	- Programming Counter(PC)
	- Stack
	- CPU register value. etc
	- 是OS分配resource之對象單位(eg:CPU, Memory, I/O-device)
2. 與Program(程式之不同)
 
	<table>
	<tr>
		<td>Process</td>
		<td>Program</td>
	</tr>
	<tr>
		<td>A program in excution,</td>
		<td>Just a File stored in the storage,</td>
	</tr>
	<tr>
		<td>"Active" entity</td>
		<td>"Passive" entity</td>
	</tr>
	</table>


</br></br>
## Process Control Block 內容(PCB)

1. Def:OS為了管理所有Process，會在kernel memory中，替每個Process各自準備一個 _Block(表格)_，紀錄Process之所有相關資訊
	- PCB主要內容有
		- Process No(ID):是 _unique_ (唯一的)
		- Process _state_ :eg ready, running, wait etc.
		- Programming Counter: 內放 the _next_ instruction address
		- CPU register:紀錄使用到的各種Register之值 eg:PSW, Stack Top, Accumulator
		- CPU Scheduling information: eg: Process之優先權值, arrival time etc.
		- Memory Management Information: eg: Base/limit register value, page table, etc
		- Accounting info eg: Process使用了多少CPU Time 還剩多少, 用了哪些Resource
		- I/O status info eg: Process發出了多少I/O-request, 目前之I/O處理狀況, 分配了哪些I/O-Device, File open數有多少

## Process State Transition Diagram (狀態轉換圖)

1. 目的:描述Process之life cycle
2. 版本:
	1. 5個State之STD [恐]
	2. 7個State之STD [Stalling]
	3. Unix之STD [Stalling]


</br></br>

## 5個State之STD[恐]

<table>
	<td>
		<pre>
               ┌─────────┐               
               │   New   │               
               └─────────┘               
                    │                    
                   1│                    
                    │                    
                    ▼                    
               ┌─────────┐         5     
          ┌───▶│  Ready  │◀─────────┐    
          │    └─────────┘          │    
          │         │          ┌────────┐
        3 │        2│          │  Wait  │
          │         │          └────────┘
          │         ▼               ▲    
          │    ┌─────────┐          │    
          └────│ Running │──────────┘    
               └─────────┘        4      
                    │                    
                    │ 6                  
                    │                    
                    ▼                    
               ┌─────────┐               
               │  Exit   │               
               └─────────┘               
		</pre>
	</td>
		<td>
			<table>
				<tr>
					<td>New(Create)</td>
					<td>Process被建立，已分得PCB空間，尚未載入Memory(or 尚未取得Memory資源)</td>
				</tr>
				<tr>
					<td>Ready</td>
					<td>Processu已在Memory中，且在ReadyQueue內，具有資格爭奪CPU</td>
				</tr>
				<tr>
					<td>Running</td>
					<td></td>
				</tr>
				<tr>
					<td>Wait(Blocked)</td>
					<td>Processu在ReadyQueue中，wait for I/O-Complete or event occurs，不會與其他Process爭奪CPU</td>
				</tr>
				<tr>
					<td>Exit(Terminate,Zombie,Abort)</td>
					<td>Processu完成工作，正常結束或異常終止，此時可能其PCB尚未回收，因為要等其父親(Parent Process)Collect child process之成果後，才會回收PCB space</td>
				</tr>
			</table>
		</td>
</table>

<table>
	<tr>
		<td>Transition</td>
		<td>Descriptioin</td>
	</tr>
	<tr>
		<td>1</td>
		<td>Allocate process memory space或由 Long-Term scheduler 載入 Job 到 Memory 中(Batch sys)</td>
	</tr>
	<tr>
		<td>2</td>
		<td>由 short-term scheduler 自 ReadyQueue中排出一個process; 分派CPU給它</td>
	</tr>
	<tr>
		<td>3</td>
		<td>Process被迫放棄CPU. eg: Timeout, Interrupt</td>
	</tr>
	<tr>
		<td>4</td>
		<td>Process waits for I/O-Complete or event occurs</td>
	</tr>
	<tr>
		<td>5</td>
		<td>I/O-Complete or event occurs</td>
	</tr>
	<tr>
		<td>6</td>
		<td>Process完成工作 or 被中止</td>
	</tr>
</table>

## 7個State之STD[Stalling]

<table>
	<tr>
		<td>State</td>
		<td>Descriptioin</td>
	</tr>
	<tr>
		<td>Block / Suspend</td>
		<td>Block Process in a Disk</td>
	</tr>
	<tr>
		<td>Ready / Suspend</td>
		<td>Ready process in a Disk</td>
	</tr>
</table>

<table>
	<tr>
		<td>Transition</td>
		<td>Descriptioin</td>
	</tr>
	<tr>
		<td>1</td>
		<td></td>
	</tr>
	<tr>
		<td>2</td>
		<td></td>
	</tr>
	<tr>
		<td>3</td>
		<td></td>
	</tr>
	<tr>
		<td>4</td>
		<td></td>
	</tr>
	<tr>
		<td>5</td>
		<td>This is a poor Design,但由基於下列情況，可能存在</td>
	</tr>
	<tr>
		<td>6</td>
		<td>also poor design,支持理由,當Blocked/suspend process變成ready/suspend時OS有可能強迫低優先權的running process放掉CPU及Memory供高優先權</td>
	</tr>
</table>

## Unix STD[Stalling]
