# Less-is-More

Simple yet Effective Heuristic Community Detection with Graph Convolution Network.

> 这是论文代码仓库（研究代码风格），核心目标是做**图社区发现 / 图聚类**实验。

---

## 1. 项目简介

本项目实现了一个基于 GCN 表征学习与结构启发式社区中心初始化的社区检测流程：

1. 加载图数据（特征、标签、邻接矩阵）；
2. 使用 Louvain 在图结构上获取初始社区；
3. 筛选较大结构社区作为“结构中心”；
4. 通过 GCN 编码得到节点表示；
5. 计算节点到社区中心的软分配；
6. 使用模块度相关目标进行优化并评估 NMI/ACC/F1/ARI/DBI/Q。

---

## 2. 仓库结构

```text
Less-is-More/
├── main.py                 # 训练入口：参数、训练循环、评估、结果记录
├── DGI.py                  # DeepGraphInfomax 类与模块度相关损失实现
├── model.py                # Encoder/Summarizer/corruption 等网络组件
├── evaluation.py           # NMI/ACC/F1/ARI/模块度Q等评估函数
├── utils/
│   ├── load_data.py        # 数据加载与类型转换
│   ├── data_processor.py   # 邻接归一化、图构建等通用处理
│   └── ...                 # 其它工具模块
├── datasets.zip            # 数据集压缩包（解压后按约定目录放置）
├── best_model.pkl          # 训练保存的最佳模型（运行后生成/覆盖）
├── result.csv              # 实验输出结果（运行后追加）
└── README.md
```

---

## 3. 环境依赖

建议 Python 3.8+，并安装以下核心依赖（可按需调整版本）：

- `torch`
- `torch_geometric`
- `numpy`
- `scikit-learn`
- `networkx`
- `scipy`
- `clusteval`
- `matplotlib`
- `openpyxl`

示例安装（仅供参考）：

```bash
pip install torch numpy scipy scikit-learn networkx matplotlib openpyxl clusteval
# torch_geometric 请按官方说明匹配你的 PyTorch/CUDA 版本进行安装
```

---

## 4. 数据准备

代码默认从 `./datasets/<dataset_name>/` 读取 3 个 `npy` 文件：

- `./datasets/<dataset_name>/<dataset_name>_feat.npy`
- `./datasets/<dataset_name>/<dataset_name>_label.npy`
- `./datasets/<dataset_name>/<dataset_name>_adj.npy`

例如当 `--dataset cora` 时，应存在：

```text
./datasets/cora/cora_feat.npy
./datasets/cora/cora_label.npy
./datasets/cora/cora_adj.npy
```

> 若仓库中已有 `datasets.zip`，请先解压并确认目录层级正确。

---

## 5. 快速开始

### 5.1 默认运行

```bash
python main.py
```

默认参数中数据集为 `cora`。

### 5.2 常用参数

```bash
python main.py \
  --dataset cora \
  --lr 0.001 \
  --hidden 512 \
  --clustertemp 30 \
  --seed 24
```

主要参数说明：

- `--dataset`：数据集名称（对应 `datasets/<name>/`）。
- `--lr`：学习率。
- `--hidden`：隐藏层维度。
- `--clustertemp`：softmax 温度（影响分配“硬度”）。
- `--seed`：随机种子。

---

## 6. 输出与结果

运行时会：

- 在控制台打印训练过程与中间评估；
- 将最佳模型保存到 `best_model.pkl`；
- 将实验结果追加写入 `result.csv`。

关键指标包括：

- `NMI`
- `ACC`
- `F1`
- `ARI`
- `DBI`
- `Q`（modularity）

---

## 7. 新人阅读建议（推荐顺序）

1. **先读 `main.py`**：理解完整训练链路（数据→结构社区→模型→评估）。
2. **再读 `DGI.py`**：重点看 `forward()` 与 `modularity()`。
3. **读 `model.py`**：理解编码器与聚类分配相关组件。
4. **读 `utils/load_data.py`**：确认数据格式与读取逻辑。
5. **读 `evaluation.py`**：理解每个指标的来源与计算方式。

如果你是第一次接触该仓库，建议先用默认参数跑通一次，再做超参数调整与消融实验。

---

## 8. 常见问题（FAQ）

### Q1: 运行报找不到数据文件？
通常是目录结构不匹配。请检查 `datasets/<dataset>/<dataset>_{feat,label,adj}.npy` 是否齐全且命名完全一致。

### Q2: 指标波动大？
请固定 `--seed`，并多次运行比较均值与方差。

### Q3: GPU 支持如何？
当前入口脚本主要按 CPU 路径组织；如需 GPU 训练，建议补充 device 迁移与张量/模型统一放置逻辑。

---

## 9. 引用

If you make use of this code in your work, please cite:

```bibtex
@misc{wang2025moresimpleeffectiveheuristic,
  title={Less is More: Simple yet Effective Heuristic Community Detection with Graph Convolution Network},
  author={Hong Wang and Yinglong Zhang and Zhangqi Zhao and Zhicong Cai and Xuewen Xia and Xing Xu},
  year={2025},
  eprint={2501.12946},
  archivePrefix={arXiv},
  primaryClass={cs.SI},
  url={https://arxiv.org/abs/2501.12946}
}
```
