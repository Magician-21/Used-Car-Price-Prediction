# Used-Car-Price-Prediction [![License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

天池竞赛二手车价格预测项目，基于LightGBM的完整机器学习解决方案

## 项目概述
通过特征工程与LightGBM建模，预测二手汽车交易价格。该项目完整实现：
✅ 数据清洗与缺失值处理  
✅ 时间序列特征解析（注册日期/创建日期）  
✅ 高频类别编码与异常值处理  
✅ 特征标准化与匿名特征处理  
✅ 5折交叉验证与模型调优

## 数据准备
### 数据集说明
- 训练集：`used_car_train_20200313.csv`（15万条）
- 测试集：`used_car_testB_20200421.csv`（5万条）
- 字段包含：30+特征（品牌/地区/功率/变速箱类型等）

### 缺失值处理策略[7](@ref)
| 特征             | 处理方式                |
|------------------|-------------------------|
| gearbox          | 众数填充（mode）        | 
| notRepairedDamage | 替换"-"为NaN后特殊编码 |
| power            | 中位数填充              |
| bodyType         | 高频类别填充            |

## 模型训练
### 核心流程
```python
# 关键模型参数（lgb_model函数）
params = {
    'boosting_type': 'gbdt',
    'objective': 'regression',
    'metric': 'mae',
    'learning_rate': 0.01,
    'num_leaves': 31,
    'min_data_in_leaf': 20
}

# 5折交叉验证流程
folds = KFold(n_splits=5, shuffle=True)
