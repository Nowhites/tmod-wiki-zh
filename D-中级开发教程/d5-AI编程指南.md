以下指南旨在教授创建NPC AI的一些常见方法。大多数内容可以在弹射物和NPC之间互换使用，只需将npc替换为projectile，反之亦然。有些内容不能互换，如果弹射物有替代方法，会提到。

首先，让我们创建一个基本的NPC。本指南不会涵盖set defaults中的内容，你可以查看示例模组和[原版字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-NPC-Field-Values)来了解应该放什么。我们制作的大部分AI都将放入AI钩子中，所以请重写那个钩子。
```cs
//other stuff
public override void AI(){
     //AI每帧调用一次。你放在这里的任何内容每秒将发生60次。
}
```

我们现在有一个放置AI的地方，但它还什么都不做——让我们从改变NPC的速度开始。
速度是一个Vector2（由2个浮点数x和y组成），对应于每帧（每秒60次）NPC在AI执行后x和y方向上变化的像素数。

速度为-5,5意味着每帧向左移动5像素和向下移动5像素（请记住，在泰拉瑞亚中，向y加相当于向下）。改变速度是让NPC移动的最常见方式，它会在帧之间保持——如果我设置为5,5，它将保持这种方式直到其他东西改变它。

让我们通过改变速度让NPC向上漂浮。
```cs
public override void AI(){
      npc.velocity = new Vector2(0, -5);//减去Y以向上移动
      //AI每帧调用一次。你放在这里的任何内容每秒将发生60次
}
```

如果你测试这段代码，你应该看到你的NPC向上飞。不过你可能希望你的NPC做更复杂的事情，为此我们需要开始向AI添加更多内容。

//TODO 动作中的gif

计时器在本[指南](https://github.com/tModLoader/tModLoader/wiki/Time-and-Timers)中有介绍，如果你还没有读过应该先读一下。让我们像该指南展示的那样添加一个计时器
```cs
int Time = 0;
public override void AI(){
    Time++;
    //npc.velocity是每帧（每秒60次）NPC的像素变化量
    npc.velocity = new Vector2(0, -5);//减去Y以向上移动
    //AI每帧调用一次。你放在这里的任何内容每秒将发生60次
}
```

每次我们的NPC存活的一个tick，我们的AI都会执行，增加计时器。这允许我们根据NPC存活的时间来让某些事情发生，而不仅仅是每tick都做事。

现在我们有了追踪时间的变量，我们可以使用%让事情基于计时器发生。%计算左操作数除以右操作数后的余数，可用于让某些事情每x tick发生一次，像这样：
```cs
int Time;
public override void AI(){
     Time++;
     npc.velocity = new Vector2(0, -5);//减去Y以向上移动
     if(Time % 120 == 0){
           //这里的代码每120 tick或2秒发生一次
     }
     //AI每帧调用一次。你放在这里的任何内容每秒将发生60次
}
```

在我们的NPC存活的第一个tick，我们的AI方法首先执行。首先时间递增，变成1。然后速度改变，然后检查if语句。Time % 120取2的余数，这里是1/120的余数，是1。最后我们用==0检查它是否等于0。1不等于0，所以括号内的代码运行。最后，在我们的AI运行后，NPC根据其速度移动。由于我们的速度是0,-5，它移动那么多像素，从起始位置向上移动5像素。

1/60秒后，在下一个tick我们的AI再次运行。这次，Time变成2（如果你想知道为什么Time在tick之间保持其值，那是因为它是一个字段——它在方法外声明）。同样，Time(2) % 120不等于0，所以我们的if语句不会激活。最后我们的速度再次更新。

我们的AI继续每tick运行，我们的NPC将由于其速度向上漂浮。60 tick后是1秒。120 tick后是2秒，由于120除120等于1余0，我们if语句中的代码将被激活。240 tick或4秒后，if语句将再次激活，因为240/120等于2余0。这种每2秒或120 tick发生一次的模式将持续到NPC死亡。

//TODO 也许在NPC上方绘制时间的gif

现在我们可以周期性地让事情发生，但如果我们实际上不能做某事，这有什么好处呢？

人们想让Boss做的最常见的事情之一是发射弹射物，这很简单——调用NewProjectile方法。但是要生成弹射物，你需要提供关于要生成的弹射物的某些信息。它应该从哪里开始？它应该造成15点伤害还是500点伤害？它是木箭还是来自其他模组的弹射物？

信息的填充顺序如下：
```cs
Projectile.NewProjectile(float X, float Y, float SpeedX, float SpeedY, int Type, int Damage, float KnockBack, int Owner = 255, float ai0 = 0f, float ai1 = 0f);
```
请注意，有一个不同的重载使用Vector2而不是浮点数作为x/y和SpeedX/SpeedY。为了一致性，我不会使用它，但它实际上是一样的。

浮点数X是生成位置的X坐标，Y是Y坐标（我们的弹射物在哪里？）。SpeedX和SpeedY都是生成时的x和y速度（弹射物应该向哪个方向移动？）。

Type是弹射物的类型（应该生成什么弹射物？）这可以是原版弹射物ID，如
`ProjectileID.WoodenArrowHostile`，或者是模组弹射物，如`ModContent.ProjectileType<MyClass>()`。请确保分别使用`Terraria.ID`或你弹射物类的命名空间。

Damage不言自明，请记住在弹射物中设置projectile.Damage不起作用，必须在这里设置。

//TODO 据我所知，这在expert模式中会加倍和翻四倍，检查一下

Knockback也不言自明，但注意它是一个浮点数，所以可以接受小数。

//TODO 解释多少 knockback 对应多少距离。

Owner、ai0和ai1都是可选参数（看它们后面有= something，表明它们的默认值），如果你不知道放什么可以安全地省略。Owner是"拥有"弹射物的玩家的whoami，没有则为255。ai0和ai1用于向生成的弹射物传递信息，稍后在本指南中会有专门的部分介绍。

看起来有很多，但幸运的是编写起来简单得多
这段代码从NPC中心垂直向上发射木箭弹射物，伤害50，无击退
```cs
Projectile.NewProjectile(npc.Center.X, npc.Center.Y, 0, -50, ProjectileID.WoodenArrowHostile, 50, 0f);
```

如你所见，每个参数都填写了关于如何创建弹射物的信息。我们希望它在NPC中心的x和y坐标生成，速度为0,-50即向上每tick移动50像素。它应该是一个造成50点伤害且0击退的木箭。

但是，如果你把这代码加入AI，你可能会在多人游戏中遇到奇怪数量的弹射物。这是因为泰拉瑞亚处理多人游戏中NPC的方式。假设2个人连接到一个多人游戏，当你的NPC生成时。

在这个假设游戏中，你的NPC将在3个位置创建。在每个客户端上，以及在服务器上。这3个位置中的每一个现在都将运行你的AI代码，就像在单人游戏中运行一样。在每个客户端和服务器上，它移动完全相同，因为每个上都运行相同的AI（除非有什么随机内容）。然后，NPC调用NewProjectile。在所有3个游戏中弹射物都被生成，但然后，所有3个将它们生成的弹射物同步到其他2个，我们最终在每个上得到3个——每个连接的客户端和服务器都生成弹射物。

解决方案是什么？不要在连接的客户端上发射弹射物，让NewProjectile同步它们。我们通过检查`Main.netMode`来做到这一点，它告诉我们是在单人游戏（等于NetmodeID.SinglePlayer）、客户端（NetmodeID.MultiplayerClient）还是服务器（NetmodeID.Server）。
```cs     
int Time;
public override void AI(){
     Time++;
     if(Time % 120 == 0 && Main.netMode != NetmodeID.MultiplayerClient){
          //如果我们不是多人客户端且已经过了120 tick
          //这里的代码每120 tick或2秒发生一次
          Projectile.NewProjectile(npc.Center.X, npc.Center.Y, 0, -5, ProjectileID.WoodenArrowHostile, 50, 0f);
     }
     //AI每帧调用一次。你放在这里的任何内容每秒将发生60次
}
```
### 寻找目标
现在我们知道了如何发射弹射物，让我们让它更难躲避，向玩家发射它。

为此，我们需要找到我们想要瞄准的玩家。对于找到NPC附近最近的目标，这非常简单。
首先调用`npc.TargetClosest(true/false)`，true/false是你是否希望NPC将精灵方向改变为面向目标（也可以留空。这会搜索附近可被NPC瞄准的玩家，然后将玩家的whoami（whoami将在后面详细介绍，简单地说它们是放置在各自Main数组中的东西）设置为npc.Target。

然后我们可以用Main.Player[npc.Target]获取玩家并将其存储在一个玩家变量中供以后使用。
```cs
npc.TargetClosest(false);
Player target = Main.player[npc.target];
//我们现在可以用target做事情
```
如果我们不想要最近的而是想要检查其他一些条件，我们可以循环Main.Player数组来找到有效目标。

另外，如果你在弹射物中并想要追踪玩家目标，这是一种方法（请记住，对于弹射物没有TargetClosest，开头的部分可以省略）。

从NPC发射弹射物时，一个更好的替代方法是传递NPC目标作为ai参数（参见下面关于如何使用ai参数传递信息的部分）。
```cs
npc.TargetClosest(false);
Player p = Main.player[npc.target];//首先获取最近的以防我们所有检查都失败
for(int i = 0; i < Main.maxPlayers; i++)
{
      if(Main.player[i].active){
           //用&添加其他检查
           p = Main.player[i];
      }
}
//使用p
```
有了我们的玩家变量，我们可以做很多事情。人们做的一件常见的事情是发射弹射物或让Boss向玩家移动。首先我们得到到玩家的方向，并将其保存在Vector2中
```cs
public override void AI(){
     npc.TargetClosest(false);//首先设置npc.target为最近的人
     Player target = Main.player[npc.target]; //然后从数组中获取玩家并将其存储在变量中
     Vector2 ToPlayer = npc.DirectionTo(target.Center);//然后我们创建一个到玩家中心的方向的Vector2
     //我们现在可以使用ToPlayer，因为它包含玩家相对于我们的方向
     //如果你想让ToPlayer的任何东西移动得更快，乘以某个值，如npc.Velocity = ToPlayer * 5f
}
```
发射弹射物应该是这样的
```cs
int Time;
public override void AI(){
     Time++;//增加我们的计时器以便我们可以用它来追踪我们已经存活了多少tick
     if(Time % 120 == 0 && Main.netMode != NetmodeID.MultiplayerClient)//如果已经过了120 tick且我们不是客户端
     {
          npc.TargetClosest(false);//设置npc.target为最近的玩家
          Player target = Main.player[npc.target];//获取我们刚才瞄准的玩家
          Vector2 ToPlayer = npc.DirectionTo(target.Center) * 5;//获取到玩家的方向。5是速度
          Projectile.NewProjectile(npc.Center.X, npc.Center.Y, ToPlayer.X, ToPlayer.Y, ProjectileID.WoodenArrowHostile, 50, 0f);//使用我们刚获得的方向向玩家发射弹射物
     }
     //AI每帧调用一次。你放在这里的任何内容每秒将发生60次
}
```
希望你能理解那里的一切作用。
Time变量追踪Boss存活的总tick数。`if(Time %120 ==0)`检查该变量是否是120的倍数，即每120 tick或2秒。
`npc.TargetClosest(false)`和`Player target = Main.Player[npc.target];`获取最近的玩家并将其存储在一个名为target的Player变量中。
`Vector2 ToPlayer = npc.DirectionTo(target.Center);`然后将我们想要发射弹射物的速度存储在一个Vector2中（本例中是从我们的NPC到玩家的方向）。最后我们在NPC中心生成一个弹射物，方向朝向玩家作为速度。

如果我们想飞向某个位置，我们只需将npc.velocity设置为一个方向Vector2，像这样：
```cs
npc.TargetClosest(false);
Player target = Main.player[npc.target];
Vector2 ToPlayer = npc.DirectionTo(target.Center) * 3;
npc.velocity = ToPlayer; 
```

如果我们想要冲刺，我们只需将上述代码放入一个像弹射物代码一样的if语句中。
//TODO 示例 + 解释原因
### 制作模式
大多数时候我们不希望我们的NPC只做一件事，而是希望它有多个可以执行的"攻击"。为此，你需要有一个变量来保存当前发生了什么攻击，这样我们的NPC可以在帧之间记住它在做什么。

在这种情况下，我们使用一个`int`来保存当前阶段。我们的第一次攻击对应0，第二次对应1，等等。然后，在每个AI tick的开始，我们可以使用这个来继续执行上一帧所做的攻击。

为了让这段代码更清晰，我将每次攻击分离到自己的方法中，并使用常量来表示每个阶段的"ID"。这些常量意味着与它们代表的数字相同的东西，只是更容易阅读。当你看`if(stage == ChaseState)`而不是`if(stage == 1)`时，这就意味着更多。

使用状态的另一个问题是同步。当一个NPC有不同的事情可以同时做时，必须确保它在多人游戏中的所有客户端上做相同的事情。

解决方案是将变量（如我们的状态）保存在npc.ai数组中。这个数组（浮点数数组）会自动在客户端之间同步，所以我们的NPC的状态在所有客户端和服务器上总是相同的。

最后一个改变是将每次攻击的代码保存在自己的方法中，然后在AI中调用它们。这纯粹是为了组织，使AI中的代码更容易阅读。

```cs
//用于使代码可读的常量变量
const int ProjectileState = 0;//不是检查state是否为0，我们可以检查它是否为ProjectileState。在应用中它们意味着相同的事情，但前者看起来好多了。
const int ChaseState = 1;
const int CircleProjectileState = 2;
//在这里我创建了用于保存我们所在状态的变量。
//使用属性，这个变量实际上与npc.ai[0]相同。设置它的值就是设置ai[0]，获取它的值就是返回ai[0]。
//这样做是因为如果我们在多人游戏中，我们的状态在不同的客户端/服务器上可能不一样，导致NPC不同步。为了避免这个问题，我们使用ai数组元素之一作为我们的状态。这个数组在客户端和服务器之间同步，所以我们的NPC不会不同步。
float State
{
    get => npc.ai[0]; //当获取这个变量的值时，返回npc.ai[0]的值 
    set => npc.ai[0] = value; //当设置这个变量为某个值时，设置npc.ai[0]的值
}
//你可以写npc.ai[0]而不是state，它会工作相同，但这使代码更具可读性。或者，如果你用完了npc.ai槽或不想使用它，你可以使用接收和发送额外AI钩子来同步你的值。

//在这里我对计时器和不同的AI槽做同样的事情。
float Timer
{
    get => npc.ai[1];
    set => npc.ai[1] = value;
}
public override void AI()
{
    //由于所有状态都增加Timer并使用npc.Target，我们可以把它们的共同代码放在这里
    Timer++;//将计时器增加1
    npc.TargetClosest(false);//设置npc.Target为最近的玩家
    if (State == ProjectileState)
    {
        ProjectileAttack();//运行攻击方法
        if (Timer == 180)
        {
            //如果已经过了3秒，
            Timer = 0;//重置计时器
            State = ChaseState;//转到另一个状态
            /*如果我想要随机攻击而不是固定顺序呢？
            使用Main.rand.Next(bottom, top +1)来获取随机数，所以我们可以这样使用它
            state = Main.rand.Next(0,3);//获取0-2的随机数
            npc.netUpdate = true;//在这个tick更新我们的NPC
            */
        }
    }
    else if (State == ChaseState)
    {
        Chase(); //调用下面定义的追逐方法
        if (Timer == 240)
        {
            //如果已经过了4秒
            Timer = 0;//重置计时器
            npc.velocity = Vector2.Zero;//由于我们在这个状态中改变了速度，我们需要在进入新阶段之前重置它
            State = CircleProjectileState; //将我们的状态设置为另一个
                                           //state = Main.rand.Next(0,3); - 随机状态
                                           //npc.netUpdate = true;
        }
    }
    else if (State == CircleProjectileState)
    {
        CircleProjectile();
        if (Timer == 180)
        {
            //如果已经过了3秒
            Timer = 0;//重置计时器
            State = ChaseState;//转到另一个状态
                               //npc.netUpdate = true;
                               //state = Main.rand.Next(0,3); - 随机状态
        }
    }
}
//在AI之外，我们为每个状态创建方法，然后调用它们
private void ProjectileAttack()
{
    if (Timer % 20 == 0 && Main.netMode != NetmodeID.MultiplayerClient)
    {
        Player target = Main.player[npc.target];//获取最近的玩家
        Vector2 ToPlayer = npc.DirectionTo(target.Center) * 3;//改变3来改变速度
        Projectile.NewProjectile(npc.Center.X, npc.Center.Y, ToPlayer.X, ToPlayer.Y, ProjectileID.WoodenArrowHostile, 50, 0f);//发射弹射物
                                                                                                                              //如果我想要模组弹射物呢？将类型参数(ProjectileID.WoodenArrowHostile)替换为ModContent.ProjectileType<MyClass>()并确保使用它的命名空间
    }
}
private void Chase()
{
    Player target = Main.player[npc.target];//获取最近的玩家
    Vector2 ToPlayer = npc.DirectionTo(target.Center) * 3;//获取到玩家的方向;改变3来改变速度
    npc.velocity = ToPlayer;//设置我们的速度为到玩家的方向，这样我们向它移动
}
private void CircleProjectile()
{
    if (Timer % 60 == 0 && Main.netMode != NetmodeID.MultiplayerClient)
    {
        //如果已经过了60 tick且我们不是多人客户端
        for (int i = 0; i < 360; i += 12)
        {
            //循环直到i = 360，完成一次完整旋转。你可以通过改变12来改变旋转间隔
            Player target = Main.player[npc.target];//获取最近的玩家
            Vector2 ToPlayer = npc.DirectionTo(target.Center).RotatedBy(MathHelper.ToRadians(i));//默认情况下，旋转使用弧度。我更喜欢使用度数，因此我转换它们
            if (Main.netMode != NetmodeID.MultiplayerClient)
                Projectile.NewProjectile(npc.Center.X, npc.Center.Y, ToPlayer.X * 5, ToPlayer.Y * 5, ProjectileID.WoodenArrowHostile, 50, 0f);//发射弹射物
        }
    }
}
```

### 使用WhoAmI和Main.数组
有时候我们想要生成一个NPC（也可以是弹射物甚至尘埃）并让它做一些需要从生成它的NPC获取目标、位置或其他值的事情，或者需要知道生成的NPC是否存活/它的血量或其他不是我们的NPC的属性。

世界上所有活跃的NPC、弹射物、尘埃和玩家（以及一些不活跃的）都是它们数组的一部分（数组就像一组相同数据类型而不是只有一个数据。例如，Main.npc是500个NPC都在一个地方。要访问数组中的一个项目，你使用array[x]，其中x是数组中第x个东西）。这些是游戏追踪要更新、绘制、同步等内容的的方式。它们还有另一个重要功能，允许通过这些数组让一个东西影响另一个东西。

这些数组是：
//TODO 获取其余的长度

* Main.projectile，容纳最多499个弹射物 + 一个虚拟弹射物（稍后会有更多介绍）
* Main.dust，容纳6000个尘埃和一个虚拟
* Main.npc
* Main.player，容纳254个玩家 + 一个虚拟玩家

当某物被创建时，比如说通过NewProjectile创建弹射物，它在数组中（这里是Main.projectile）获得一个位置，并为它在那里创建一个新的弹射物对象。

当这个弹射物被创建时，它的whoami被设置为它在数组中的位置。此外，生成它的方法（NewProjectile）将返回那个位置。这对尘埃和NPC也一样。你可以用这个在发射后修改弹射物，像这样：

```cs
int Index = Projectile.NewProjectile(parameters);//你显然需要填写参数
Projectile p = Main.projectile[Index];//这也可以应用于NPC——在调用NewNPC后使用NPC n = Main.npc[Index]
//你现在可以使用它的字段/方法
//例如 p.Center、p.active 或 p.Kill()
NetMessage.SendData(MessageID.SyncProjectile, -1, -1, null, Index);//之后确保调用这个以确保弹射物同步到多人客户端
//如果是NPC，将第一个参数替换为MessageID.SyncNPC
//像这样：NetMessage.SendData(MessageID.SyncNPC, -1, -1, null, Index); 
```

如果`Main.projectile[Index];`看起来对你来说很熟悉，那是因为你已经用过类似的东西来获取玩家目标。

但如果我想要我的生成弹射物（或NPC）拥有生成它的"父"的WhoAmI呢？这是我们使用生成方法的可选参数的情况之一。

如果你查看NewNPC/Projectiles的参数，你应该看到像ai0、ai1等浮点数。这些用于向生成的事物传递信息。
假设我在NewProjectile/NewNPC中设置ai0为15——那么在弹射物或NPC中，npc/projectile.ai[0]将是15。而且，更重要的是，如果我将其设置为父级的WhoAmI，我可以用npc/projectile.ai[0]获取父级。
```cs
//在父级中
Projectile.NewProjectile(npc.Center.X, npc.Center.Y, ToPlayer.X * 5, ToPlayer.Y * 5, ProjectileID.WoodenArrow, 50,0f,255, npc.WhoAmI);

//在生成的弹射物中
NPC owner = Main.NPC([(int)projectile.ai[0]]);//注意我在这里放了(int)——因为AI参数是浮点数且可以是小数，我必须将它们转换为一个整数来访问数组（注意这不会四舍五入，它只是删除小数点后的所有内容）
```

你也可以使用AI参数传递其他信息，比如目标玩家的whoami、攻击剩余时间等。

模组开发者常犯的一个错误是认为如果某物在其数组中且不为null，它就在世界中活跃。情况并非如此。如果一个NPC（或弹射物/尘埃等）死了，它会留在数组中直到被新的生成物取代。但是，它会有npc/projectile/dust.Active设置为false，阻止它更新。如果你自己循环这些数组，要注意这些不活跃的对象。

另一件要记住的事情是我之前提到的"虚拟"槽。这些数组在末尾有一个额外的槽，不会被更新，相反它持有一个不做任何事情的虚拟对象，在浏览数组时应该忽略。当循环它们时，使用Main.maxProjectiles和其他数组对应物来避免虚拟槽。

### 制作阶段
你可以让你的NPC根据某些因素选择不同的攻击，并通过将阶段选择代码放在自己的方法中然后调用而不是在攻击中设置状态，使你的代码整体更有组织。
让我们看一个之前的阶段，但这次如果生命值低于50，通过检查`npc.life`和`lifeMax`让状态设置为如果我们添加的新阶段
```cs

if(npc.life < (npc.lifeMax / 2){
     //如果低于50，设置它为其他东西，如阶段2攻击
     State = Below50Attack;//随机？Main.rand.Next(LowestStageAttack , HighestStageAttack + 1); 
}
else
{
     state = Main.rand.Next(0,3);//转到另一个状态
}
```

问题是，如果我们想在每个攻击后转换，我们必须在第一阶段的所有状态中都放这段代码，这有很多不必要的代码并导致其他问题。
我们可以创建一个改变阶段的方法，然后在我们的阶段中调用它，而不是这样
```cs
private int ChoosePhase(){
     //注意这是int，不是void。这意味着它必须返回一个值（本例中是int类型）
     npc.netUpdate = true;//在这个tick更新这个NPC，以同步我们的状态
     if(npc.life < (npc.lifeMax / 2)){
          return Main.rand.Next(4, 7);//如果我们的生命值低于一半，返回4-6。然后在AI中我们将检查设置为什么状态
     }
     /* else if(somecondtion){
          //像这样添加更多
          return somethingelse;
     }*/
     return Main.rand.Next(0, 3);//因为我们已经在低于50时返回了，我们不需要在else中这样做
}

//然后在我们每个状态中调用它来运行上面的代码
if(timer == phaselength){
     //用转换前攻击的tick数替换phaselength
     State = ChoosePhase();//调用方法获取新状态。这会使上面的代码发生并将State设置为返回的任何值
}
```

这样，我们避免了反复放置相同的代码，如果我们以后想编辑这段代码（比如添加另一个状态或删除一个），我们只需要在一个地方更改它。

### 让NPC消失
要使NPC消失，你只需要设置npc.active为false。当然，你只想在某些条件下让你的NPC消失。最常见的是如果没有周围没有人战斗，让NPC消失。为此我们需要检查是否存在有效目标，如果不存在，我们就消失。

首先我们检查当前目标是否死亡或者是否有其他原因让我们想要消失
```cs
if(!player.active || player.dead){
     //如果我们当前瞄准的玩家死亡或不"活跃"（意味着玩家不再在玩）
     npc.TargetClosest(false);//尝试找到新目标
     player = Main.player[npc.target];//获取我们刚获得的新目标
          if(!player.active || player.dead) {
                //如果新的也死了，那么我们知道没有活跃玩家，可以消失
                npc.active = false;//将我们自己设置为不活跃，这会使我们不掉落战利品而直接消失
          }
}
```
//TODO: 基于其他条件的消失，比如生物群系（如果你读到这里，你可能只需要循环玩家数组然后用!player.desiredzone检查他们是否不在生物群系中）
弹射物消失会在TimeLeft用完时自动发生（你应该在setdefaults中设置）。如果你想手动删除它，调用`projectile.Kill();`
如果你不想让弹射物在一段时间后消失，在AI中将projectile.TimeLeft设置为大于1。因为AI每tick调用，它永远不会降到0，弹射物永远不会消失（然而这结合不与瓦片碰撞会导致一些奇怪的副作用，比如在地图上打破蜜蜂皇后蜂巢，所以请谨慎使用）。
### 动画

NPC和弹射物有不同的动画方式，这部分展示NPC。对于弹射物，改变projectile.Frame而不是使用查找帧钩子。

这两者都需要一个精灵图，按顺序分割成每一帧（NPC/弹射物可以呈现的方式）均匀堆叠在一起。

例如，查看示例模组精灵图中的flutter slime：

<img src="https://github.com/tModLoader/tModLoader/blob/1.3/ExampleMod/NPCs/FlutterSlime.png?raw=true" alt="FlutterSlime.png"/>

每一帧代表NPC可以呈现的一种方式，你的精灵图也应该看起来像这样。

我要用的精灵图看起来像这样：

//TODO 重做这个以明确每一帧的帧号 + Gif

![New Piskel (1)](https://user-images.githubusercontent.com/88946983/153733783-68d06f09-d436-428a-b0e6-ccee462e610b.png)

制作好我们的精灵图后，我们需要在SetStaticDefaults钩子中注册其中的帧数
```cs
public override void SetStaticDefaults() {
     // 其他内容
     Main.npcFrameCount[npc.type] = 4; // 我们想要4帧
}
```

当这发生时，游戏将我们的精灵图均匀地分割成我们指定的帧数（本例中为4）。

//TODO 添加游戏分离精灵图的图片

现在，我们需要改变帧以动画化我们的NPC。为此，我们需要重写`FindFrame(int frameHeight)`钩子，并改变要绘制的精灵图的哪一帧。

在FindFrame钩子中，你可能注意到了那个整数参数frameHeight。这是一帧的高度（如果你有一个5帧的精灵图高度为50像素，那就是10），应该乘以你想要的帧来得到设置npc.frame.Y的内容。

设置npc.frame.Y为`frameHeight * 3`将是第4帧，设置它为`0 * frameHeight`（或只是0）将是第一帧，因为那是我们想要绘制的帧的底部。

为了让代码更容易阅读，不要放帧号（如5），你可以分配描述每一帧作用的常量。
`npc.FrameY = frameHeight * FlyingFrame`比`npc.FrameY = frameHeight * 3`对任何阅读代码的人来说含义更清楚。

如果你的代码看起来像上面制作模式部分的例子，你可以像在AI中一样检查你的状态变量。实际上，我们可以使用一些非常类似于AI的逻辑来确定我们应该处于哪一帧。这段代码展示制作不同帧，假设你的代码看起来像上面的例子。
```cs
const int FrameLaunchingProjectiles = 0;//第一帧将在"发射弹射物"状态
const int FrameChaseOne = 1;//第二帧和第三帧将在追逐状态，我们将循环它们
const int FrameChaseTwo= 2;
const int CircleAttackFrame = 3;//第四帧将在圆形攻击中
public override void FindFrame(int frameHeight) {
     //在这里，我们改变npc.frame.Y来选择哪一帧
     if(state ==  ProjectileState){
          //如果我们在发射弹射物状态
          npc.frame.Y =  frameHeight * FrameLaunchingProjectiles;//设置要绘制哪一帧为第一帧
     }
     else if(state == ChaseState){
          //如果我们在追逐状态：
          //因为我们有两帧不同的想要循环，我们必须使用帧计时器
          npc.frameCounter++;//已经有为NPC准备好的帧计数器，所以我们使用它
          if(npc.frameCounter < 30){
               //如果已经过了不到30 tick
               npc.frame.Y = frameHeight * FrameChaseOne;//进入我们这个状态动画的第一帧
          }
          else if(npc.frameCounter <60){
               //如果已经过了超过30 tick且少于60
               npc.frame.Y = frameHeight * FrameChaseTwo;//进入第二帧
          }
          else{
              npc.frameCounter = 0;//重置计时器，重新开始动画
          }
     }
     else{
          npc.frame.Y = frameHeight * CircleAttackFrame;
    }
}
```
//TODO: 完成示例？
# 常见行为
### 生成NPC
生成NPC很像弹射物，但你使用NPC.NewNPC

它的参数是NewNPC(int X, int Y, int Type, int Start = 0, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f, int Target = 255)

请记住，所有带=的都是可选的，只有在你知道你在做什么时才需要放值。X和Y不言自明，Type就像NewProjectile中的一样——原版NPC是NPCID.Name，模组NPC是`Modcontent.NPCType<Class>()`。

Start几乎从未被使用。它是在数组中开始寻找放置NPC位置处的索引。如果设置为100，那么它的WhoAmI将是100或以上。这在实际上几乎没有用处，可以安全地保留为0。

ai0-ai3与弹射物中相同——它们的值被复制到NPC的AI数组中。

Target是在生成的NPC中npc.Target被设置为什么。
### 发射一波某物
你可以在AI中用for循环让代码执行多次。如果你想要让你的弹射物不准确或不都朝一个地方发射，阅读下面的"改变弹射物发射方向"部分，利用循环变量。
### 改变弹射物发射方向
要改变弹射物发射的方向，你需要影响`float SpeedX`和`float SpeedY`（假设你仍在使用上面的代码，这是ToPlayer X/Y）。Vector2上有许多方便的方法可以改变它的内容。

最有用的之一是`RotatedBy(double Radians)`或`RotatedByRandom`。这会旋转Vector2的内容，可用于向目标前方发射或制造散射/霰弹模式。请记住它接受弧度而不是度数，所以如果你有度数测量值，确保使用`MathHelper.ToRadians`。

示例用法——发射一圈东西
```cs
for(int i = 0; i < 360; i += 12){
     //循环直到i = 360，完成一次完整旋转。你可以通过改变12来改变旋转间隔
     Player target = Main.player[npc.target];
     Vector2 ToPlayer = npc.DirectionTo(target.Center).RotatedBy(MathHelper.ToRadians(i));//默认情况下，旋转使用弧度。我更喜欢使用度数，因此我转换它们为弧度
     if (Main.netMode != NetmodeID.MultiplayerClient)
           Projectile.NewProjectile(npc.Center.X, npc.Center.Y, ToPlayer.X * 5, ToPlayer.Y * 5, ProjectileID.WoodenArrowHostile, 50, 0f);//发射弹射物
}
```
### 如何像原版Boss一样生成我的NPC
生成你的Boss很简单，只需调用`NPC.SpawnOnPlayer(player.WhoAmI, ModContent.NPCType<Class>());`，player是要生成在的人。这甚至处理"Boss已苏醒"聊天消息。
### 如何让我的NPC/弹射物施加Debuff
为此我们需要重写`OnHitPlayer(Player player, int damage, bool crit)`用于NPC或`ModifyHitPlayer(Player target, ref int damage, ref bool crit)`用于弹射物，并调用player(NPC)或target(弹射物).AddBuff(int type, int duration)
请记住duration以tick为单位。60等于1秒
```cs
public override void OnHitPlayer(Player player, int damage, bool crit){
      //注意这在弹射物中是一个不同名称的钩子
      player.AddBuff(BuffID.Cursed, 240);//240 = 4秒
      //你可以通过使用buff ID来改变buff。模组buff应该使用ModContent.BuffType<Class>()
}
```
### 如何让某物在聊天中说话
要在聊天中打印消息，放置Main.NewText("Text", new Color(r,g,b))。R、G和B是消息颜色0-255的数字。

### 如何让我的NPC掉落物品
阅读[掉落指南](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC-Drops-and-Loot)
### 如何制作蠕虫Boss
蠕虫Boss需要比本指南覆盖的更多代码。示例模组展示了一个蠕虫代码的例子：
[1.4](https://github.com/tModLoader/tModLoader/blob/dd44e70738e29e5ded0cb979355b671b41f56efe/ExampleMod/Content/NPCs/Worm.cs)
[1.3]( https://github.com/tModLoader/tModLoader/blob/1.3/ExampleMod/NPCs/Worm.cs)
# 常见错误
记住当你试图找到如何修复错误时，谷歌是你的朋友。
### 找不到某物的类型或命名空间
请记住c#是区分大小写的——Projectile与projectile不同。
如果"某物"不是来自你的模组
这可能意味着你忘记了一个using。如果你在VS中，它应该自动推荐它们。如果你不在，尝试看看它是否在示例模组中，然后添加它有的using。添加
```cs
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;
using System;
```
应该覆盖大部分。如果你找不到且确定你没有拼写错误任何东西，请确保你没有拼写错误任何东西。
如果"某物"来自你的模组，确保它拼写正确。如果是，去它的文件确保你使用了它的命名空间。

### 无法隐式转换类型'float'到'int'。存在显式转换（你是否缺少强制转换？）
这意味着你尝试将float（可以保存小数的变量）用于需要int（只能保存整数的变量）的地方。你可以通过在前面添加(int)来"强制转换"它很容易地解决这个问题，但请记住这会导致小数点后的所有内容被删除。你可以用这个获得四舍五入的数字
`(int)Math.Round(MyFloat);`
