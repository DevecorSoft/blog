# color-profile 一个提升视觉测试稳定性的杀手级配置

## 先说结论

`color-profile`是一个能极大的提升视觉测试稳定性的配置, 能够基本消除色差对视觉测试影响, `maxDiffThreshold: 0.01`不是梦. 这个配置对于chrome浏览器来说是`chrome://flags/#force-color-profile`, `chromium`家族的浏览器都有相应的配置.

## 基于Cypress的视觉测试配置

cypress.config.ts

```
import { defineConfig } from "cypress"

export default defineConfig({
  component: {
    setupNodeEvents(on, config) {
      on('before:browser:launch', (_, launchOptions) => {
        launchOptions.args.push('--force-color-profile=sRGB')
        return launchOptions
      })
    },
  },
})
```

## 配置`color-profile`解决的问题是什么?

在开发过程中, 我们常常会遇到下面这样的场景:

1. 插上显示器视觉测试是过的, 但是拔掉显示器后, 视觉测试会挂
2. 本地视觉测试能通过, 但是在 ci runner 上会挂, 选择以 runner 的截图为基准会导致本地测试不通过, 不得不增大阈值
3. 如果 ci runner 本身不是真机, 而是虚拟环境, 视觉测试有概率的不通过, 导致要多次 rerun 才能通过测试

很大一部分原因是在不同的机器上跑出来的截图存在严重的色差, 很影响图像比对, 常常能达到惊人的30%以上. 至于有色差的原因, 大概有两点:

- 真机上,浏览器使用的 color-profile 是有显示器的色域和用户的个人偏好决定的, 会产生明显色差
- 虚拟环境下, 影响因素更多, 操作系统差异, 物理机的配置不同都会影响到 color-profile

因此, 在不同的环境中强制浏览器使用标准的 sRGB 色彩空间渲染页面能够基本上消除色差对视觉测试的影响

## 消除色差后, 比对阈值的推荐配置

- 组件级视觉测试: 0.01
- 页面级视觉测试: 0.05

## chromium 家族之外的浏览器?

- 对于 safari 没有单独的配置, 完全取决于macos的配置 `System Settings -> Displays -> color profile`
- firefox下的配置是 `gfx.color_management.display_profile`
