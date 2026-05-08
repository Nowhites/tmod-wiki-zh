# 介绍
在继续本指南之前，你应该先阅读我们的[基础网络代码](basic-netcode)指南。

# 实践建议
一般来说，你需要让数据包保持较小，以便它们能够快速地在客户端之间发送，不会对网络造成任何问题。大的数据包或发送多个数据包会导致网络拥塞，可能会在玩游戏时造成卡顿或问题。如果一个模组在多人游戏中表现不佳，这可能就是原因。如果一个模组在多人游戏中确实有bug，通常是因为它们根本不发送数据包，客户端没有同步。

为了帮助制作较小的数据包并最小化你需要发送的数据包数量，你应该尽可能使代码具有确定性。确定性意味着客户端上的代码是一致的，不需要它们之间的进一步交互就能处于相同状态。这意味着不需要它们之间的网络来确保每个人都在他们的客户端上得到相同的结果。

确保确定性并不总是容易的，但在回答以下问题时很容易检查：
> "当这个代码在它们上面单独运行时，多个客户端是否处于相同状态（或具有相同结果），且它们之间没有任何交互？"

如果答案是肯定的，这意味着你的代码足够具有确定性。如果所有客户端能够同时到达这一点，就不需要更多的网络来确保相同的结果。

# 数据包流程
网络数据包有几种可能的流程：

**流程1**：客户端 -> 服务器 -> 客户端
这种流程非常常见。客户端向服务器发送数据包，然后服务器将此数据包转发到所有其他连接的客户端（或指定的客户端）。

**流程2**：客户端 -> 服务器
这种流程也很常见，用于当客户端需要指示服务器进行某种更改时。
例如，可能运行一个更改服务器行为的（聊天）命令，也就是服务器控制的东西。

**流程3**：服务器 -> 客户端
这种流程不如其他两种流程常见，用于让服务器指示客户端某些事情。
这种流程在游戏中当客户端不同步时很常见，服务器发送信息让它重新同步。

# ModPacket
### 创建和发送数据包
如果你想在模组中发送数据包，你将使用ModPacket类。这个类设计用于在上述任何指定流程中发送数据。一个非常简单的数据包可能像这样：
```cs
ModPacket myPacket = myMod.GetPacket();
//上面这行也可以写成"ModPacket myPacket = ModContent.GetInstance<yourMod>().GetPacket();"
myPacket.Write("Hello world!");
```
现在你有了一个包含字符串数据"Hello World!"的数据包。为了将数据包发送到服务器，在ModPacket上调用`Send`函数：
```cs
myPacket.Send();
```

你可能已经意识到我们现在处于流程2中：我们正在向服务器发送数据包。现在由服务器对这个数据包做些什么。

### 接收数据包
对于接收和读取数据包，你需要在你的Mod类中重写`HandlePackets`方法：
```cs
public override void HandlePacket(BinaryReader reader, int whoAmI) {
    string msg = reader.ReadString(); // "Hello world!"
}
```
无论何时从客户端（如果是服务器）或服务器（如果是客户端）收到数据包，都会调用`HandlePackets`。whoAmI是发送数据包的任何人的ID（等同于发送者的Main.myPlayer），reader用于读取数据包的二进制数据。**因此，为了处理服务器上客户端发送的数据包，你必须确保你的模组也在服务器上运行。**

你显然需要更优雅地编写这个方法，这样你就可以处理不同类型的数据包。给你的数据包写一个标识符是很常见的，这样你就可以识别数据包是关于什么的。然后你可以读取这个类型并执行相应的读取逻辑：
```cs
ModPacket myPacket = myMod.GetPacket();
myPacket.Write((byte)0); // id
myPacket.Write("Hello world!"); // message
myPacket.Send();
```
```cs
public override void HandlePacket(BinaryReader reader, int whoAmI) {
	byte msgType = reader.ReadByte();
	switch (msgType) {
		case 0:
			string msg = reader.ReadString(); // "Hello world!"
			break;
		case 1:
			... // 另一种消息类型放在这里
			break;
		default:
			Logger.WarnFormat("MyMod: Unknown Message type: {0}", msgType);
			break;
	}
}
```

### 读取数据包
读取就像写入一样，你需要从BinaryReader对象调用任何适当的读取函数。非常常见的有：`ReadString()`、`ReadInt32()`、`ReadByte()`和`ReadSingle()`（读取浮点数）。

### 按正确顺序写入和读取
非常重要，你需要记住数据包是**FIFO**：先进先出。
**你必须按发送顺序读取数据**。
例如，让我们考虑一个你发送一个字节、一个整数然后一个浮点数的数据包。我们将使用一些与数据包ID一起的任意数字。你将如下写入和发送数据包：
```cs
ModPacket packet = mod.GetPacket();
packet.Write((byte)0); // id
packet.Write(100); // 进度
packet.Write(1.0f); // 难度
packet.Send();
```
你必须在接收端以相同顺序读取数据包，所以：
```cs
byte id = reader.ReadByte();
int progress = reader.ReadInt32();
float difficulty = reader.ReadSingle();
```

# 网络优化
糟糕的网络代码会导致你的模组在多人游戏中表现不佳。让我们讨论一些概念以确保不会发生这种情况。

**读取不足或写入过多**
一般来说，你必须记住**只发送你实际会使用的数据**。如果你发送的数据多于你读取的数据，这被认为是读取不足或写入过多。从本质上讲，你用冗余数据（你不使用的数据）阻塞了网络。**始终确保你发送的所有数据都被实际使用**。

**通常不需要发送的常见数据是计算值，或者而是计算参数**。
考虑你在一个客户端上计算了一个数字，而这个数字需要发送到另一个客户端。你可以选择发送用于计算数字的非确定性参数，或者你可以选择发送计算后的数字。例如，如果我们取简单的整数乘法，后者选择将是最好的，因为我们只发送一个整数而不是两个整数。然而，在某些情况下，你可能从这两个参数中得到多个结果，在这种情况下，最好发送参数而不是计算后的值，然后在另一个客户端上重新计算。

**在正确的时刻发送数据包**
为了让网络不会因大量数据包而溢出，你需要考虑什么时候是发送数据包的正确时刻。例如，考虑一个通知Boss死亡的数据包。你可以选择每帧发送一个包含Boss生命值的数据包，并在接收端检查这个值。然而，游戏更新的间隔会导致你每秒发送数十个数据包！可以想象，以这种方式保存大量数据的数据包会阻塞网络，导致游戏表现不佳。

**确保你在应该发送数据包的地方发送数据包，在这种情况下是Boss死亡时。** ModNPC有一个用于此的钩子。

**在很多情况下，你根本不需要发送数据包；只需使用提供的tML钩子之一。** 在给出的例子中，我们也可以只在Boss的死亡钩子中检查运行实例是否是服务器，然后运行我们需要的任何逻辑。通常已经有一个你可以用于你的逻辑的钩子可用。我们的[基础网络代码指南](basic-netcode)更深入地讨论了这个问题。

但是你可能想知道，**什么时候发送数据包？** 通常发送数据包的好时机是非确定性行为时。再次考虑多人游戏中的Boss。你可能已经编程让AI半随机地切换Boss的阶段。在多人场景中，这种随机切换应该发生在服务器上，此时服务器应该向所有客户端发送数据包通知阶段变化。通过这种方式，所有客户端将保持同步，因为它们被服务器通知。如果客户端自己决定什么时候切换阶段，每个客户端将在不同时间切换，彼此不同步。

# 为ModPacket建模发送和接收
一般来说，很容易建模你应该如何从数据包写入和读取数据。**你应该按发送的相同顺序读取数据**。考虑以下场景：
```cs
float someVal;
int someInt;

public void Send(int toWho, int fromWho)
{
	ModPacket packet = mod.GetPacket();
	if (Main.netMode == NetmodeID.Server) {
		packet.Write(fromWho);
	}
	packet.Write(someVal);
	packet.Write(someInt);
	packet.Send(toWho, fromWho);
}

public void Receive(BinaryReader reader, int fromWho)
{
	if (Main.netMode == NetmodeID.MultiplayerClient) {
		fromWho = reader.ReadInt32();
	}
	someVal = reader.ReadSingle();
	someInt = reader.ReadInt32();
	if (Main.netMode == NetmodeID.Server) {
		Send(-1, fromWho);
	} else {
		MyModPlayer myModPlayer = Main.player[fromWho].GetModPlayer<MyModPlayer>();
		myModPlayer.someVal = someVal;
		myModPlayer.someInt = someInt;
	}
}
```

在上面的例子中，我们展示了你如何建模你的发送和接收方法来处理所有相关方：连接的客户端和服务器。
在Send()方法中，我们发送一个包含浮点数和整数值的数据包给某个特定目标，并忽略传入的fromWho客户端。在Receive()方法中，我们读取数据包数据，然后根据我们是服务器还是客户端做些什么。你会看到我们根据Main.netMode读取fromWho数据。这是因为数据包的这一部分只在服务器发送时才存在，所以我们只能作为客户端时读取它。你可以用这个来访问最初发送数据包的客户端，而不是256作为fromWho，这表示服务器。

如果你仔细比较Send()和Receive()，你会发现读取和写入的顺序是一致的。在Receive()方法的末尾，我们再次调用Send()并传递-1作为toWho，这表示除了fromWho客户端外的每个连接的客户端。这使得服务器将数据包转发给其他客户端。条件地，如果我们不是服务器（所以我们是连接的客户端），我们可以将新读取的值设置在我们实际需要的地方。

# 管理多个数据包的好实践
如果你在一个大型模组上工作，你可能会发现自己在发送许多数据包，如果没有适当的设置，管理它们可能会很麻烦。建议使用PacketHandler类来更容易地处理不同的数据包。随意使用这个类：
```cs
	internal abstract class PacketHandler
	{
		internal byte HandlerType { get; set; }
		
		public abstract void HandlePacket(BinaryReader reader, int fromWho);

		protected PacketHandler(byte handlerType)
		{
			HandlerType = handlerType;
		}

		protected ModPacket GetPacket(byte packetType, int fromWho)
		{
			var p = MyMod.Instance.GetPacket();
			p.Write(HandlerType);
			p.Write(packetType);
			if (Main.netMode == NetmodeID.Server)
			{
				p.Write((byte)fromWho);
			}
			return p;
		}
	}
```
这个类为你处理一些事情，包括写入处理器类型和数据包类型。这在接收数据包时会有用，知道转发到哪里。

你可以继承这个基类，例如在为"MyBossNPC"制作处理器时：
```cs
	internal class MyBossNPCPacketHandler : PacketHandler
	{
		public MyBossNPCPacketHandler(byte handlerType) : base(handlerType)
		{
		}

		public override void HandlePacket(BinaryReader reader, int fromWho)
		{
			throw new NotImplementedException();
		}
	}
```
如果我们按照之前的例子，你可能想要同步这个Boss的目标。你可以修改类来反映这一点。你需要让HandlePacket按数据包类型切换，并调用适当的处理方法：

```cs
	internal class MyBossNPCPacketHandler : PacketHandler
	{
		public const byte SyncTarget = 1;

		public MyBossNPCPacketHandler(byte handlerType) : base(handlerType)
		{
		}

		public override void HandlePacket(BinaryReader reader, int fromWho)
		{
			switch (reader.ReadByte())
			{
				case (SyncTarget):
					ReceiveTarget(reader, fromWho);
					break;
			}
		}

		public void SendTarget(int toWho, int fromWho, int npc, int target)
		{
			ModPacket packet = GetPacket(SyncTarget, fromWho);
			packet.Write(npc);
			packet.Write(target);
			packet.Send(toWho, fromWho);
		}

		public void ReceiveTarget(BinaryReader reader, int fromWho)
		{
			int npc = reader.ReadInt32();
			int target = reader.ReadInt32();
			if (Main.netMode == NetmodeID.Server) {
				SendTarget(-1, fromWho, npc, target);
			}
			else {
				NPC theNpc = Main.npc[npc];
				theNpc.oldTarget = theNpc.target;
				theNpc.target = target;
			}
		}
	}
```

最后一件事是，当你模组收到数据包时，实际调用适当的PacketHandler类。你可以创建一个全局类来为你处理这个。在你的mod类中：
```cs
public override void HandlePacket(BinaryReader reader, int whoAmI) 
{
	ModNetHandler.HandlePacket(reader, whoAmI);
}
```

在你的新ModNetHandler类中：
```cs
internal class ModNetHandler 
{
	// 当添加很多处理器时，自动化创建它们可能是明智的
	public const byte MyBossNpcType = 1;
	internal static MyBossNPCPacketHandler myBossNpc = new MyBossNPCPacketHandler (MyBossNpcType);
	public static void HandlePacket(BinaryReader r, int fromWho)
	{
		switch (r.ReadByte())
		{
			case MyBossNpcType:
				myBossNpc.HandlePacket(r, fromWho);
				break;
		}
	}
}
```

通过这个设置，你可以继续创建新的PacketHandler类，以更全局的方式处理某些主题。
