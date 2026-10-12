# siftup - PriorityQueue 核心原理动画演示与交互实验室

> 🌲 一款 100% 纯原生、零外部依赖的交互式算法动画系统，深入透视 JDK `java.util.PriorityQueue#siftUpComparable` 的空穴位移优化、二叉树与数组代数映射以及完全二叉堆运作机制。

🔗 **在线体验链接 (GitHub Pages)**: [https://lieeew.github.io/siftup/](https://lieeew.github.io/siftup/)

---

## ✨ 核心特性

- **严格还原 OpenJDK 官方逻辑（100% 正确性优先）**
  - 拒绝误导性的两两交换（swap）动画！
  - 完整呈现 JDK 经典的**“暂存 key 与空穴（Hole）单向覆盖”**优化，将数组写入操作减少近半。
- **多维动画实时联动**
  - **逻辑二叉堆（SVG 树形视图）**：动态计算节点层级与坐标，支持脉冲虚线空穴、发光比较目标与平滑连线。
  - **底层物理数组（Object[] queue）**：连续单元格排布，带 `↑ k` 与 `↑ parent` 动态指针指示。
  - **JDK 源码逐行追踪**：随执行状态点亮 Java 真实代码行（`while`、`(k - 1) >>> 1`、`compareTo`、`break`、`queue[k] = e`）。
  - **运行期变量看板**：实时计算 `k`、`key`、`parent`、`e` 以及比较布尔结果。
- **自由自定义数据与一键重试重放**
  - 支持在输入框中任意输入整型序列（支持逗号、空格或负数，如 `10, 4, 15, 2, 7`）。
  - **“🔄 重试当前数据”**：保留当前输入，一键清空并从第 0 步重新自动演示。
  - 内置 5 大经典场景芯片：`默认示例 [5, 1, 3, 8, 2]`、`逆序最大上浮`、`顺序无需上浮`、`含重复元素`、`含负数` 及 `🎲 随机序列`。
- **📐 数组与二叉树映射公式交互探针**
  - 点击树或数组中任意节点 `k`，即可实时查看其**父节点 `(k - 1) >>> 1`**、**左孩子 `2k + 1`**、**右孩子 `2k + 2`** 的动态代数推导过程与奇偶整除解析。
- **零网络依赖与极致性能**
  - 单文件 HTML5 + CSS3 + 原生 JavaScript + SVG，无需 Node.js 构建，离线双击即可毫秒级启动运行。

---

## 🚀 本地运行

该项目无需任何构建或打包工具：

```bash
# 克隆仓库
git clone https://github.com/lieeew/siftup.git

# 进入目录
cd siftup

# 直接使用浏览器打开
open index.html # macOS
# 或在 Windows 上使用 start index.html
```

---

## 📖 核心算法解析

### 1. JDK 源码片段

```java
private void siftUpComparable(int k, E x) {
    Comparable<? super E> key = (Comparable<? super E>) x;
    while (k > 0) {
        int parent = (k - 1) >>> 1;
        Object e = queue[parent];
        if (key.compareTo((E) e) >= 0)
            break;
        queue[k] = e;
        k = parent;
    }
    queue[k] = key;
}
```

### 2. 为什么计算父节点是 `(k - 1) >>> 1`？

在 0-based 数组中：
* 若节点是左孩子（奇数 $2p + 1$）：$(2p + 1 - 1) / 2 = p$
* 若节点是右孩子（偶数 $2p + 2$）：$(2p + 2 - 1) / 2 = (2p + 1) / 2 = p + 0.5$
  在整数除法或无符号右移 `>>> 1` 中，小数 $0.5$ 被直接舍弃，结果同样为 $p$！
  因此无须任何 `if-else` 分支，单条单周期位移指令即可秒解父节点。

---

## 📄 开源许可证

[MIT License](LICENSE)
