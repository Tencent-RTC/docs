> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用 SmallStreamAutoSwitcher 插件来启动自动切换大小流的能力。该插件通常用在带宽受限的场景，如带宽不足，导致大流卡顿，则自动切换到小流，会降低分辨率但保证流畅度。

## 前提条件

- TRTC Web SDK 版本 >= 5.11.0

## 实现流程

### 引入并注册插件

```javascript
import SmallStreamAutoSwitcher from 'trtc-sdk-v5/plugins/small-stream-auto-switcher';
const trtc = TRTC.create({ plugins: [SmallStreamAutoSwitcher] });
```

## 插件启动

默认的切换策略为：
- 当网络持续 10s 内延迟大于 200ms 或 丢包率大于 20%，并且帧率波动较大时，则切换到小流
- 当网络持续 20s 内延迟小于 100ms 且 丢包率小于 10% 时，并且帧率恢复正常时，则切回到大流
- 只允许自动切换到小流 2 次，超过 2 次后，则不再自动切换到小流

```javascript
trtc.startPlugin('SmallStreamAutoSwitcher');
```

## 插件停止

```javascript
trtc.stopPlugin('SmallStreamAutoSwitcher');
```

## 进阶使用

可以通过自定义参数来调整插件的策略，如：

只允许自动切换到小流 1 次，切换后不再切回大流。

```javascript
trtc.startPlugin('SmallStreamAutoSwitcher', {
  maxAutoSwitchToSmallCount: 1,
});
```

以下为所有参数的说明，不传则使用默认策略。

```javascript
trtc.startPlugin('SmallStreamAutoSwitcher', {
  rttPoorLimit: 200, // 延迟阈值，单位：ms，当延迟大于此值时触发切换到小流的条件，默认 200
  lossPoorLimit: 20, // 丢包率阈值，单位：%，当丢包率大于此值时触发切换到小流的条件，默认 20
  poorDuration: 10 * 1000, // 网络状况差的持续时间阈值，单位：ms，当延迟或丢包率连续超过阈值达到此次数时，开始检查是否切换到小流，默认 10000（10秒）
  rttGoodLimit: 100, // 延迟良好阈值，单位：ms，当延迟小于此值时触发切换到大流的条件，默认 100
  lossGoodLimit: 10, // 丢包率良好阈值，单位：%，当丢包率小于此值时触发切换到大流的条件，默认 10
  goodDuration: 20 * 1000, // 网络状况好的持续时间阈值，单位：ms，当延迟和丢包率连续低于阈值达到此次数时，开始检查是否切换到大流，默认 20000（20秒）
  sleepTime: 30 * 1000, // 切换后的休眠时间，单位：ms，在此时间内不再进行自动切换，默认 30000（30秒）
  maxAutoSwitchToSmallCount: 2, // 每个用户最大自动切换到小流的次数，超过此次数后不再自动切换到小流，默认 2
});
```

## 参数说明

| 参数名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| rttPoorLimit | number | 200 | 延迟阈值（ms），当网络延迟大于此值时，开始计数切换到小流的条件 |
| lossPoorLimit | number | 20 | 丢包率阈值（%），当丢包率大于此值时，开始计数切换到小流的条件 |
| poorDuration | number | 10000 | 网络状况差的持续时间阈值（ms），当延迟或丢包率连续超过阈值达到此次数时，开始检查是否切换到小流，默认 10000（10秒） |
| rttGoodLimit | number | 100 | 延迟良好阈值（ms），当网络延迟小于此值时，开始计数切换到大流的条件 |
| lossGoodLimit | number | 10 | 丢包率良好阈值（%），当丢包率小于此值时，开始计数切换到大流的条件 |
| goodDuration | number | 20000 | 网络状况好的持续时间阈值（ms），当延迟和丢包率连续低于阈值达到此次数时，开始检查是否切换到大流，默认 20000（20秒） |
| sleepTime | number | 30000 | 切换后休眠时间（ms），在此期间内不会再次触发自动切换，避免频繁切换 |
| maxAutoSwitchToSmallCount | number | 2 | 每个用户允许的最大自动切换到小流次数，超过后不再自动切小流 |

## 切换逻辑说明

### 切换到小流条件
1. 网络条件：延迟连续 `poorCount` 次超过 `rttPoorLimit`、或丢包率连续 `poorCount` 次超过 `lossPoorLimit`
2. 帧率条件：大流帧率波动较大（最大帧率与最小帧率的差值超过最大帧率的1/3）
3. 限制条件：用户当前订阅的是大流，且自动切换次数未达到 `maxAutoSwitchToSmallCount` 上限，且不在休眠期

### 切换到大流条件
1. 网络条件：延迟连续 `goodCount` 次低于 `rttGoodLimit`、且丢包率连续 `goodCount` 次低于 `lossGoodLimit`
2. 帧率条件：小流帧率稳定（最大帧率与最小帧率的差值不超过最大帧率的1/3）
3. 限制条件：用户当前订阅的是小流，且该用户只切换过1次小流，且不在休眠期

> **注意：** 
> - 检测频率为每 2 秒一次
> - 网络质量和帧率数据的统计窗口大小自动适应poorDuration和goodDuration的最大值
> - 每次切换后会进入 `sleepTime` 时长的休眠期，期间不会再次触发自动切换
> - 只有自动切换过1次小流的用户才允许切回大流，切换过2次的用户将保持小流状态
