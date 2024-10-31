# **DEPI Final Project** 


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## Data Handling Class


```python
class DataHandling:
    def __init__ (self,filepath,encoding='UTF-8'):
        self.df=pd.read_csv(filepath, delimiter=';',encoding=encoding)
    
    
    def Dataexplore(self):
        print(f"data summary is:\n\n {self.df.info()}\n")
        print(f"data statistics are :\n\n{self.df.describe()} \n")
        print(f"null values are:\n\n{self.df.isna().sum()}\n")

    def Fixdtype(self):
        self.df["Order Date"]=pd.to_datetime(self.df["Order Date"],dayfirst=True)
        self.df["Ship Date"]=pd.to_datetime(self.df["Ship Date"],dayfirst=True)
    
    
    
    def Handlenulls(self):
        th=0.7*len(self.df)
        self.df.dropna(thresh=th,axis=1,inplace=True)
        for col in self.df.columns: 
            null_perc=((self.df[col].isna().sum())/len(self.df))*100 
            if 0<null_perc<=3:
                self.df.dropna(subset=[col],inplace=True,axis=0) 
            elif 3<null_perc<70:
                if self.df[col].dtype in['float64', 'int64']:
                    self.df[col].fillna(self.df[col].median(),inplace=True) 
                else:
                    self.df[col].fillna(self.df[col].mode()[0],inplace=True)
    
    
    def Handle_dup(self):
        self.df.drop_duplicates(inplace=True)
     
     
    
    def Handleoutliers(self):
        for col in self.df.columns:
            if self.df[col].dtype in['float64','int64']: 
                q1=self.df[col].quantile(0.25)
                q3=self.df[col].quantile(0.75) 
                IQR=q3-q1
                lower=q1-1.5*IQR 
                upper=q3+1.5*IQR 
                self.df=self.df[(self.df[col]>=lower)&(self.df[col]<=upper)]

    
    def Histograms(self,column,figsize=(10,6)):
            if column not in self.df.columns:
                raise ValueError(f"Column '{column}' is not present in the DataFrame.")
        
            if self.df[column].dtype in['float64','int64']:
                plt.figure(figsize=figsize)
                sns.histplot(self.df[column])
                plt.show()

            else:
                raise TypeError(f"Column '{column}' is not numerical.")

    
    def Cat_visual(self,column,plot_type='bar',figsize=(10,6)):
            plt.figure(figsize=figsize)
            if column not in self.df.columns:
                raise ValueError(f"Column '{column}' is not present in the DataFrame.")
            if plot_type=='bar':
                sns.barplot(x=self.df[column].value_counts().index, 
                        y=self.df[column].value_counts().values)
                plt.xlabel(column)
                plt.ylabel('Count')
            elif plot_type=='pie':
                plt.pie(self.df[column].value_counts().values, labels=self.df[column].value_counts().index, autopct='%1.1f%%',startangle=90)
                plt.axis('equal')
                plt.title(column)
                plt.show()

            else:
                raise ValueError(f"Plot type '{plot_type}' is not supported. Use 'pie' or 'bar'.")

    
    def boxplot(self,column,figsize=(10,6)):
            plt.figure(figsize=figsize)
            if column not in self.df.columns:
                raise ValueError(f"Column '{column}' is not present in the DataFrame.")
            sns.boxplot(x=self.df[column])

    def Cleaned(self):
        df.Handlenulls()
        df.Handle_dup()
        df.Handleoutliers()
        return self.df
```


```python
df=DataHandling("D:\Depi\EU-Dataset\Orders.csv")
```


```python
df.Fixdtype()
```


```python
orders=df.Cleaned()
```

## Orders Table


```python
orders
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Row ID</th>
      <th>Order ID</th>
      <th>Order Date</th>
      <th>Ship Date</th>
      <th>Ship Mode</th>
      <th>Customer ID</th>
      <th>Segment</th>
      <th>Postal Code</th>
      <th>Product ID</th>
      <th>Sales</th>
      <th>Quantity</th>
      <th>Discount</th>
      <th>Profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>CA-2022-152156</td>
      <td>2022-11-08</td>
      <td>2022-11-11</td>
      <td>Second Class</td>
      <td>CG-12520</td>
      <td>Consumer</td>
      <td>42420</td>
      <td>FUR-BO-10001798</td>
      <td>261,96</td>
      <td>2</td>
      <td>0</td>
      <td>41,9136</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>CA-2022-152156</td>
      <td>2022-11-08</td>
      <td>2022-11-11</td>
      <td>Second Class</td>
      <td>CG-12520</td>
      <td>Consumer</td>
      <td>42420</td>
      <td>FUR-CH-10000454</td>
      <td>731,94</td>
      <td>3</td>
      <td>0</td>
      <td>219,582</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>CA-2022-138688</td>
      <td>2022-06-12</td>
      <td>2022-06-16</td>
      <td>Second Class</td>
      <td>DV-13045</td>
      <td>Corporate</td>
      <td>90036</td>
      <td>OFF-LA-10000240</td>
      <td>14,62</td>
      <td>2</td>
      <td>0</td>
      <td>6,8714</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>US-2021-108966</td>
      <td>2021-10-11</td>
      <td>2021-10-18</td>
      <td>Standard Class</td>
      <td>SO-20335</td>
      <td>Consumer</td>
      <td>33311</td>
      <td>FUR-TA-10000577</td>
      <td>957,5775</td>
      <td>5</td>
      <td>0,45</td>
      <td>-383,031</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>US-2021-108966</td>
      <td>2021-10-11</td>
      <td>2021-10-18</td>
      <td>Standard Class</td>
      <td>SO-20335</td>
      <td>Consumer</td>
      <td>33311</td>
      <td>OFF-ST-10000760</td>
      <td>22,368</td>
      <td>2</td>
      <td>0,2</td>
      <td>2,5164</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9989</th>
      <td>9990</td>
      <td>CA-2020-110422</td>
      <td>2020-01-21</td>
      <td>2020-01-23</td>
      <td>Second Class</td>
      <td>TB-21400</td>
      <td>Consumer</td>
      <td>33180</td>
      <td>FUR-FU-10001889</td>
      <td>25,248</td>
      <td>3</td>
      <td>0,2</td>
      <td>4,1028</td>
    </tr>
    <tr>
      <th>9990</th>
      <td>9991</td>
      <td>CA-2023-121258</td>
      <td>2023-02-26</td>
      <td>2023-03-03</td>
      <td>Standard Class</td>
      <td>DB-13060</td>
      <td>Consumer</td>
      <td>92627</td>
      <td>FUR-FU-10000747</td>
      <td>91,96</td>
      <td>2</td>
      <td>0</td>
      <td>15,6332</td>
    </tr>
    <tr>
      <th>9991</th>
      <td>9992</td>
      <td>CA-2023-121258</td>
      <td>2023-02-26</td>
      <td>2023-03-03</td>
      <td>Standard Class</td>
      <td>DB-13060</td>
      <td>Consumer</td>
      <td>92627</td>
      <td>TEC-PH-10003645</td>
      <td>258,576</td>
      <td>2</td>
      <td>0,2</td>
      <td>19,3932</td>
    </tr>
    <tr>
      <th>9992</th>
      <td>9993</td>
      <td>CA-2023-121258</td>
      <td>2023-02-26</td>
      <td>2023-03-03</td>
      <td>Standard Class</td>
      <td>DB-13060</td>
      <td>Consumer</td>
      <td>92627</td>
      <td>OFF-PA-10004041</td>
      <td>29,6</td>
      <td>4</td>
      <td>0</td>
      <td>13,32</td>
    </tr>
    <tr>
      <th>9993</th>
      <td>9994</td>
      <td>CA-2023-119914</td>
      <td>2023-05-04</td>
      <td>2023-05-09</td>
      <td>Second Class</td>
      <td>CC-12220</td>
      <td>Consumer</td>
      <td>92683</td>
      <td>OFF-AP-10002684</td>
      <td>243,16</td>
      <td>2</td>
      <td>0</td>
      <td>72,948</td>
    </tr>
  </tbody>
</table>
<p>9824 rows × 13 columns</p>
</div>




```python
orders.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 9824 entries, 0 to 9993
    Data columns (total 13 columns):
     #   Column       Non-Null Count  Dtype         
    ---  ------       --------------  -----         
     0   Row ID       9824 non-null   int64         
     1   Order ID     9824 non-null   object        
     2   Order Date   9824 non-null   datetime64[ns]
     3   Ship Date    9824 non-null   datetime64[ns]
     4   Ship Mode    9824 non-null   object        
     5   Customer ID  9824 non-null   object        
     6   Segment      9824 non-null   object        
     7   Postal Code  9824 non-null   int64         
     8   Product ID   9824 non-null   object        
     9   Sales        9824 non-null   object        
     10  Quantity     9824 non-null   int64         
     11  Discount     9824 non-null   object        
     12  Profit       9824 non-null   object        
    dtypes: datetime64[ns](2), int64(3), object(8)
    memory usage: 1.0+ MB
    


```python
orders.to_csv(r"D:\Depi\Cleaned\orders.csv",index=False)
```


```python
df=DataHandling("D:\Depi\EU-Dataset\Products.csv",encoding='ISO-8859-1')
```


```python
products=df.Cleaned()
```

## Products Table


```python
products
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Product ID</th>
      <th>Category</th>
      <th>Sub-Category</th>
      <th>Product Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>FUR-BO-10000112</td>
      <td>Furniture</td>
      <td>Bookcases</td>
      <td>Bush Birmingham Collection Bookcase, Dark Cherry</td>
    </tr>
    <tr>
      <th>1</th>
      <td>FUR-BO-10000330</td>
      <td>Furniture</td>
      <td>Bookcases</td>
      <td>Sauder Camden County Barrister Bookcase, Plank...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>FUR-BO-10000362</td>
      <td>Furniture</td>
      <td>Bookcases</td>
      <td>Sauder Inglewood Library Bookcases</td>
    </tr>
    <tr>
      <th>3</th>
      <td>FUR-BO-10000468</td>
      <td>Furniture</td>
      <td>Bookcases</td>
      <td>O'Sullivan 2-Shelf Heavy-Duty Bookcases</td>
    </tr>
    <tr>
      <th>4</th>
      <td>FUR-BO-10000711</td>
      <td>Furniture</td>
      <td>Bookcases</td>
      <td>Hon Metal Bookcases, Gray</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1889</th>
      <td>TEC-PH-10004912</td>
      <td>Technology</td>
      <td>Phones</td>
      <td>Cisco SPA112 2 Port Phone Adapter</td>
    </tr>
    <tr>
      <th>1890</th>
      <td>TEC-PH-10004922</td>
      <td>Technology</td>
      <td>Phones</td>
      <td>RCA Visys Integrated PBX 8-Line Router</td>
    </tr>
    <tr>
      <th>1891</th>
      <td>TEC-PH-10004924</td>
      <td>Technology</td>
      <td>Phones</td>
      <td>SKILCRAFT Telephone Shoulder Rest, 2" x 6.5" x...</td>
    </tr>
    <tr>
      <th>1892</th>
      <td>TEC-PH-10004959</td>
      <td>Technology</td>
      <td>Phones</td>
      <td>Classic Ivory AntiqueTelephoneZL1810</td>
    </tr>
    <tr>
      <th>1893</th>
      <td>TEC-PH-10004977</td>
      <td>Technology</td>
      <td>Phones</td>
      <td>GE 30524EE4</td>
    </tr>
  </tbody>
</table>
<p>1894 rows × 4 columns</p>
</div>




```python
products.to_csv(r"D:\Depi\Cleaned\products.csv",index=False)
```


```python
df=DataHandling("D:\Depi\EU-Dataset\Location.csv")
```


```python
locations=df.Cleaned()
```

## Locations Table


```python
locations
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>City</th>
      <th>State</th>
      <th>Region</th>
      <th>Country/Region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>76106.0</td>
      <td>Fort Worth</td>
      <td>Texas</td>
      <td>Central</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>1</th>
      <td>53711.0</td>
      <td>Madison</td>
      <td>Wisconsin</td>
      <td>Central</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>2</th>
      <td>68025.0</td>
      <td>Fremont</td>
      <td>Nebraska</td>
      <td>Central</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>3</th>
      <td>77095.0</td>
      <td>Houston</td>
      <td>Texas</td>
      <td>Central</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>4</th>
      <td>75080.0</td>
      <td>Richardson</td>
      <td>Texas</td>
      <td>Central</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>627</th>
      <td>83301.0</td>
      <td>Twin Falls</td>
      <td>Idaho</td>
      <td>West</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>628</th>
      <td>92672.0</td>
      <td>San Clemente</td>
      <td>California</td>
      <td>West</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>629</th>
      <td>94568.0</td>
      <td>Dublin</td>
      <td>California</td>
      <td>West</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>630</th>
      <td>93405.0</td>
      <td>San Luis Obispo</td>
      <td>California</td>
      <td>West</td>
      <td>United States</td>
    </tr>
    <tr>
      <th>631</th>
      <td>95240.0</td>
      <td>Lodi</td>
      <td>California</td>
      <td>West</td>
      <td>United States</td>
    </tr>
  </tbody>
</table>
<p>631 rows × 5 columns</p>
</div>




```python
locations.to_csv(r"D:\Depi\Cleaned\locations.csv",index=False)
```


```python
df=DataHandling("D:\Depi\EU-Dataset\Customers.csv",encoding='ISO-8859-1')
```


```python
customers=df.Cleaned()
```

## Customers Table


```python
customers
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Customer ID</th>
      <th>Customer Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AA-10315</td>
      <td>Alex Avila</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AA-10375</td>
      <td>Allen Armold</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AA-10480</td>
      <td>Andrew Allen</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AA-10645</td>
      <td>Anna Andreadi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>AB-10015</td>
      <td>Aaron Bergman</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>788</th>
      <td>XP-21865</td>
      <td>Xylona Preis</td>
    </tr>
    <tr>
      <th>789</th>
      <td>YC-21895</td>
      <td>Yoseph Carroll</td>
    </tr>
    <tr>
      <th>790</th>
      <td>YS-21880</td>
      <td>Yana Sorensen</td>
    </tr>
    <tr>
      <th>791</th>
      <td>ZC-21910</td>
      <td>Zuschuss Carroll</td>
    </tr>
    <tr>
      <th>792</th>
      <td>ZD-21925</td>
      <td>Zuschuss Donatelli</td>
    </tr>
  </tbody>
</table>
<p>793 rows × 2 columns</p>
</div>




```python
customers.to_csv(r"D:\Depi\Cleaned\customers.csv",index=False)
```


```python

```


```python

```
