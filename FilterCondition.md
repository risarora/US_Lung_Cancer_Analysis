#Filter Pandas Dataframe By Values of Column
```
TobacoUsage_pd[TobacoUsage_pd['YEAR']==2012]
```


#Get Unique Values from a Column in Pandas Data Frame

```
TobacoUsage_pd['YEAR'].unique().tolist()

['2014-2015',
 '2015',
 '2016',
 '2013-2014',
 '2012-2013',
 '2011-2012',
 '2014',
 '2013',
 '2012',
 '2011',
 '2015-2016']
 ```


##### Get Frequency Counts of a Column

```
TobacoUsage_pd['YEAR'].value_counts()


2012         2124
2014         2124
2013         2124
2016         2124
2015         2124
2011         2124
2014-2015     265
2015-2016     265
2011-2012     265
2013-2014     265
2012-2013     265
Name: YEAR, dtype: int64

```


##### Remove rows whose columns value count is less than 265
##### OR
##### Select rows whose columns value count is greater than 265

Selected = TobacoUsage_pd['YEAR'].value_counts()[TobacoUsage_pd['YEAR'].value_counts()>265]

Selected
```
2012    2124
2014    2124
2013    2124
2016    2124
2015    2124
2011    2124
Name: YEAR, dtype: int64
```

retainList = Selected.index.tolist()
retainList
```
['2012', '2014', '2013', '2016', '2015', '2011']
```

TobacoUsage_pd_Yearonly = TobacoUsage_pd[TobacoUsage_pd.YEAR.isin(deleteList)]


##### TobacoUsage_pd_Yearonly.head()

```
TobacoUsage_pd_Yearonly = TobacoUsage_pd[TobacoUsage_pd.YEAR.isin(TobacoUsage_pd['YEAR'].value_counts()[TobacoUsage_pd['YEAR'].value_counts()>265].index.tolist())]

TobacoUsage_pd_Yearonly['YEAR'].value_counts()
```


#### Get Frequency Counts of a Column
```
TobacoUsage_pd_Yearonly = TobacoUsage_pd[TobacoUsage_pd.YEAR.isin(TobacoUsage_pd['YEAR'].value_counts()[TobacoUsage_pd['YEAR'].value_counts()>265].index.tolist())]
```

#### Filter Based on two columns
**males = df[(df[Gender]=='Male') & (df[Year]==2014)]**

```
TobacoUsage_pd_Yearonly[(TobacoUsage_pd_Yearonly['LocationAbbr']=='AL' ) & (TobacoUsage_pd_Yearonly['YEAR']=='2016')]
```



TobacoUsage_pd['YEAR'].value_counts()



#### List Columns Names

```
list(TobacoSuperGroups)

['YEAR',
 'LocationAbbr',
 'LocationDesc',
 'TopicType',
 'TopicDesc',
 'MeasureDesc',
 'DataSource',
 'Response',
 'Data_Value_Unit',
 'Data_Value_Type',
 'Data_Value',
 'Data_Value_Footnote_Symbol',
 'Data_Value_Footnote',
 'Data_Value_Std_Err',
 'Low_Confidence_Limit',
 'High_Confidence_Limit']
 ```


##### Drop Columns based on Names

df.drop(['col_name_1', 'col_name_2', ..., 'col_name_N'], inplace=True, axis=1, errors='ignore')

```
TobacoSuperGroups = TobacoSuperGroups.drop()[ 'Sample_Size',
  'Gender',
  'Race',
  'Age',
  'Education',
  'GeoLocation',
  'TopicTypeId',
  'TopicId',
  'MeasureId',
  'StratificationID1',
  'StratificationID2',
  'StratificationID3',
  'StratificationID4',
  'SubMeasureID',
  'DisplayOrder'], inplace=True, axis=1, errors='ignore')
```

#####  Combining DataFrames with Concatenation
You can concatenate rows or columns together, the only requirement is that the shape is the same on corresponding axis.
To concat rows vertically:
```  
pd.concat([df_1, df_2], axis=0)
```
  Or
  to concat columns horizontally:
```
  pd.concat([df_1, df_2], axis=1)
```


TBD :  Pandas Merge

https://www.kaggle.com/crawford/python-merge-tutorial







http://leportella.com/cheatlist/2018/05/20/models-cheat-list.html
