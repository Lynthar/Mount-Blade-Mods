# Captivity Events (CE) Mod 二次开发技术指南

> **面向对象**：非计算机专业背景的游戏Mod开发者
> **适用版本**：CaptivityEvents v1.3.11.1311
> **游戏版本**：Mount & Blade II: Bannerlord

---

## 目录

1. [概述与核心概念](#1-概述与核心概念)
2. [目录结构详解](#2-目录结构详解)
3. [快速入门：创建你的第一个扩展包](#3-快速入门创建你的第一个扩展包)
4. [事件(Event)系统详解](#4-事件event系统详解)
5. [标志(Flags)完整参考](#5-标志flags完整参考)
6. [后果(Consequences)完整参考](#6-后果consequences完整参考)
7. [技能与特质系统](#7-技能与特质系统)
8. [高级功能](#8-高级功能)
9. [本地化与多语言支持](#9-本地化与多语言支持)
10. [调试与测试](#10-调试与测试)
11. [常见问题解答](#11-常见问题解答)
12. [速查表](#12-速查表)

---

## 1. 概述与核心概念

### 1.1 什么是 Captivity Events？

Captivity Events（简称CE）是一个为《骑马与砍杀2：霸主》设计的事件系统框架。它允许mod作者通过编写XML文件来创建各种随机事件，而**无需编写任何C#代码**。

### 1.2 核心概念

| 概念 | 说明 |
|------|------|
| **事件(Event)** | 游戏中发生的一个互动场景，包含描述文本、图片、选项等 |
| **标志(Flag)** | 事件触发的条件，如"玩家是女性"、"在城市中"等 |
| **后果(Consequence)** | 选项产生的效果，如"获得金币"、"逃跑"等 |
| **选项(Option)** | 玩家可选择的行动，每个选项可有不同的后果 |
| **扩展包(Expansion)** | 你创建的内容包，包含新事件、图片等 |

### 1.3 三种事件类型

```
┌─────────────────────────────────────────────────────────────┐
│                        事件类型                              │
├──────────────┬──────────────────┬───────────────────────────┤
│   Random     │     Captive      │         Captor            │
│   随机事件    │     被俘事件      │        俘虏者事件          │
├──────────────┼──────────────────┼───────────────────────────┤
│ 玩家正常行军  │  玩家被敌人俘虏   │     玩家有俘虏时触发        │
│ 时随机触发    │  时触发           │                           │
└──────────────┴──────────────────┴───────────────────────────┘
```

### 1.4 模块加载顺序

CE使用 `z` 前缀确保正确的加载顺序：

```
Native (游戏核心) → SandBox → ... → zCaptivityEvents (CE核心) → zCEDefaults (默认事件) → zCE你的扩展包
```

**重要**：你的扩展包ID必须以 `zCE` 开头！

---

## 2. 目录结构详解

### 2.1 整体项目结构

```
CaptivityEvents/
│
├── CaptivityEventsV1.3.11.1311/     # ★ 核心模块（不要修改）
│   └── Modules/zCaptivityEvents/
│       ├── bin/                      # DLL文件
│       ├── GUI/                      # 用户界面
│       ├── ModuleData/Languages/     # 核心翻译文件
│       ├── ModuleLoader/             # 事件加载器
│       │   └── CaptivityRequired/
│       │       ├── Events/
│       │       │   ├── CEEventsModal.xsd    # ★ 事件XML结构定义
│       │       │   └── CECustomModal.xsd    # ★ 自定义模块结构定义
│       │       └── Images/           # 核心图片
│       └── SubModule.xml             # 模块定义
│
├── CEDefaultEvents1311/              # 默认事件包（可参考学习）
│   └── Modules/zCEDefaults/
│       ├── Events/                   # 30个事件XML文件
│       └── Images/                   # 500+事件图片
│
├── ExampleExpansion/                 # ★ 示例扩展包（入门模板）
│   └── Modules/zCEExampleExpansion/
│       ├── Events/
│       │   └── ExampleEvents.xml     # 带详细中文注释的示例
│       ├── Settings/
│       │   └── CEModuleCustom.xml    # 模块设置
│       └── SubModule.xml             # 模块定义
│
└── Tools/
    └── CEEventEditor.html            # ★ 可视化事件编辑器
```

### 2.2 扩展包必需文件

创建扩展包至少需要以下文件：

```
你的扩展包/
└── Modules/zCE你的名称/
    ├── SubModule.xml           # 【必需】模块定义文件
    ├── Settings/
    │   └── CEModuleCustom.xml  # 【必需】CE模块配置
    ├── Events/
    │   └── 你的事件.xml         # 【必需】事件定义文件
    └── Images/                  # 【可选】自定义图片
        └── 你的图片.png
```

---

## 3. 快速入门：创建你的第一个扩展包

### 3.1 第一步：复制模板

复制 `ExampleExpansion` 文件夹，重命名为你的扩展包名称。

### 3.2 第二步：修改 SubModule.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<Module>
  <!-- 显示在游戏启动器中的名称 -->
  <Name value="我的CE扩展包" />

  <!-- 模块唯一ID，必须以zCE开头 -->
  <Id value="zCEMyExpansion" />

  <!-- 版本号 -->
  <Version value="v1.0.0" />

  <ModuleType value="Community" />
  <ModuleCategory value="Singleplayer" />

  <!-- 依赖CE核心模块 -->
  <DependedModules>
    <DependedModule Id="zCaptivityEvents" DependentVersion="v1.3.11.1311" />
  </DependedModules>

  <!-- 纯数据驱动，无需DLL -->
  <SubModules></SubModules>
</Module>
```

### 3.3 第三步：修改 CEModuleCustom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CECustom xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="../../zCaptivityEvents/ModuleLoader/CaptivityRequired/Events/CECustomModal.xsd">

    <!-- 在CE设置中显示的名称 -->
    <CEModuleName>我的扩展包</CEModuleName>

    <!-- 可选：定义自定义技能 -->
    <CESkills>
        <CESkill Id="MyCustomSkill" Name="自定义技能" MinLevel="0" MaxLevel="100"/>
    </CESkills>

    <!-- 可选：定义自定义标志 -->
    <CEFlags>
        <CEFlag Id="MyCustomFlag" Name="自定义标志" DefaultValue="false"/>
    </CEFlags>
</CECustom>
```

### 3.4 第四步：创建你的第一个事件

在 `Events/` 文件夹中创建 `MyEvents.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CEEvents xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="../../zCaptivityEvents/ModuleLoader/CaptivityRequired/Events/CEEventsModal.xsd">

    <!-- 一个简单的随机事件 -->
    <CEEvent>
        <!-- 事件唯一ID -->
        <Name>MY_random_first_event</Name>

        <!-- 事件描述文本 -->
        <Text>你在路边发现了一个神秘的箱子。你要打开它吗？</Text>

        <!-- 背景图片（不含扩展名） -->
        <BackgroundName>default_random</BackgroundName>

        <!-- 触发条件 -->
        <MultipleRestrictedListOfFlags>
            <RestrictedListOfFlags>Random</RestrictedListOfFlags>
            <RestrictedListOfFlags>LocationLand</RestrictedListOfFlags>
            <RestrictedListOfFlags>LocationTravellingParty</RestrictedListOfFlags>
        </MultipleRestrictedListOfFlags>

        <!-- 玩家选项 -->
        <Options>
            <!-- 选项1：打开箱子 -->
            <Option>
                <Order>0</Order>
                <MultipleRestrictedListOfConsequences>
                    <RestrictedListOfConsequences>GiveGold</RestrictedListOfConsequences>
                    <RestrictedListOfConsequences>Leave</RestrictedListOfConsequences>
                </MultipleRestrictedListOfConsequences>
                <OptionText>打开箱子</OptionText>
                <GoldTotal>R 50 200</GoldTotal>  <!-- 随机获得50-200金币 -->
            </Option>

            <!-- 选项2：离开 -->
            <Option>
                <Order>1</Order>
                <MultipleRestrictedListOfConsequences>
                    <RestrictedListOfConsequences>Leave</RestrictedListOfConsequences>
                </MultipleRestrictedListOfConsequences>
                <OptionText>小心驶得万年船，离开这里</OptionText>
            </Option>
        </Options>

        <!-- 触发权重（越高越容易触发） -->
        <WeightedChanceOfOccurring>20</WeightedChanceOfOccurring>

        <!-- 最低年龄要求 -->
        <ReqHeroMinAge>18</ReqHeroMinAge>
    </CEEvent>

</CEEvents>
```

### 3.5 第五步：安装测试

1. 将整个扩展包文件夹复制到游戏的 `Modules` 目录
2. 在游戏启动器中启用你的模块
3. 进入游戏测试

---

## 4. 事件(Event)系统详解

### 4.1 事件基本结构

```xml
<CEEvent>
    ├── Name              【必需】事件唯一标识符
    ├── Text              【必需】事件描述文本
    ├── BackgroundName    【可选】单一背景图片
    ├── Backgrounds       【可选】条件背景图片组
    ├── NotificationName  【可选】通知图标
    ├── SoundName         【可选】音效文件
    │
    ├── MultipleRestrictedListOfFlags   【必需】触发条件标志
    │
    ├── Options           【必需】玩家选项列表
    │   └── Option
    │       ├── Order                            选项排序
    │       ├── OptionText                       选项文本
    │       ├── MultipleRestrictedListOfConsequences  后果列表
    │       ├── TriggerEventName                 触发后续事件
    │       └── [各种效果参数]
    │
    ├── WeightedChanceOfOccurring  【推荐】触发权重
    │
    └── [各种触发条件参数]
</CEEvent>
```

### 4.2 事件命名规范

建议使用以下命名格式：

```
[你的前缀]_[事件类型]_[描述]_[编号]

示例：
MY_random_merchant_001      # 随机商人事件
MY_captive_escape_001       # 被俘逃跑事件
MY_captor_prisoner_001      # 俘虏者事件
```

### 4.3 文本中的特殊语法

#### 本地化字符串ID

```xml
<Text>{=StringID}这是事件描述文本</Text>
```
- `{=StringID}` 用于多语言本地化
- 如果不需要多语言，可以直接写文本

#### 条件文本

```xml
<Text>
  {?ISCAPTORFEMALE}她{?}他{\?}看着你说道...
</Text>
```

| 条件标记 | 说明 |
|---------|------|
| `{?ISCAPTORFEMALE}...{?}...{\?}` | 俘虏者是否为女性 |
| `{?ISHEROPREGANANT}...{?}...{\?}` | 玩家是否怀孕 |
| `{?CONDITION}真{?}假{\?}` | 通用条件格式 |

### 4.4 背景图片设置

#### 简单背景

```xml
<BackgroundName>my_image</BackgroundName>
```
图片文件：`Images/my_image.png`

#### 条件背景（根据条件显示不同图片）

```xml
<Backgrounds>
    <Background Name="prison_female" Weight="1" UseConditions="condition_female"/>
    <Background Name="prison_male" Weight="1" UseConditions="condition_male"/>
    <Background Name="prison_default" Weight="1"/>
</Backgrounds>
```

#### 动画背景（帧序列）

```xml
<BackgroundAnimation>
    <BackgroundName>frame_001</BackgroundName>
    <BackgroundName>frame_002</BackgroundName>
    <BackgroundName>frame_003</BackgroundName>
</BackgroundAnimation>
<BackgroundAnimationSpeed>0.5</BackgroundAnimationSpeed>
```

### 4.5 事件链（触发后续事件）

```xml
<!-- 主事件 -->
<CEEvent>
    <Name>MY_event_main</Name>
    <Options>
        <Option>
            <Order>0</Order>
            <MultipleRestrictedListOfConsequences>
                <RestrictedListOfConsequences>Continue</RestrictedListOfConsequences>
            </MultipleRestrictedListOfConsequences>
            <OptionText>继续</OptionText>
            <!-- 触发单一后续事件 -->
            <TriggerEventName>MY_event_result</TriggerEventName>
        </Option>
        <Option>
            <Order>1</Order>
            <MultipleRestrictedListOfConsequences>
                <RestrictedListOfConsequences>Continue</RestrictedListOfConsequences>
            </MultipleRestrictedListOfConsequences>
            <OptionText>赌一把</OptionText>
            <!-- 随机触发多个后续事件 -->
            <TriggerEvents>
                <TriggerEvent>
                    <EventName>MY_event_win</EventName>
                    <EventWeight>3</EventWeight>  <!-- 权重3 -->
                </TriggerEvent>
                <TriggerEvent>
                    <EventName>MY_event_lose</EventName>
                    <EventWeight>1</EventWeight>  <!-- 权重1 -->
                </TriggerEvent>
            </TriggerEvents>
        </Option>
    </Options>
</CEEvent>

<!-- 后续事件（只能被触发，不会随机出现） -->
<CEEvent>
    <Name>MY_event_result</Name>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>CanOnlyBeTriggeredByOtherEvent</RestrictedListOfFlags>
        <RestrictedListOfFlags>Random</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>
    ...
</CEEvent>
```

---

## 5. 标志(Flags)完整参考

### 5.1 事件类型标志（必须选一个）

| 标志 | 说明 |
|------|------|
| `Random` | 随机事件（玩家未被俘、无需俘虏） |
| `Captive` | 被俘事件（玩家被敌人俘虏时） |
| `Captor` | 俘虏者事件（玩家有俘虏时） |

### 5.2 特殊控制标志

| 标志 | 说明 |
|------|------|
| `CanOnlyBeTriggeredByOtherEvent` | 只能被其他事件触发，不会随机出现 |
| `Overwritable` | 允许被覆写（仅用于默认事件） |
| `IgnoreAllOther` | 如果此事件有效则忽略其他所有事件 |
| `ProgressMenu` | 进度菜单事件（需要等待时间） |
| `WaitingMenu` | 等待菜单事件 |

### 5.3 位置标志

| 标志 | 说明 |
|------|------|
| `LocationLand` | 在陆地上 |
| `LocationSea` | 在海上 |
| `LocationTravellingParty` | 行军途中 |
| `LocationPartyInTown` | 队伍在城镇中 |
| `LocationPartyInCastle` | 队伍在城堡中 |
| `LocationPartyInVillage` | 队伍在村庄中 |
| `LocationDungeon` | 在地牢中 |
| `LocationVillage` | 在村庄场景中 |
| `LocationCity` | 在城市场景中 |
| `LocationCastle` | 在城堡场景中 |
| `LocationHideout` | 在土匪藏身处 |
| `LocationTavern` | 在酒馆中 |

### 5.4 时间与季节标志

| 标志 | 说明 |
|------|------|
| `TimeNight` | 夜间 |
| `TimeDay` | 白天 |
| `SeasonWinter` | 冬季 |
| `SeasonSpring` | 春季 |
| `SeasonSummer` | 夏季 |
| `SeasonFall` | 秋季 |

### 5.5 玩家(Hero)状态标志

| 标志 | 说明 |
|------|------|
| `HeroGenderIsFemale` | 玩家是女性 |
| `HeroGenderIsMale` | 玩家是男性 |
| `HeroHaveOffspring` | 玩家有子女 |
| `HeroNotHaveOffspring` | 玩家没有子女 |
| `HeroHaveSpouse` | 玩家已婚 |
| `HeroNotHaveSpouse` | 玩家未婚 |
| `HeroIsPregnant` | 玩家怀孕中 |
| `HeroIsNotPregnant` | 玩家未怀孕 |
| `HeroOwnsFief` | 玩家拥有封地 |
| `HeroOwnsNoFief` | 玩家没有封地 |
| `HeroIsClanLeader` | 玩家是家族领袖 |
| `HeroIsFactionLeader` | 玩家是派系领袖 |
| `HeroIsProstitute` | 玩家是妓女状态 |
| `HeroIsSlave` | 玩家是奴隶状态 |

### 5.6 俘虏者(Captor)状态标志

| 标志 | 说明 |
|------|------|
| `CaptorIsHero` | 俘虏者是英雄 |
| `CaptorIsNonHero` | 俘虏者不是英雄 |
| `CaptorGenderIsFemale` | 俘虏者是女性 |
| `CaptorGenderIsMale` | 俘虏者是男性 |
| `CaptorHaveSpouse` | 俘虏者已婚 |
| `CaptorOwnsCurrentSettlement` | 俘虏者拥有当前定居点 |

### 5.7 队伍类型标志

| 标志 | 说明 |
|------|------|
| `CaravanParty` | 商队 |
| `BanditParty` | 土匪队伍 |
| `LordParty` | 领主队伍 |
| `DefaultParty` | 默认队伍 |

### 5.8 特殊事件标志

| 标志 | 说明 |
|------|------|
| `DuringSiege` | 围城期间 |
| `DuringRaid` | 劫掠期间 |
| `VisitedByCaravan` | 被商队访问 |
| `VisitedByLord` | 被领主访问 |
| `PlayerIsNotBusy` | 玩家不忙碌 |
| `CaptivesOutNumber` | 俘虏人数超过 |

### 5.9 替代事件标志

| 标志 | 说明 |
|------|------|
| `DeathAlternative` | 死亡替代事件 |
| `DesertionAlternative` | 逃兵替代事件 |
| `MarriageAlternative` | 婚姻替代事件 |
| `BirthAlternative` | 生育替代事件 |

---

## 6. 后果(Consequences)完整参考

### 6.1 菜单控制后果

| 后果 | 说明 |
|------|------|
| `Leave` | 离开事件（关闭菜单） |
| `Continue` | 继续（配合触发后续事件） |
| `Wait` | 等待（配合进度事件） |
| `EmptyIcon` | 空图标 |
| `Submenu` | 子菜单 |

### 6.2 金币相关后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `GiveGold` | `GoldTotal` | 给予金币（正数） |
| `ChangeGold` | `GoldTotal` | 改变金币（可正可负） |
| `GiveCaptorGold` | `CaptorGoldTotal` | 给予俘虏者金币 |
| `ChangeCaptorGold` | `CaptorGoldTotal` | 改变俘虏者金币 |

**GoldTotal 语法**：
```xml
<GoldTotal>100</GoldTotal>        <!-- 固定100金币 -->
<GoldTotal>-50</GoldTotal>        <!-- 扣除50金币 -->
<GoldTotal>R 100 500</GoldTotal>  <!-- 随机100-500金币 -->
<GoldTotal>R -200 -100</GoldTotal> <!-- 随机扣除100-200金币 -->
```

### 6.3 属性变化后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `ChangeMorale` | `MoraleTotal` | 改变士气 |
| `ChangeRenown` | `RenownTotal` | 改变声望 |
| `ChangeHealth` | `HealthTotal` | 改变生命值 |
| `ChangeRelation` | `RelationTotal` | 改变关系 |
| `ChangeProstitutionLevel` | `ProstitutionTotal` | 改变卖淫等级 |
| `ChangeSlaveryLevel` | `SlaveryTotal` | 改变奴隶等级 |

### 6.4 逃跑相关后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `AttemptEscape` | `EscapeChance` | 尝试逃跑（有成功率） |
| `Escape` | - | 直接逃跑成功 |
| `BribeAndEscape` | - | 贿赂后逃跑 |

```xml
<Option>
    <MultipleRestrictedListOfConsequences>
        <RestrictedListOfConsequences>AttemptEscape</RestrictedListOfConsequences>
    </MultipleRestrictedListOfConsequences>
    <OptionText>尝试逃跑</OptionText>
    <EscapeChance>50</EscapeChance>  <!-- 50%成功率 -->
</Option>
```

### 6.5 俘虏相关后果

| 后果 | 说明 |
|------|------|
| `GainRandomPrisoners` | 获得随机俘虏 |
| `ReleaseRandomPrisoners` | 释放随机俘虏 |
| `ReleaseAllPrisoners` | 释放所有俘虏 |
| `KillRandomPrisoners` | 杀死随机俘虏 |
| `KillAllPrisoners` | 杀死所有俘虏 |
| `WoundPrisoner` | 打伤俘虏 |
| `JoinCaptor` | 俘虏加入玩家队伍 |
| `MakeHeroCompanion` | 使英雄成为同伴 |

### 6.6 战斗相关后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `StartBattle` | `BattleSettings` | 开始战斗 |
| `RebelPrisoners` | - | 俘虏叛乱 |
| `HuntPrisoners` | - | 追捕俘虏 |

**战斗设置示例**：
```xml
<Option>
    <MultipleRestrictedListOfConsequences>
        <RestrictedListOfConsequences>StartBattle</RestrictedListOfConsequences>
    </MultipleRestrictedListOfConsequences>
    <OptionText>战斗！</OptionText>
    <BattleSettings
        Ref="enemy"
        Victory="MY_battle_win"
        Defeat="MY_battle_lose"
        EnemyName="强盗"
        PlayerTroops="20">
        <SpawnTroops>
            <SpawnTroop Id="cs_mountain_bandits_troop" Ref="enemy" Number="10"/>
            <SpawnTroop Id="cs_looter" Ref="enemy" Number="5"/>
        </SpawnTroops>
    </BattleSettings>
</Option>
```

### 6.7 怀孕相关后果

| 后果 | 说明 |
|------|------|
| `ImpregnationRisk` | 随机怀孕风险 |
| `ImpregnationHero` | 被俘虏者领袖怀孕 |
| `ImpregnationByPlayer` | 被玩家怀孕 |
| `GiveBirth` | 生育 |
| `Abort` | 流产 |

### 6.8 传送相关后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `TeleportPlayer` | `TeleportSettings` | 传送玩家 |
| `SoldToSettlement` | - | 卖到定居点 |
| `SoldToCaravan` | - | 卖到商队 |
| `SoldToLordParty` | - | 卖给领主 |
| `CapturePlayer` | - | 玩家被俘 |

**传送设置示例**：
```xml
<TeleportSettings
    Location="Town"           <!-- Town/Village/Castle/Hideout -->
    Distance="Nearest"        <!-- Nearest/Random -->
    Faction="Enemy"/>         <!-- Any/Enemy/Friendly/Neutral -->
```

### 6.9 物品相关后果

| 后果 | 配合参数 | 说明 |
|------|---------|------|
| `GiveItem` | `ItemToGive` | 给予物品 |
| `Strip` | `StripSettings` | 脱去装备 |
| `StripPlayer` | `StripSettings` | 脱去玩家装备 |

---

## 7. 技能与特质系统

### 7.1 技能(Skills)列表

#### 战斗技能
| 技能ID | 名称 | 属性 |
|--------|------|------|
| `OneHanded` | 单手武器 | 活力 |
| `TwoHanded` | 双手武器 | 活力 |
| `Polearm` | 长杆武器 | 活力 |
| `Bow` | 弓箭 | 控制 |
| `Crossbow` | 弩 | 控制 |
| `Throwing` | 投掷 | 控制 |

#### 移动技能
| 技能ID | 名称 | 属性 |
|--------|------|------|
| `Riding` | 骑术 | 耐力 |
| `Athletics` | 运动 | 耐力 |

#### 侦察技能
| 技能ID | 名称 | 属性 |
|--------|------|------|
| `Scouting` | 侦察 | 狡诈 |
| `Tactics` | 战术 | 狡诈 |
| `Roguery` | 盗贼 | 狡诈 |

#### 社交技能
| 技能ID | 名称 | 属性 |
|--------|------|------|
| `Charm` | 魅力 | 社交 |
| `Leadership` | 领导力 | 社交 |
| `Trade` | 交易 | 社交 |

#### 智力技能
| 技能ID | 名称 | 属性 |
|--------|------|------|
| `Steward` | 管理 | 智力 |
| `Medicine` | 医术 | 智力 |
| `Engineering` | 工程 | 智力 |
| `Smithing` | 锻造 | 耐力 |

### 7.2 特质(Traits)列表

#### 性格特质（范围：-2 到 +2）
| 特质ID | 名称 | 说明 |
|--------|------|------|
| `Mercy` | 仁慈 | 对苦难的厌恶，帮助陌生人或敌人的意愿 |
| `Valor` | 勇气 | 冒险赢得荣耀或财富的声誉 |
| `Honor` | 荣誉 | 尊重承诺和遵守法律的声誉 |
| `Generosity` | 慷慨 | 对亲属和下属的忠诚 |
| `Calculating` | 计算 | 为长期利益控制情绪的能力 |

### 7.3 在事件中使用技能

#### 技能检定（选项条件）

```xml
<Option>
    <OptionText>[魅力 50+] 说服他们放你走</OptionText>
    <SkillsRequired>
        <SkillRequired Id="Charm" Min="50" Ref="Hero"/>
    </SkillsRequired>
    ...
</Option>
```

#### 提升技能（选项效果）

```xml
<Option>
    <OptionText>成功完成任务</OptionText>
    <SkillsToLevel>
        <Skill Id="Roguery" ByXP="100" Ref="Hero" Color="Green"/>
        <Skill Id="Charm" ByXP="R 50 100" Ref="Hero"/>  <!-- 随机50-100经验 -->
    </SkillsToLevel>
    ...
</Option>
```

**Ref 参数说明**：
| 值 | 说明 |
|-----|------|
| `Hero` | 玩家（在Random事件中）或被俘者（在Captive/Captor事件中） |
| `Captive` | 被俘者 |
| `Captor` | 俘虏者 |

**Color 参数说明**：
| 颜色 | 用途 |
|------|------|
| `Green` | 正面效果 |
| `Red` | 负面效果 |
| `Yellow` | 中性效果 |
| `Gray` | 默认 |

### 7.4 在事件中使用特质

#### 特质检定

```xml
<Option>
    <OptionText>[仁慈] 放他们走</OptionText>
    <TraitsRequired>
        <TraitRequired Id="Mercy" Min="1" Ref="Hero"/>
    </TraitsRequired>
    ...
</Option>
```

#### 改变特质

```xml
<Option>
    <OptionText>残忍地对待他</OptionText>
    <TraitsToLevel>
        <Trait Id="Mercy" ByLevel="-1" Ref="Hero" Color="Red"/>
    </TraitsToLevel>
    ...
</Option>
```

---

## 8. 高级功能

### 8.1 进度事件（需要等待时间）

进度事件用于模拟需要时间完成的活动：

```xml
<!-- 入口事件 -->
<CEEvent>
    <Name>MY_rest_start</Name>
    <Text>你决定在这里休息一会儿。</Text>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>Random</RestrictedListOfFlags>
        <RestrictedListOfFlags>PlayerIsNotBusy</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>
    <Options>
        <Option>
            <Order>0</Order>
            <MultipleRestrictedListOfConsequences>
                <RestrictedListOfConsequences>Wait</RestrictedListOfConsequences>
            </MultipleRestrictedListOfConsequences>
            <OptionText>开始休息</OptionText>
            <TriggerEventName>MY_rest_progress</TriggerEventName>
        </Option>
    </Options>
</CEEvent>

<!-- 进度事件 -->
<CEEvent>
    <Name>MY_rest_progress</Name>
    <Text>正在休息中...</Text>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>CanOnlyBeTriggeredByOtherEvent</RestrictedListOfFlags>
        <RestrictedListOfFlags>Random</RestrictedListOfFlags>
        <RestrictedListOfFlags>ProgressMenu</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>

    <!-- 进度设置 -->
    <ProgressEvent>
        <ShouldStopMoving>true</ShouldStopMoving>  <!-- 队伍停止移动 -->
        <DisplayProgressMode>0</DisplayProgressMode>  <!-- 0=只显示进度条 -->
        <TimeToTake>4</TimeToTake>  <!-- 需要4小时 -->
        <TriggerEventName>MY_rest_complete</TriggerEventName>
    </ProgressEvent>

    <Options>
        <Option>
            <Order>0</Order>
            <MultipleRestrictedListOfConsequences>
                <RestrictedListOfConsequences>Continue</RestrictedListOfConsequences>
            </MultipleRestrictedListOfConsequences>
            <OptionText>继续休息</OptionText>
        </Option>
    </Options>
</CEEvent>

<!-- 完成事件 -->
<CEEvent>
    <Name>MY_rest_complete</Name>
    <Text>休息完毕，你感觉精力充沛！</Text>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>CanOnlyBeTriggeredByOtherEvent</RestrictedListOfFlags>
        <RestrictedListOfFlags>Random</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>
    <Options>
        <Option>
            <Order>0</Order>
            <MultipleRestrictedListOfConsequences>
                <RestrictedListOfConsequences>ChangeHealth</RestrictedListOfConsequences>
                <RestrictedListOfConsequences>Leave</RestrictedListOfConsequences>
            </MultipleRestrictedListOfConsequences>
            <OptionText>继续旅程</OptionText>
            <HealthTotal>30</HealthTotal>
        </Option>
    </Options>
</CEEvent>
```

### 8.2 延迟事件

延迟事件在一定时间后自动触发：

```xml
<Option>
    <OptionText>种下种子</OptionText>
    <DelayEvent>
        <TimeToTake>72</TimeToTake>  <!-- 72小时后触发 -->
        <TriggerEventName>MY_harvest_event</TriggerEventName>
    </DelayEvent>
</Option>
```

### 8.3 条件事件（用于背景判断）

条件事件本身不会触发，只用于其他事件的条件检查：

```xml
<!-- 条件事件：检查玩家是否是女性 -->
<CEEvent>
    <Name>CE_condition_female</Name>
    <Text>条件检查用</Text>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>CanOnlyBeTriggeredByOtherEvent</RestrictedListOfFlags>
        <RestrictedListOfFlags>HeroGenderIsFemale</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>
</CEEvent>

<!-- 条件事件：检查玩家是否是男性 -->
<CEEvent>
    <Name>CE_condition_male</Name>
    <Text>条件检查用</Text>
    <MultipleRestrictedListOfFlags>
        <RestrictedListOfFlags>CanOnlyBeTriggeredByOtherEvent</RestrictedListOfFlags>
        <RestrictedListOfFlags>HeroGenderIsMale</RestrictedListOfFlags>
    </MultipleRestrictedListOfFlags>
</CEEvent>

<!-- 使用条件背景的事件 -->
<CEEvent>
    <Name>MY_event_with_conditional_bg</Name>
    <Text>...</Text>
    <Backgrounds>
        <Background Name="female_bg" Weight="1" UseConditions="CE_condition_female"/>
        <Background Name="male_bg" Weight="1" UseConditions="CE_condition_male"/>
    </Backgrounds>
    ...
</CEEvent>
```

### 8.4 地形条件

限制事件只在特定地形触发：

```xml
<CEEvent>
    <Name>MY_desert_event</Name>
    ...
    <TerrainTypesRequirements>
        <TerrainTypes>
            <TerrainType>Desert</TerrainType>
            <TerrainType>Dune</TerrainType>
        </TerrainTypes>
    </TerrainTypesRequirements>
</CEEvent>
```

**可用地形类型**：
`Plain`, `Desert`, `Snow`, `Forest`, `Steppe`, `Mountain`, `Lake`, `Water`, `River`, `Canyon`, `Swamp`, `Dune`, `Beach`, `Cliff`, `CoastalSea`, `OpenSea`

### 8.5 王国与家族操作

```xml
<Option>
    <OptionText>加入他的王国</OptionText>
    <KingdomOptions>
        <KingdomOption Ref="Hero" Action="Join" Kingdom="Captor"/>
    </KingdomOptions>
</Option>

<Option>
    <OptionText>加入他的家族</OptionText>
    <ClanOptions>
        <ClanOption Ref="Hero" Action="Join" Clan="Captor"/>
    </ClanOptions>
</Option>
```

### 8.6 生成士兵

```xml
<Option>
    <OptionText>招募士兵</OptionText>
    <SpawnTroops>
        <SpawnTroop Id="imperial_infantry" Ref="friend" Number="5"/>
        <SpawnTroop Id="imperial_archer" Ref="friend" Number="3"/>
    </SpawnTroops>
</Option>
```

**Ref 参数**：
| 值 | 说明 |
|-----|------|
| `friend` | 加入玩家队伍 |
| `enemy` | 作为敌人 |
| `temporary` | 临时参战 |

---

## 9. 本地化与多语言支持

### 9.1 字符串ID格式

在事件文本中使用字符串ID：

```xml
<Text>{=MY_EVENT_001}这是事件描述文本。</Text>
<OptionText>{=MY_OPTION_001}这是选项文本。</OptionText>
```

### 9.2 翻译文件结构

```
你的扩展包/
└── Modules/zCE你的名称/
    └── ModuleData/
        └── Languages/
            ├── language_data.xml
            ├── English/
            │   └── MyStrings.xml
            └── CNs/
                └── MyStrings.xml
```

### 9.3 language_data.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LanguageData id="English">
  <LanguageFile xml_path="English/MyStrings.xml" />
</LanguageData>
```

### 9.4 翻译文件格式

```xml
<?xml version="1.0" encoding="utf-8"?>
<base xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" type="string">
    <tags>
        <tag language="English" />
    </tags>
    <strings>
        <string id="MY_EVENT_001" text="You find a mysterious box by the road." />
        <string id="MY_OPTION_001" text="Open the box" />
        <string id="MY_OPTION_002" text="Leave it alone" />
    </strings>
</base>
```

---

## 10. 调试与测试

### 10.1 控制台命令

在游戏中按 `Alt + ~` 打开控制台，使用以下命令：

| 命令 | 说明 |
|------|------|
| `captivity.reload_settings` | 重新加载配置 |
| `captivity.reload_events` | 重新加载事件（热重载） |
| `captivity.list_events` | 列出所有加载的事件 |
| `captivity.list_possible_events` | 列出当前可触发的事件 |
| `captivity.fire_event [事件名]` | 强制触发指定事件 |
| `captivity.can_i_run_this_event [事件名]` | 检查事件是否可触发 |
| `captivity.current_status [英雄名]` | 查看英雄状态 |
| `captivity.impregnate [英雄名]` | 使英雄怀孕（测试用） |
| `captivity.clean_save` | 清理CE存档数据 |

### 10.2 常见错误排查

#### 事件不触发

1. 检查 `SubModule.xml` 中的依赖是否正确
2. 确保事件有正确的事件类型标志（`Random`/`Captive`/`Captor`）
3. 使用 `captivity.can_i_run_this_event` 检查条件
4. 检查 `WeightedChanceOfOccurring` 是否设置

#### XML解析错误

1. 确保XML格式正确（闭合标签、引号等）
2. 使用在线XML验证器检查语法
3. 检查 XSD 路径是否正确

#### 图片不显示

1. 确保图片是PNG格式
2. 检查 `BackgroundName` 不包含文件扩展名
3. 确保图片在正确的 `Images/` 目录中

### 10.3 使用可视化编辑器

项目提供了一个HTML编辑器工具：`Tools/CEEventEditor.html`

直接在浏览器中打开即可使用，可以帮助你：
- 可视化创建事件
- 生成正确的XML代码
- 验证事件结构

---

## 11. 常见问题解答

### Q: 我需要会编程吗？

**A**: 不需要！CE完全是数据驱动的，你只需要编写XML文件。XML本质上就是一种结构化的文本格式，学习起来非常简单。

### Q: 如何让我的事件更容易触发？

**A**: 增加 `WeightedChanceOfOccurring` 的值。例如：
- 权重 10：较少触发
- 权重 50：中等频率
- 权重 100：频繁触发
- 权重 500：非常频繁

### Q: 为什么我的选项显示为灰色/不可选？

**A**: 检查选项的条件要求：
- `SkillsRequired`：技能不足
- `TraitsRequired`：特质不满足
- `ReqGoldAbove`：金币不足
- `ReqTroopsAbove`：士兵不足

### Q: 如何创建一个只在特定情况下出现的事件？

**A**: 组合使用多个标志：
```xml
<MultipleRestrictedListOfFlags>
    <RestrictedListOfFlags>Random</RestrictedListOfFlags>
    <RestrictedListOfFlags>HeroGenderIsFemale</RestrictedListOfFlags>
    <RestrictedListOfFlags>TimeNight</RestrictedListOfFlags>
    <RestrictedListOfFlags>SeasonWinter</RestrictedListOfFlags>
</MultipleRestrictedListOfFlags>
```
以上事件只会在：女性角色 + 夜间 + 冬季 时触发。

### Q: 如何让事件有多个随机结果？

**A**: 使用 `TriggerEvents` 配合权重：
```xml
<TriggerEvents>
    <TriggerEvent>
        <EventName>好结果</EventName>
        <EventWeight>7</EventWeight>  <!-- 70%概率 -->
    </TriggerEvent>
    <TriggerEvent>
        <EventName>坏结果</EventName>
        <EventWeight>3</EventWeight>  <!-- 30%概率 -->
    </TriggerEvent>
</TriggerEvents>
```

### Q: 可以使用自己的图片吗？

**A**: 可以！将PNG图片放入 `Images/` 目录，然后在 `BackgroundName` 中引用（不含扩展名）。

### Q: 如何让事件只触发一次？

**A**: 使用 `CanOnlyHappenNrOfTimes`：
```xml
<CanOnlyHappenNrOfTimes>1</CanOnlyHappenNrOfTimes>
```

---

## 12. 速查表

### 12.1 事件模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CEEvents xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="[相对路径]/CEEventsModal.xsd">

    <CEEvent>
        <Name>UNIQUE_EVENT_ID</Name>
        <Text>事件描述文本</Text>
        <BackgroundName>image_name</BackgroundName>

        <MultipleRestrictedListOfFlags>
            <RestrictedListOfFlags>Random</RestrictedListOfFlags>
            <RestrictedListOfFlags>LocationLand</RestrictedListOfFlags>
        </MultipleRestrictedListOfFlags>

        <Options>
            <Option>
                <Order>0</Order>
                <MultipleRestrictedListOfConsequences>
                    <RestrictedListOfConsequences>Leave</RestrictedListOfConsequences>
                </MultipleRestrictedListOfConsequences>
                <OptionText>选项文本</OptionText>
            </Option>
        </Options>

        <WeightedChanceOfOccurring>20</WeightedChanceOfOccurring>
        <ReqHeroMinAge>18</ReqHeroMinAge>
    </CEEvent>

</CEEvents>
```

### 12.2 常用后果组合

| 场景 | 后果组合 |
|------|---------|
| 获得金币并离开 | `GiveGold` + `Leave` |
| 失去金币并继续 | `ChangeGold` + `Continue` |
| 尝试逃跑 | `AttemptEscape` |
| 触发战斗 | `StartBattle` |
| 释放俘虏并提升声望 | `ReleaseRandomPrisoners` + `ChangeRenown` + `Leave` |
| 等待进度事件 | `Wait` |

### 12.3 常用需求参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `ReqHeroMinAge` | 最低年龄 | `18` |
| `ReqHeroMaxAge` | 最高年龄 | `50` |
| `ReqGoldAbove` | 最低金币 | `100` |
| `ReqGoldBelow` | 最高金币 | `1000` |
| `ReqTroopsAbove` | 最少士兵 | `10` |
| `ReqTroopsBelow` | 最多士兵 | `100` |
| `ReqCaptivesAbove` | 最少俘虏 | `1` |
| `ReqMoraleAbove` | 最低士气 | `30` |
| `ReqMoraleBelow` | 最高士气 | `70` |

### 12.4 随机数语法

```xml
<GoldTotal>R 100 500</GoldTotal>      <!-- 100到500之间随机 -->
<HealthTotal>R -20 -5</HealthTotal>   <!-- -20到-5之间随机 -->
<RenownTotal>R 1 10</RenownTotal>     <!-- 1到10之间随机 -->
```

---

## 附录：推荐学习路径

1. **入门**：仔细阅读 `ExampleExpansion` 中的示例事件
2. **进阶**：研究 `CEDefaultEvents1311` 中的默认事件
3. **参考**：查阅 `CEEventsModal.xsd` 了解所有可用元素
4. **实践**：从简单的随机事件开始，逐步增加复杂度
5. **测试**：使用控制台命令频繁测试你的事件

---

*本指南基于 Captivity Events v1.3.11.1311 编写*
*如有问题，请参考官方文档或社区资源*
