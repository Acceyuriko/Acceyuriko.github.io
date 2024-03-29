---
title: 'Hades 作弊笔记'
date: 2022-01-02 16:10:00
description: 'Hades 作弊笔记'
tags: hades
---

[Hades](https://store.steampowered.com/app/1145360/Hades)，很棒的游戏，但无奈游戏苦手，一直打不通。研究了一些作弊方法，记在这里。

注：没有特殊说明的话，文件前置目录均为 `path/to/hades/Content/Scripts` 目录。修改之后一般需要放弃当前进度，或者退出游戏重开。

## 大厅兑换

游戏里资源实在是太肝了，不想肝的话可以改了花费去商店免费换。

`StoreData.lua` 下搜索 `BrokerData`

- BuyName 购买的物资
- BuyAmount 购买的数量
- CostName 花费的物资
- CostAmount 花费的数量

物资名称如下

- MetaPoints 黑暗
- Gems 宝石
- LockKeys 钥匙
- GiftPoints 蜜露
- SuperGems 钻石
- SuperGiftPoints 仙酒
- SuperLockKeys 泰坦之血

## 镜子强化

`MetaUpgradeData.lua` 文件下，找到想要的升级项，修改即可。

镜子的升级项如下：

```lua
MetaUpgradeOrder =
{
	{ "BackstabMetaUpgrade", "FirstStrikeMetaUpgrade" },
	{ "DoorHealMetaUpgrade", "DarknessHealMetaUpgrade" },
	{ "ExtraChanceMetaUpgrade", "ExtraChanceReplenishMetaUpgrade" },
	{ "StaminaMetaUpgrade", "PerfectDashMetaUpgrade" },
	{ "StoredAmmoVulnerabilityMetaUpgrade", "StoredAmmoSlowMetaUpgrade" },
	{ "AmmoMetaUpgrade", "ReloadAmmoMetaUpgrade" },
	{ "MoneyMetaUpgrade", "InterestMetaUpgrade" },
	{ "HealthMetaUpgrade", "HighHealthDamageMetaUpgrade" },
	{ "VulnerabilityEffectBonusMetaUpgrade", "GodEnhancementMetaUpgrade" },
	{ "RareBoonDropMetaUpgrade", "RunProgressRewardMetaUpgrade" },
	{ "EpicBoonDropMetaUpgrade", "DuoRarityBoonDropMetaUpgrade" },
	{ "RerollMetaUpgrade", "RerollPanelMetaUpgrade" },
}
```

每一项的基础参数如下

- Starting 是否不需要解锁
- CostTable 每次升级要花费的黑暗，添加该项的数目，则可以提高解锁上限
- ChangeValue 强化值

## 添加吸血

游戏里本身有一个吸血剑，把它的逻辑抄到想要添加吸血功能的 `trait` 中即可。

在 `TraitData.lua` 中，搜索 `AddOutgoingLifestealModifiers`，可以找到吸血的逻辑。

但是给每把武器都添加吸血，代码太多了。这里我给宙斯的闪电链祝福加上了吸血，这样只要抽到宙斯祝福就行。如下：

```lua
ZeusWeaponTrait =
{
    -- previous logic
    AddOutgoingLifestealModifiers =
    {
        ValidWeapons = {
            "SwordWeapon",
            "SpearWeapon",
            "BowWeapon",
            "ShieldWeapon",
            "FistWeapon",
            "GunWeapon",
        },
        ValidMultiplier = 1.00,
        MaxLifesteal = 2,
        MinLifesteal = 2,
    },
    -- next logic
}
```

## 增加攻击速度

同上，在 ZeusWeaponTrait 中添加增加攻击速度的效果即可。 这里 BaseValue 即攻击的充能时间。

测试发现，攻击速度有提升但不到 0.5 这个程度，不清楚是哪里限制了最小值。

```lua
PropertyChanges =
{
    -- previous logic
    {
        WeaponNames = {
            "SwordWeapon",
            "SpearWeapon",
            "BowWeapon",
            "ShieldWeapon",
            "FistWeapon",
            "GunWeapon",
        },
        WeaponProperty = "ChargeTime",
        BaseValue = 0.5,
        SourceIsMultiplier = true,
        ChangeType = "Multiply",
        ExcludeLinked = true,
    },
    -- next logic
}
```

## 开局自带闪电链天赋

在 `RunMananger.lua`中，找到 `StartNewRun`，把这句话添加到最后面

```lua
AddTraitToHero({ TraitName = "ZeusWeaponTrait", Rarity = "Heroic" })
```

加上上面两处修改，这样就有了增加攻速和吸血效果。


## 无限逆天改命

在 `RunManager.lua` 中，找到 `StartNewRun`，预设了游戏初始化时的数据。 找到 `NumRerolls`，改大即可。

```lua
-- CurrentRun.NumRerolls = GetNumMetaUpgrades( "RerollMetaUpgrade" ) + GetNumMetaUpgrades("RerollPanelMetaUpgrade")
CurrentRun.NumRerolls = 9999
```
