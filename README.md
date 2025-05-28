# CarPriceMaster [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) ![Python 3.7+](https://img.shields.io/badge/Python-3.7%2B-blue)

天池二手车价格预测竞赛Top 5%解决方案，基于LightGBM的全流程优化实现。

![Feature Importance](feature_importance.png)

## 目录
- [项目亮点](#✨-项目亮点)
- [数据管道](#-数据管道)
- [模型架构](#-模型架构)
- [快速部署](#-快速部署)
- [竞赛成绩](#-竞赛成绩)
- [开发者指南](#-开发者指南)

---

## ✨ 项目亮点
### 工业级数据处理
- **时空特征挖掘**：从注册日期提取车龄、季节特征（`car_age`，`reg_season`）
- **对抗式异常处理**：对功率(power)进行截断修正（>600→600，<0→0）
- **智能缺失填充**：动态选择众数/中位数策略（见下表）

| 特征             | 填充策略          | 代码函数              |
|------------------|-------------------|-----------------------|
| gearbox          | 训练/测试集独立众数 | `gearbox_fill()`      |
| notRepairedDamage | 映射为特殊类别(-1) | `notRepairedDamage_fill()` |
| power            | 中位数+异常修正    | `power_fill()`        |

### 高鲁棒性建模
```python
# 创新点：自适应验证策略
params = {
    'metric': 'mae',
    'num_leaves': 63,          # 更深的树结构
    'lambda_l1': 0.2,          # 强正则化防止过拟合
    'boosting': 'dart',        # 采用Dropout技术
    'path_smooth': 50          # 优化类别特征处理
}
