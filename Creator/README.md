# Creator

## Thought
- 同Runtime层的协议。面向UI编程，提取操作指令协议，同Runtime通讯，此协议又可以作为Console这种形态的Shell使用。
- 同IO设备的协议。本Creator看作编辑器主机，还需要有个IO设备用作操作输入及显示器输出，之间形成IO协议。此IO设备可以是当下的js版本，也可以是新imgui版，也可以是内建GUI版本。
- 协议通讯的同步还是异步的问题。
- 循序渐进，先以IO类游戏需要设定里程碑。

## Challenge
- 作为一个编辑器工具，操作上有非常多的细节需要实现，以及软件过程中高覆盖的实时QA管控。
- 可被扩展，本来Creator与Runtime的可被扩展可看做两层面。但从现代引擎天然所必须具备的编辑器形态角度对于扩展C类用户来说，其扩展工作看起来都会是围绕Creator的。
