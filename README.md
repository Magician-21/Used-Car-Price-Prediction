# 二手车价格预测（天池比赛）  
**基于LightGBM的结构化数据建模方案**


## 项目简介  
本项目是阿里云天池「二手车价格预测」比赛的解决方案，通过数据清洗、特征工程和LightGBM模型实现价格预测。核心流程包括缺失值处理、时间特征构造、类别特征编码及五折交叉验证，最终生成符合要求的提交结果。


## 技术栈  
- **数据处理**：Pandas、NumPy  
- **可视化**：Matplotlib、Seaborn  
- **特征工程**：LabelEncoder、StandardScaler  
- **建模**：LightGBM、Scikit-learn（KFold）  
- **开发环境**：Jupyter Notebook  


## 数据处理流程  
1. **缺失值处理**  
   - **类别特征**：`bodyType`/`fuelType`/`gearbox` 用众数填充  
   - **数值特征**：`power` 用中位数填充，`kilometer` 用均值填充  
   - **特殊符号**：`notRepairedDamage` 中的 `-` 转换为`NaN`后填充为`-1`  

2. **日期特征处理**  
   - 将`regDate`和`creatDate`转换为日期格式，无效值用众数填充  
   - 构造**车龄特征**：`car_age = (创建年份-注册年份) + (创建月份-注册月份)/12`  

3. **异常值处理**  
   - `power` 截断到 `[0, 600]`，过滤明显不合理值  


## 特征工程  
1. **类别特征编码**  
   - **高频类别编码**：对`brand`/`regionCode`计算出现频率，生成新特征`*_freq`  
   - **标签编码**：`notRepairedDamage` 转换为数值类别  

2. **数值特征分箱**  
   - `kilometer` 分箱为10个区间，生成`kilometer_bin`  

3. **匿名特征处理**  
   - 对`v_0`到`v_14`进行标准化（StandardScaler）  


## 模型训练  
- **算法选择**：LightGBM（高效的梯度提升框架，适合结构化数据）  
- **目标函数**：回归任务（价格对数转换后优化MAE）  
- **交叉验证**：5折分层交叉验证（KFold），防止过拟合  
- **关键参数**：  
  ```python  
  params = {  
      'objective': 'regression',  
      'metric': 'mae',  
      'learning_rate': 0.01,  
      'num_leaves': 31,  # 控制树复杂度  
      'feature_fraction': 0.8,  # 特征随机抽样  
      'lambda_l1': 0.1,  # L1正则化  
      'verbosity': -1  # 静默模式  
  }  
  ```  
- **结果**：交叉验证平均MAE约为 `0.18`（具体值以实际运行为准）  


## 结果展示  
1. **特征重要性**  
   ![特征重要性图](feature_importance.png)  
   *（前20重要特征，包含车龄、品牌频率、行驶里程等）*

2. **提交文件**  
   生成的预测结果存储于`submission.csv`，格式为：  
   | SaleID | price |  
   |--------|-------|  
   | 123    | 89000 |  


## 如何运行  
### 1. 环境配置  
```bash  
# 安装依赖  
pip install pandas numpy matplotlib seaborn lightgbm scikit-learn  
```  

### 2. 数据准备  
- 从天池比赛页面下载训练集和测试集：  
  - 训练集：[used_car_train_20200313.csv](https://tianchi.aliyun.com/competition/entrance/231784/rankingList)  
  - 测试集：[used_car_testB_20200421.csv](https://tianchi.aliyun.com/competition/entrance/231784/rankingList)  
- 将文件放入项目根目录，确保路径正确：  
  ```  
  project/  
  ├─ data/  
  │  ├─ used_car_train_20200313.csv  
  │  └─ used_car_testB_20200421.csv  
  └─ code/  
     └─ main.py  # 主程序  
  ```  

### 3. 运行代码  
```bash  
python main.py  
```  
- 输出结果：  
  - 控制台显示交叉验证过程和MAE值  
  - 生成`submission.csv`（位于项目根目录）  
  - 生成`feature_importance.png`（特征重要性图）  


## 文件结构  
```  
.  
├─ data/                # 原始数据  
│  ├─ used_car_train_20200313.csv  
│  └─ used_car_testB_20200421.csv  
├─ code/                # 代码文件  
│  └─ main.py           # 主程序（数据处理+建模）  
├─ submission.csv       # 预测结果  
├─ feature_importance.png  # 特征重要性图  
└─ README.md            # 项目说明  
```  


## 贡献与反馈  
欢迎提出Issue或PR优化模型：  
- 改进特征工程（如添加交互特征）  
- 调整模型参数（如尝试XGBoost/LSTM）  
- 优化数据预处理逻辑  

联系方式：2246817895@qq.com 




---  
**天池比赛链接**：[https://tianchi.aliyun.com/competition/entrance/231784/rankingList](https://tianchi.aliyun.com/competition/entrance/231784/rankingList)  
**GitHub仓库**：[(https://github.com/Magician-21/Used-Car-Price-Prediction)]
