# Data-Analysis-Practice
## 初学者小白用于练习数据分析的文件存储
## 数据清理：

一.检查结构完整性：一行一个对象，一列一个变量，一个单元格一个元素

例子：用eval函数将字符串转为列表，再用explode将列表元素分为不同行

二.检查内容完整性：

例子：

1.用info函数检查数据的空缺值个数，以及数据的类型

2.改变应改变的数据类型（to_date转换为日期；astype（str）转换为字符串；astype（“category”）转化为分类数据等）

3.评估某变量的空缺值是否影响分析目的或者此空缺值的对象是否存在其他会影响分析目的的异常变量（条件筛选（query函数）出空缺值的对象并作进一步筛选）

4.用dropna来清除需清除的空缺值行（dropna（subset=”存在空缺值的变量”））

5.处理重复数据（.duplicated().sum()查看重复数据的个数；用.drop_duplicates()删除重复数据）

6.处理不一致数据（用.value_counts()看到可能意义相同但描述不同的不一致数据；用replace置换）

7.处理不合理数据（用.describe()查找是否存在不合理数据，条件筛选并适当修改删除）
  
## 数据整理：

一．根据需要，将不同表格合并或对表格的不同变量进行处理。
（concat（[],ignore_index=True，axis）：纵向/横向直接拼接；

merge（df1,df2,left_on="",right_on="",how=""）:合并dataframe；

df1.join(df2,how="",lsuffix="",rsuffix=""):根据索引合并）

二．用groupby函数根据需要分组

（df.groupby("")["  "].mean():只有完成最后的一步mean运算才能得到一个完整的dataframe，

也可以用reset_index()将分组表格变为正常dataframe）

（pd.pivot_table(df,index=[["",""]],columns="",values="",aggfunc=np.sum())是另外一种聚合函数）

三.对于数值数据，可以定义分组的范围（如根据年龄的不同区间进行分组）

（bins=[10,20,30,40,50,60,70,80,90]

labels=["儿童"，“少年”，………………]

pd.cut(df1["年龄"],bins，labels)

## 可视化处理

一.对于不同的自变量和因变量，可以善用不同的可视化函数

    Ⅰ.因变量为分类数据
    
    用饼状图或直方图描绘因变量的分布
    
    （df_counts=df["Survived"].value_counts()
    
    df_counts_index=df_counts.index
    
    plt.pie(df_counts,labels=df_counts_index,autopct="%.2f%%")
    
    plt.show()）
    
        1.自变量为分类数据
        
        用饼状图或直方图描绘自变量的分布，用countplot描绘自变量和因变量之间的关系
        （figure,axes=plt.subplots(1,2)
        
        axes[0].pie(cleaned_datas["Pclass"].value_counts(),labels=cleaned_datas["Pclass"].value_counts().index,autopct="%.2f%%")
        
        sns.countplot(cleaned_datas,x="Pclass",hue="Survived",ax=axes[1])
        
        plt.show()）
        
        2.自变量为数值数据
        
        先用histplot和boxplot描绘自变量的分布
       
        （figure,axes=plt.subplots(1,2)
        
        sns.histplot(cleaned_datas,x="Age",ax=axes[0])
        
        sns.boxplot(cleaned_datas,y="Age",ax=axes[1])
        
        plt.show()）
        
        ，再用histplot描绘自变量和因变量之间的关系
        
        （sns.histplot(cleaned_datas,x="Age",hue="Survived")
        
        plt.show()）
        
    Ⅱ.因变量为数值数据
    
    用histplot和barplot描绘因变量的分布
    
        Ⅰ.自变量为分类数据
        
        用饼状图或直方图描绘自变量的分布，用barplot描绘自变量和因变量之间的关系
        
        Ⅱ.自变量为数值数据
        
        先用histplot和boxplot描绘自变量的分布，再用scatterplot描绘自变量和因变量之间的关系
        
        （sns.scatterplot(cleaned_house,x="area",y="price")
        
        plt.show()）
        
二.可用.pairplot画出全部数值数据之间的两两关系

（sns.pairplot(cleaned_iri,hue="Species",kind="reg",plot_kws={"scatter_kws":{"alpha":0.4}})

plt.show()）

## 假设检验

先引入模块：from scipy.stats import ttest_ind 

一.建立假设

$H_0$:(如：A和B间长度不存在显著差异/A的长度显著不大于B的长度)

$H_1$:(如：A和B间长度存在显著差异/A的长度显著大于于B的长度)

二.确认单双尾

为双尾/单尾检验

三.确认显著水平

通常双尾为0.05，单尾为0.025

四.计算t值和p值

（t_stat,p_value=ttest_ind(setosa_datas["SepalLengthCm"],versicolor_datas["SepalLengthCm"])

print(t_stat,p_value)）

五.若t值大于t值临界值（根据样本数量查表），或p值小于t值临界值，则拒绝原假设

## 线性回归

一.import statsmodels.api as sm 引入模块(线性回归和逻辑回归都是用这个模块，z检验也是这个模块)

二.先复制一份之前已经处理完的干净数据，用来推算模型，然后删掉明显对因变量不可能起到作用的自变量

三.用pd.get_dummies来将分类数据转换为虚拟变量

（lr_house_price=pd.get_dummies(lr_house_price,drop_first=True,columns=["mainroad","guestroom","basement","hotwaterheating",

                                                       "airconditioning","prefarea","furnishingstatus"],dtype=int)）

四.设立y和x，y为因变量，x为自变量

五.对x用corr函数检测各变量之间的相关性，一般认为0.8及以上为强相关

可以用热力图，更加直观

（sns.heatmap(x.corr().abs())

plt.show()）

六.删掉相关性高的变量，保留一个，并用x=sm.add_constant(x)添加截距

七.建立模型：

（model=sm.OLS(y,x).fit()

model.summary()）为采用最大二乘法拟合的线性回归模型

（model=sm.Logit(y,x).fit()

model.summary()）为采用最大似然估计拟合的逻辑回归模型

八.观察P>|z|及P>|t|项，和采用的显著水平作比较（通常采用0.05），若大于这个值说明此变量对因变量影响不大，删去后重新建立模型

九.对于测试集，同样需要先清洗数据，如填充缺失值等，并对于某个分类数据在测试集样本中可能没有出现所有可能的情况，采用函数

（titanic_test["Sex"]=pd.Categorical(titanic_test["Sex"],categories=['male','female'])），

来说明分类数据的所有可能性，并引入虚拟变量，随后添加截距

十.采用model.predict对测试集进行预测


