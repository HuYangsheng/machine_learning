技术小结：

一、数据清洗

1、将缺失的数据标记为NaN：    利用```replace()```方法来实现
        
    data = data.replace( {-1:np.nan} )
    
2、删除缺失值可以使用```dropna(Thresh=thresh)```来设置删除缺失多少比例的数据。

3、删除类别型变量中的“异常字”可以使用```str.find()```来判断，并做删除操作

    data['UserInfo_8'] = [s[:-1] if s.find("市")>0 else s[:] for s in data.UserInfo_8]
    
4、```select_dtypes(['object'])```可以用来筛选DataFrame中的指定类别的数据

    df_obj = data.select_dtypes(['object'])
    
    data[df_obj.columns] = df_obj.apply(lambda x: x.str.strip())
  
二、特征工程

1、日期数据可以使用```pd.to_datetime()```来转换

2、利用```map()```和```lambda()```来高效实现特征的二值化

    data['is_1312'] = data['year_month'].map(lambda x : 1 if x == '2013-12' else 0)
    
3、利用xgboost来做特征选择：

- 首先将特征与标签组成数据集：df_1 = pd.concat([pd.get_dummies(data["UserInfo_2"]),data["target"]],axis = 1)
- 再用这个数据创建X_train、y_train，并训练XGBClassifier
- 接着通过```plot_importance()```来画出重要的特征：plot_importance(model_1, max_num_features=10, height=0.4)
- 最后使用```SelectFromModel()```来完成特征的选择
    * selection = SelectFromModel(xgb_clf, threshold=thresh, prefit=True)
    * X_train_selected = selection.transform(X_train)

4、pandas的独热编码```get_dummies(data)```可以指定data为具体的内容

    data_1 = pd.get_dummies(data[dummies_columns])
    
三、模型训练

1、```VotingClassifier()```是一个集成多个分类器进行投票的算法
