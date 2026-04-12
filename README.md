# 基于GLM5 + OpenClaw架构在 UnipatAI Monthly SWEBench 2026-03 上的测试结果

使用的是Harbor+openclaw的架构，来自这个pr：https://github.com/harbor-framework/harbor/pull/879


### 遇到的部分困难

1、Run5-Alpine-5中的五道题没办法直接运行，是因为PR #879 这版 OpenClaw 安装脚本只适配了 Debian/Ubuntu，没有适配 Alpine 系统，在本地去修改Harbor 的脚本文件解决了这个问题。

2、因为是第一次运行该项目，确实存在经验不足。而且国内的vpn网络非常不稳定，很多题目相关的镜像网站访问出错，docker 因为 vpn 的原因也常常会出现网络错误。所以112道题分了很多次分别完成，因此git上传的文件比较分散，非常抱歉。


### Repo结构

[Run1-43](https://github.com/WishingCat/UnipatAI-Monthly-SWEBench-2026-03-GLM5-OpenClaw/tree/main/Run1-43 "Run1-43")

[Run2-24](https://github.com/WishingCat/UnipatAI-Monthly-SWEBench-2026-03-GLM5-OpenClaw/tree/main/Run2-24 "Run2-24")

[Run3-9](https://github.com/WishingCat/UnipatAI-Monthly-SWEBench-2026-03-GLM5-OpenClaw/tree/main/Run3-9 "Run3-9")

[Run4-Alpine-5](https://github.com/WishingCat/UnipatAI-Monthly-SWEBench-2026-03-GLM5-OpenClaw/tree/main/Run4-Alpine-5 "Run4-Alpine-5")

分别是五次运行的tasks记录。

每次由于各种debug，均只完成了部分任务，比如Run1完成了43道题目，Run2为24道。

[Run4-Alpine-5](https://github.com/WishingCat/UnipatAI-Monthly-SWEBench-2026-03-GLM5-OpenClaw/tree/main/Run4-Alpine-5 "Run4-Alpine-5") 是专门为了解决「遇到的部分困难」-「1」的五道问题题目而单独运行的结果。
