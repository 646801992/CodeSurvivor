# Code Survivor

一个基于Pygame的简易吃鸡游戏对战平台，需要参赛用户自己编写AI控制玩家行为。

## 使用方法

参赛用户根据DemoAgent.py文件来修改其中Agent类，实现玩家AI。

将同一场比赛Agent文件都放在demo_group文件夹里，确保每个文件里都有Agent这个类。

运行CodeSurvivorSever.py文件。

出现画面后按空格即开始比赛。

## 游戏规则

与吃鸡游戏类似，玩家需要在场上尽可能存活，剩下最后一个玩家时该玩家获胜。

每隔一段时间安全圈会收缩，通过画面可以看到效果。

#### 地图地形：

- 草地，可以走，地图中用0表示
- 岩石，不可以走，地图中用1表示
- 树林，可以走，地图中用2表示
- 水源，可以走，但会扣HP，地图中用3表示

#### 每个参赛玩家的情况：

- 每个玩家的出生点随机
- 每个玩家有三个数值：HP，饥饿值，口渴值
- 玩家初始HP为100，初始饥饿值和口渴值都为20
- 每个单位时间消耗1点食物和水分，即饥饿值和口渴值都减1
- 饥饿值或口渴值将为0时每个单位时间消耗1点HP
- 在水边每个单位时间增加5点口渴值
- 在森林里每个单位时间增加5点饥饿值
- 在水里每个单位时间减少1点HP
- 在毒圈里每个单位时间减少2点HP

## Agent编写规则

#### get_info()函数

**系统通过调用get_info()函数来给你更新当前场上信息**，get_info()函数的输入参数info_dict为一个字典。

info_dict中的字段及对应数据如下：

- pos: 一个元组储存你当前的位置(x, y)
- ground_map: 一个numpy数组储存地形。0：草地，1：岩石，2：树林，3：水源。
- safe_mask: 当前安全的区域，有毒区域标记为0，无毒区域标记为1。
- next_safe_mask: 下一次缩圈后的安全区域，有毒区域标记为0，无毒区域标记为1。
- tick: 游戏开始以来的tick数。
- count_down: 还有几次tick后就开始缩圈，当tick为0时表示正在缩圈。
- players_pos: 一个列表储存所有玩家的位置，形如[(1,2), (3,4), (5,6)]。每个玩家在列表中的编号位置不变。
- hp: 玩家当前血量。
- hunger: 玩家当前饥饿值，100为最不饥饿。
- thirst: 玩家当前口渴值，100位最不口渴。

#### tack_action()函数

系统通过调用tack_action()函数来获取你要采取的行动，目前支持两种行动：移动，射击。

你需要在**0.1秒内**返回格式为('move', (x, y))或('shoot', (x, y))的数据。

**关于移动**

- 当你选择移动时，仅可移动到自己周围八个格子内，如果不想动就将移动位置设为你当前的坐标。

**关于射击**

- 射击距离超过一半地图宽度时100%射不中。
- 射击和自己在同一个格子里的玩家时100%射中。
- 射击距离在上述两种情况的中间则按照概率计算是否射中（线性），越近则射中的概率越高。
- 越近距离射中敌人则扣的HP越多，一次射击最多扣10HP，最少扣1HP。