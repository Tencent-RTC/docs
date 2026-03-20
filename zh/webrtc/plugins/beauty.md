> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要描述如何使用美颜插件。

## 前提条件

- 需要使用美颜功能的 sdkAppId 已开通专业版包月套餐。请参见文档 [包月套餐计费说明](https://www.tencentcloud.com/zh/document/product/647/56025)
- [TRTC SDK](https://www.npmjs.com/package/trtc-sdk-v5) 版本需要 >= 5.5.0
- 各平台系统及配置要求如下表：

| 平台 | 操作系统 | 浏览器版本 | fps | 推荐配置 | 备注 |
| --- | --- | --- | --- | --- | --- |
| Web | Windows | Chrome 90+ Firefox 90+ Edge 97+ | 30 | 内存：16GB CPU：i5-10500 GPU：独显 2GB | 建议使用最新版 Chrome 浏览器  <b> （开启浏览器硬件加速）</b> |
| 15 | 内存：8GB CPU：i3-8300 GPU：intel 核显 1GB |  |  |  |  |
| Mac | Chrome 98+ Firefox 96+ Safari 14+ | 30 | 2019年 MacBook 内存：16GB(2667MHz) CPU：i7(6核 2.60GHz) GPU：AMD Radeon 5300M |  |  |
| Android | 微信内嵌浏览器 Chrome 火狐浏览器 小米浏览器 华为浏览器 | 30 | 高端机（高通骁龙 8 Gen1 等） |  |  |
| 20 | 中端机（天玑 8000-MAX 等） |  |  |  |  |
| 10 | 低端机（高通骁龙 660 等） |  |  |  |  |
| iOS | 微信内嵌浏览器 Chrome Safari Firefox | 30 | iPhone 13 | - 要求 iOS 14.4 以上系统 - 建议使用 Chrome 或 Safari 浏览器 - <b> 不支持 QQ 浏览器</b> |  |
| 20 | iPhone xr |  |  |  |  |

## 使用步骤

### 一、注册插件

在使用之前，首先需要注册插件。以下是注册插件的示例代码：

```js
import TRTC from 'trtc-sdk-v5';
import { Beauty } from 'trtc-sdk-v5/plugins/video-effect/beauty';

const trtc = TRTC.create({ plugins: [Beauty] });
```

### 二、开启本地摄像头

```js
await trtc.startLocalVideo({
  view: 'local-video' // 填入自己业务需要的 div id
});
```

### 三、使用美颜和内置 2D 特效

#### 美颜效果

美颜功能的开启、更新和停止通过三个接口来控制

- `trtc.startPlugin('Beauty', options)` 开启美颜
- `trtc.updatePlugin('Beauty', options)` 更新美颜参数
- `trtc.stopPlugin('Beauty')` 停止美颜

其中 `options` 参数支持 6 种美颜参数：

```ts
type BeautifyOptions = {
  whiten?: number; // 美白 0-1
  dermabrasion?: number; // 磨皮 0-1
  lift?: number; // 窄脸 0-1
  shave?: number; // 削脸 0-1
  eye?: number; // 大眼 0-1
  chin?: number; // 下巴 0-1
}
```

#### 内置 2D 特效贴纸

本插件提供内置的 2D 特效贴纸，下面是列表

| 特效 ID            | 名称        |
|---------------------|--------------------|
| EDE5D18065451445    | 奶瓶面膜 |
| 7A62218064EF7959    | 毛毡发箍     |
| B4D63180653948C3    | 刘海发带     |
| D7C6418064B90F4C    | 可爱喵        |
| 1D6EB18069D76A62    | 波点女孩    |
| CBE7618065D9D76F    | 爱心烟花    |
| 9B86618065596018    | 可爱猪猪        |
| 428C518065369ACF    | 双麻花     |
| B30E218064F7B397    | 元气贴纸   |
| AE3C81806521B49B    | 兔兔酱      |

可以在参数中传入 `EffectListOptions` 类型的数组，将 `EffectId` 填入 `id` 字段，`intensity` 用于控制透明度。传入的 `EffectId` 对应的贴纸将会生效。更多信息请参考示例代码。

```ts
type EffectListOptions = {
  id: string;
  intensity?: number; // specify the strength of the effect
}
```

## 示例代码

以下是代码示例：

开启插件时，`options` 还需要额外携带当前用户的登录信息（sdkAppId、userId、userSig）用作校验。

```javascript
// 开启插件
const options = {
  sdkAppId: 0,
  userId: '',
  userSig: '',

  whiten: 0.5; // 美白 0-1
  dermabrasion: 0.5; // 磨皮 0-1
  lift: 0.5; // 窄脸 0-1
  shave: 0.5; // 削脸 0-1
  eye: 0.5; // 大眼 0-1
  chin: 0.5; // 下巴 0-1

  // 特效数组
  effect: [{
    id: '7A62218064EF7959', // 特效 ID
    intensity: 0.7 // 生效强度
  }]
}
try {
  await trtc.startPlugin('Beauty', options);
} catch (error) {
  console.error('Beauty start failed', error);
}
```

```javascript
// 更新美颜参数
const options = {
  whiten: 0.5; // 美白 0-1
  dermabrasion: 0.5; // 磨皮 0-1
  lift: 0.5; // 窄脸 0-1
  shave: 0.5; // 削脸 0-1
  eye: 0.5; // 大眼 0-1
  chin: 0.5; // 下巴 0-1

  effect: [{
    id: '7A62218064EF7959',
    intensity: 0.7,
  },{
    id: 'D7C6418064B90F4C',
    intensity: 0.7
  }]
}
try {
  await trtc.updatePlugin('Beauty', options);
} catch (error) {
  console.error('Beauty update failed', error);
}
```

```javascript
// 停止美颜
try {
  await trtc.stopPlugin('Beauty');
} catch (error) {
  console.error('Beauty stop failed', error);
}
```

## API 说明

### trtc.startPlugin('Beauty', options)

用于开启美颜

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| sdkAppId     | `number`  |    必填          | 当前应用 ID |
| userId    | `string`  |     必填         | 当前用户 ID                              |
| userSig   | `string` | 必填 | 用户 ID 对应的 UserSig                     |
| whiten   | `number` | 选填 | 美白 ，取值范围 [0,1]                  |
| dermabrasion   | `number` | 选填 | 磨皮 ，取值范围 [0,1]                    |
| lift   | `number` | 选填 | 窄脸 ，取值范围 [0,1]                    |
| shave   | `number` | 选填 | 削脸 ，取值范围 [0,1]                   |
| eye   | `number` | 选填 | 大眼 ，取值范围 [0,1]                    |
| chin   | `number` | 选填 | 下巴，取值范围 [0,1]                 |
| effect       | `Array<EffectListOptions>` | 选填   | 特效列表    |
| onError    | `(error) => {}`  |     选填           | 运行过程中发生错误的回调 <br> - error.code=10000003 渲染耗时长 <br>  - error.code=10000006 浏览器特性支持不足，可能会出现卡顿情况 <br> 推荐处理方法可参考文末常见问题                         |

EffectListOptions:

| Name       | Type     | Attributes | Description                       |
|------------|----------|------------|-----------------------------------|
| id   | `number` | 必填   | 特效 ID          |
| intensity     | `string` | 选填   | 生效强度                |

| 特效 ID              | 名称        |
|---------------------|--------------------|
| EDE5D18065451445    | 奶瓶面膜 |
| 7A62218064EF7959    | 毛毡发箍     |
| B4D63180653948C3    | 刘海发带     |
| D7C6418064B90F4C    | 可爱喵        |
| 1D6EB18069D76A62    | 波点女孩    |
| CBE7618065D9D76F    | 爱心烟花    |
| 9B86618065596018    | 可爱猪猪        |
| 428C518065369ACF    | 双麻花     |
| B30E218064F7B397    | 元气贴纸   |
| AE3C81806521B49B    | 兔兔酱      |

### trtc.updatePlugin('Beauty', options)

可修改美颜参数

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| whiten   | `number` | 选填 | 美白 ，取值范围 [0,1]                  |
| dermabrasion   | `number` | 选填 | 磨皮 ，取值范围 [0,1]                    |
| lift   | `number` | 选填 | 窄脸 ，取值范围 [0,1]                    |
| shave   | `number` | 选填 | 削脸 ，取值范围 [0,1]                   |
| eye   | `number` | 选填 | 大眼 ，取值范围 [0,1]                    |
| chin   | `number` | 选填 | 下巴，取值范围 [0,1]                 |
| effect       | `Array<EffectListOptions>` | 选填   | 特效列表    |

EffectListOptions:

| Name       | Type     | Attributes | Description                       |
|------------|----------|------------|-----------------------------------|
| id   | `number` | 必填   | 特效 ID          |
| intensity     | `string` | 选填   | 生效强度                |

| 特效 ID              | 名称        |
|---------------------|--------------------|
| EDE5D18065451445    | 奶瓶面膜 |
| 7A62218064EF7959    | 毛毡发箍     |
| B4D63180653948C3    | 刘海发带     |
| D7C6418064B90F4C    | 可爱喵        |
| 1D6EB18069D76A62    | 波点女孩    |
| CBE7618065D9D76F    | 爱心烟花    |
| 9B86618065596018    | 可爱猪猪        |
| 428C518065369ACF    | 双麻花     |
| B30E218064F7B397    | 元气贴纸   |
| AE3C81806521B49B    | 兔兔酱      |

### trtc.stopPlugin('Beauty')

关闭美颜

## 常见问题

**1. 在 Chrome 中运行 Demo 发现画面颠倒且卡顿**

本插件使用 GPU 进行加速，您需要在浏览器设置中找到使用硬件加速模式并启用。可以将 `chrome://settings/system` 复制到浏览器地址栏，并且打开硬件加速模式。

**2. 当设备性能不足造成延迟高，提示渲染耗时长**

可通过监听事件，降低视频分辨率或者帧率。

```javascript
async function onError(error) {
  const { code } = error;
  if (code === 10000003 || code === 10000006) {
    // 降低分辨率帧率
    await trtc.updateLocalVideo({
      option: {
        profile: '480p_2'
      },
    });
    // await trtc.stopPlugin('Beauty'); // 或者关闭插件
  }
}

await trtc.startPlugin('Beauty', {
  ...// 其他参数
  onError,
});
```
