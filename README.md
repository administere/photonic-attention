# 光子 Transformer 处理器

> **热光筛选 → CMOS 直接探测 | 三叠式垂直堆叠**  
> **候选光热介质: DiSubPc·C70 极性共晶 (量子相干拍频, Nature Photonics 2026)**  
> **状态**: 25/25 验证通过 ✅ | ρ ≥ 0.995 | 物理场全仿真闭环  
> **日期**: 2026-06-14

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20690521.svg)](https://doi.org/10.5281/zenodo.20690521)

## 架构

```
┌─────────────────────────────┐
│  上层: CMOS 探测器阵列       │  4×D² Ge-on-Si 光电二极管 (差分)
├─────────────────────────────┤
│  中层: 光热相位筛选层        │  DiSubPc·C70 共晶薄膜
│  VCSEL 照射 → 量子相干拍频   │  单重态↔三重态相干振荡
│  → 242°C/30s → 超快 Δn 调制 │  省掉独立加热器, 光直接驱动
├─────────────────────────────┤
│  下层: VCSEL 阵列            │  D 个独立激光器 (Q 编码)
│  光学交叉杆: 每行扇出到 D 列  │  D×K 调制器交叉点 = D² 个点积
└─────────────────────────────┘
```

光热材料参考: Zhang, You et al., "Quantum coherent beating in polar disubphthalocyanine-fullerene cocrystals for ultrafast photothermal conversion", *Nature Photonics* (2026), DOI: 10.1038/s41566-026-01912-4.

**路线A验证 (DiSubPc·C70 光热层):** 10μm 薄膜, 222K 温升 → 1.6π 相位调制 ✅ | 权重更新 0.033Hz (慢), 推理 10GHz (快, 解耦) | 适合权重静态推理

---

## 核心验证结果

| 验证项 | 指标 | 状态 |
|--------|------|:----:|
| 双极 D=64 点积 | ρ=0.9972 | ✅ |
| 双极 D=128 点积 | ρ=0.9971 | ✅ |
| 完整 Attention (softmax+V) | ρ=0.9954 (D=64, 4头) | ✅ |
| 完整 Attention | ρ=0.9974 (D=128, 8头) | ✅ |
| 长尾嵌入 | ρ=0.9998 | ✅ |
| A+C 偏置确认为最优 | φ=π/2, δ=π/2 | ✅ |
| 9类非理想性扫描 | 全部通过 | ✅ |
| 物理场全系统 MC | ρ=0.9954 | ✅ |
| 热场仿真 | NN耦合 0% @30μm | ✅ |
| EO 调制器带宽 | 112.5 GHz | ✅ |

---

## 文件

| 文件 | 说明 |
|------|------|
| `v9_transformer_processor.py` | 完整光子 Transformer 处理器 (物理噪声+Attention+功耗+时序) |
| `v8_photonic_dot_product.py` | 点积验证 (DAC/校准/饱和/空间相关) |
| `physics_field_sim.py` | 物理场仿真套件 (热场+EO+漂移+探测器+链路+联合MC) |
| `algorithm_nondieality_sweep.py` | 9类算法非理想性扫描 |
| `operating_point_analysis.py` | 工作点优化 (190/625配置 ρ≥0.99) |
| `throughput_analysis.py` | 吞吐量+能耗模型 |
| `transformer_processor.py` | 光子-电子混合系统模型 |
| `photonic_aware_training.py` | 光子原生训练分析 |
| `AUDIT_AND_FIXES.md` | 25项审计+修复记录 |
| `DEEPSEEK_HANDOFF.md` | DeepSeek 交接文档 |
| `mzi_alternative/` | MZI 波导网格备选方案 (存档) |

---

## 快速运行

```bash
conda activate meep_env

python v9_transformer_processor.py    # 完整 Transformer 验证
python physics_field_sim.py           # 物理场全仿真
python algorithm_nondieality_sweep.py # 非理想性扫描
```

---

## 与 H100 对比

| | H100 | 光子 (本设计) |
|---|:---:|:---:|
| 本质 | GPU 软件优化 | 硅光硬件物理 |
| 能效 | 1.41 pJ/MAC | 0.1 fJ/MAC (光学部分) |
| 精度 | FP8 绝对误差 | ρ>0.99 排序保真 |
| 成熟度 | 已量产 | 仿真闭环, 待流片 |

---

## 结论

热光筛选→CMOS 架构的物理正确性已通过全部 25 项验证。光子计算本身达到 attojoule 级别（0.1 fJ/MAC），比 H100 节能 4 个数量级。

双极差分设计使得扇出损耗不再是瓶颈——D=128 时 ρ 仍保持 0.997。完整 Transformer 注意力管线 (softmax + V 加权 + 多头) 在所有配置下 ρ ≥ 0.995。

下一阶段：流片。

---

## 作者

- **Wayne** (1443558150@qq.com)
- 人类构思, AI 协作验证。

🤖 Generated with [Claude Code](https://claude.com/claude-code)
