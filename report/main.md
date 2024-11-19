# Chromium 在 RISC-V 平台上的可用性报告

# 简介

## 软件说明
Chromium 是一个免费的开源网络浏览器项目，主要由 Google 开发和维护。它是一个广泛使用的代码库，为 Google Chrome 和许多其他浏览器（包括 Microsoft Edge、Samsung Internet 和 Opera）提供了绝大多数代码。该代码还被多个应用程序框架使用。

## 测试目的
本次测试皆在验证 Chromium 在 RISC-V 平台上的可用性，特别是在 Milk-V Pioneer Box 和 Sipeed LicheePi 4A 两个典型平台上的表现。通过手动测试的方法，评估了 Chromium 当前在 RISC-V 平台上的可用性，对目前的平台兼容性，及用户的日常使用体验，给出了定性和定量的结论，并为其未来进一步的优化和支持提供参考。

## 测试概述
本次测试在 RISC-V 设备 Milk-V Pioneer Box 和 Sipeed LicheePi 4A 的多个 Linux 发行版上对多个版本的 Chromium 进行了手动测试,手动测试采用贴近真实用户日常使用场景的测试用例，模拟一般用户的操作方式对 Chromium 在测试环境上的日常浏览体验进行了评估，对其目前在 RISC-V 上的可用性进行了较为全面的测试并得出了相应的结论。

在部分系统下还使用了 Speedometer3 和 Basemark 来测试浏览器浏览网页以及图形性能。

## 测试总结
本次测试在以下平台和系统上验证情况如下：

| 平台        | 发行版        | 测试结果              |
|-------------|---------------|-----------------------|
| Pioneer Box | RevyOS        | 可用                  |
| LicheePi 4A | RevyOS        | 可用                  |
| Pioneer Box | openEuler     | 可用                  |
| LicheePi 4A | openEuler     | 无 GUI 版本           |
| LicheePi 4A | Fedora        | 当前无网卡支持        |
| Pioneer Box | Fedora        | 软件源内没有 Chromium |
| Both        | openKylin     | 软件源内没有 Chromium |
| Both        | openCloudOS   | 未找到可用版本        |
| Both        | Ubuntu        | 未找到可用版本        |
| Both        | Debian/RuyiOS | 使用 RevyOS 替代      |


性能测试结果如下：

- Speedometer 3：

| Pioneer Box | LicheePi 4A | x86-64 参考 |
|-------------|-------------|-------------|
|    1.03     | 0.706       | 10-20       |

- Basemark：

| Pioneer Box             | LicheePi 4A             | x86-64 参考 |
|-------------------------|-------------------------|-------------|
| 22.87（部分测试未运行） | 16.98（部分测试未运行） | 2000        |

## 环境说明

## 硬件环境
本次测试主要在 Milk-V Pioneer Box 和 Sipeed LicheePi 4A 上进行，机器硬件配置为：

Milk-V Pioneer Box:
- CPU: SG2042 64 Core C920@2.0GHz
- RAM: 4 channel 3200Hz 128GB DDR4 SODIMM (32GB * 4)
- SSD: PCIe 3.0 x 4 1TB
- GPU: AMD R5 230

Sipeed LicheePi 4A:
- CPU: TH1520, RISC-V 2.0G C910 x4
- RAM: 16 GB 64bit LPDDR4X-3733
- Storage: TF Card, 128 GB eMMC

## 软件环境

本次测试涵盖的系统版本和 Chromium 版本如下：
- openEuler（仅测试 Pioneer Box）:软件仓库自带 Chromium 
  - Pioneer Box 版本: 
- RevyOS:软件仓库自带 Chromium
  - Pioneer Box 版本: Chromium 128.0.6613.113
  - LicheePi 4A 版本: Chromium 109.0.5414.119
  
其余未测试系统及原因如下：

| LicheePi 4A | openEuler     | 无 GUI 版本           |
| LicheePi 4A | Fedora        | 当前无网卡支持        |
| Pioneer Box | Fedora        | 软件源内没有 Chromium |
| Both        | openKylin     | 软件源内没有 Chromium |
| Both        | openCloudOS   | 未找到可用版本        |
| Both        | Ubuntu        | 未找到可用版本        |
| Both        | Debian/RuyiOS | 使用 RevyOS 替代      |

## 环境搭建

### 安装系统

#### Sipeed LicheePi 4A

LicheePi 4A 各个系统在[支持矩阵](https://github.com/ruyisdk/support-matrix/tree/main/LicheePi4A)上详细记载了如何进行安装，可作为参考。

- RevyOS
从 [ISCAS 镜像](https://mirror.iscas.ac.cn/revyos/extra/images/lpi4a/)下载并解压：
```shell!
wget https://mirror.iscas.ac.cn/revyos/extra/images/lpi4a/20240720/boot-lpi4a-20240720_171951.ext4.zst
wget https://mirror.iscas.ac.cn/revyos/extra/images/lpi4a/20240720/u-boot-with-spl-lpi4a.bin
wget https://mirror.iscas.ac.cn/revyos/extra/images/lpi4a/20240720/root-lpi4a-20240720_171951.ext4.zst
zstd -d boot-lpi4a-20240720_171951.ext4.zst
zstd -d root-lpi4a-20240720_171951.ext4.zst
```

按住 boot 键后，上电/Reset 进入刷写模式。

接下来刷写系统：
```shell!
sudo fastboot devices
sudo fastboot flash ram u-boot-with-spl-lpi4a.bin 
sudo fastboot reboot
sudo fastboot flash uboot u-boot-with-spl-lpi4a.bin
sudo fastboot flash boot boot-lpi4a-20240720_171951.ext4
sudo fastboot flash root root-lpi4a-20240720_171951.ext4
```
默认账号密码为：`debian`:`debian`

#### Milk-V Pioneer Box

##### RevyOS
下载 [RevyOS 20241025](https://mirror.iscas.ac.cn/revyos/extra/images/sg2042/20241025/revyos-pioneer-20241025-001347.img.zst)后，使用 `zstd` 解压， `dd` 到 **NVME 硬盘**中 *（需要一个硬盘盒）*
```shell!
zstd -d revyos-pioneer-20241025-001347.img.zst
sudo dd if=path/to/revyos-pioneer-20241025-001347.img of=/dev/your/nvme bs=4M status=progress
```
将硬盘插入系统后自动启动

##### openEuler
<!-- TODO: -->

### 手动测试环境

#### RevyOS
采用 `apt` 进行安装：
```shell!
sudo apt update
sudo apt upgrade
sudo apt install chromium
```

#### openEuler
<!-- TODO:图形界面安装 -->
采用 `` 进行安装：
```shell!

```


### 性能测试

- Speedometer 3

在 Chromium 浏览器中访问：https://browserbench.org/Speedometer3.0/

- Basemark

在 Chromium 浏览器中访问：https://web.basemark.com

# 测试内容

## 手动测试

手动测试中，通过模拟用户日常使用习惯，精选了 138 个测试用例进行测试，其涵盖了浏览网页、下载、书签、打印、设置、历史等常见操作，并以此基础进行了实际使用体验和步骤截图。

## 性能测试

- Speedometer 3

Speedometer 3 是 Web 浏览器的基准测试，它通过对各种工作负载上的模拟用户交互进行计时来测量 Web 应用程序响应能力。

- Basemark

Basemark Web 3.0 是一个全面的 Web 浏览器性能基准测试，可测试移动或桌面系统使用基于 Web 的应用程序的能力。该基准测试包括使用网络建议和功能的各种系统和图形测试。

# 测试结果

详细测试数据可见 [Github 仓库](https://github.com/QA-Team-lo/chromium_test)

## 手动测试

手动测试 Chromium 可以正常使用。除解码可能由于没有外设支持导致无法硬解外，其余工作正常。

包括 Google、Baidu、Bilibili 和知乎等网站均能正常访问，账号功能正常可以登录和同步，书签、历史记录、下载等常用功能运行良好。

由于只有软解且 CPU 性能不足，视频播放遇到卡顿，在预期之中。

# 性能测试
<!-- TODO: -->
- Speedometer 3


- Basemark

LicheePi 4A 和 Pioneer Box 不支持测试所需 WebGL 图形渲染。

# 总结

