技术小结:

一、数据预处理

1、可以用Decimal来保留小数点后六位

    Decimal(x).quantize(Decimal('0.000000'))
    
2、describe()可以进行指定显示内容

    data_df[site_features].describe()
    
3、画堆叠柱状图使用两个bar
    
    plt.bar(range(len(x)), y_0, width=0.5, color='red', label=0)
    plt.bar(range(len(x)), y_1, width=0.5, color='blue', label=1, bottom=y_0)
    第二个bar中加入bottom指定上一个显示的数据。
    
4、plt.legend(title='click')，title参数可以给图例加标题

5、plt.xticks(rotation=90) # 设置横坐标标签旋转角度。

6、iloc[]可以在里面添加条件进行筛选赋值

    data_df.loc[data_df['hour']==a, 'hour'] = 0
    
7、对变量进行特征编码

    data_df_hour = data_df['hour'].apply(str).str.get_dummies().add_prefix('hour:')
    # 首先转成str类型，之后调用get_dummies()方法进行独热编码
    
二、特征工程

1、利用SelectFromModel()来选择特征

    lr_clf = LogisticRegression(penalty='l1', C=c_best)
    lr_clf.fit(X_train, y_train) # 在整个训练数据重新训练
    select_model = SelectFromModel(lr_clf, prefit=True)
    selected_features = select_model.get_support()  # 被选出来的特征 
    feature_names = feature_names[selected_features]  # 重新构造feature_names
    # 重新构造训练数据和测试数据
    X_train = X_train[:, selected_features]
    X_test = X_test[:, selected_features]
    
三、模型训练

