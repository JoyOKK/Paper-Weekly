# Paper Weekly

每周阅读 1–2 篇论文，在本文件以列表记录每篇的**核心 Idea / 贡献 / 解决的挑战 / 方法**，并把汇报 HTML 放在对应日期子文件夹下，方便随时翻阅回顾。

## 目录约定

```
YYYY-MM-DD/                 # 阅读日期
  <paper-slug>/
    presentation.html       # 论文汇报（可直接浏览器打开）
template/
  presentation.html         # 新论文汇报模板
```

新增一篇时：复制 `template/presentation.html` 到当天子目录，填完内容后在下方列表补一条。

---

## 论文列表

| 日期 | 论文 | 会议 | 核心 Idea（一句话） | 汇报 |
| --- | --- | --- | --- | --- |
| 2026-08-19 | [ARDY](./2026-08-19/ardy/presentation.html) | SIGGRAPH (TOG) 2026 | 显式 root + 隐式 body 混合表征，两阶段自回归扩散做实时交互式动作生成 | [HTML](./2026-08-19/ardy/presentation.html) |

---

## 2026-08-19

### ARDY — Autoregressive Diffusion with Hybrid Representation for Interactive Human Motion Generation

- **会议 / 年份:** SIGGRAPH (TOG) 2026
- **作者:** NVIDIA + ETH Zürich（Zhao, Petrovich, Zhang, Wang, Tang, Rempe）
- **链接:** [Project](https://research.nvidia.com/labs/sil/projects/ardy/) · [arXiv](https://arxiv.org/abs/2607.08741) · [GitHub](https://github.com/nv-tlabs/ardy)
- **汇报:** [presentation.html](./2026-08-19/ardy/presentation.html)

**核心 Idea**

把动作拆成**全局显式 root** 和 **tokenizer 压缩的 body latent**，再用**交错两阶段 Transformer 去噪器**自回归生成短窗口动作。这样既能直接覆盖 root 做轨迹控制，又能在紧凑隐空间里高效生成身体运动，从而同时支持在线文本与长程运动学约束。

**解决的挑战**

- 离线方法（扩散 / 掩码生成）文本与约束控制强，但整段并行去噪，延迟不适合交互。
- 在线方法实时，但往往只能做文本或只能做运动学控制；即便两者都有，历史窗口太短，复杂语义与窗口外长程目标会失败。
- 先前工作常靠测试时优化或额外 RL 策略补控制，交互场景下太慢。

**贡献**

1. Hybrid 表征：显式全局 root + 潜在 body，兼顾精确轨迹覆盖与生成学习效率。
2. 两阶段自回归扩散：可变历史上下文，原生支持窗口内/外的 root 路径、关键帧、稀疏关节位置/旋转。
3. 在 Bones Rigplay（约 700h）上做架构消融，并在 HumanML3D 上对比离线 / 在线 SOTA；4-step 模型 RTX 4090 上约 33 ms。

**方法要点**

- Body tokenizer：因果 Transformer + FSQ，patch=4 帧；解码器把全局 root 转成本地速度表示，减轻滑步。
- 生成：每次预测 \(C\) 个 token（默认 40 帧 / 2s @20fps），条件为文本 \(s\)、可变历史 \(H\)（最长 8s）、以及可超出当前窗口的目标 \(\mathbf{g}\)。
- 约束以 masked 显式动作注入：root 特征直接 overwrite，body 目标与 mask 拼进 token。
- 两阶段去噪：先预测干净 root，再条件预测 body latent，步间交错互相影响。
- 损失：hybrid L1 + 解码 body L1 + 约束 L1 + FK 一致性；CFG 训练时 10% 丢文本/约束。
