# IBP Sales Forecasting Documentation

## Tujuan Projek

1. Forecast sales dengan timeframe weekly
2. Allow user untuk memilih value-value pada beberapa metrik untuk melihat pengaruhnya terhadap sales

## Data yang Digunakan

- Data traffic diambil dari mixpanel dengan column:
    - event_date
    - session_start_count — Jumlah Traffic
- Data sales dan metric diambil dari dataset ‘ (ATH) Sales Order Main Dataset V2’ di quicksight
    - Column yang diambil:
        - order_date
        - sales_total — Jumlah Sales
        - total_order — Jumlah order
        - qty — Jumlah produk terjual
- Granularity yang digunakan adalah Daily

## Library yang digunakan — in requirements.txt

- numpy
- pandas
- plotly
- jdata
- scikit-learn
- json
- matplotlib
- pystan==2.19.1.1
- prophet
- dash

## Model yang digunakan

- Model yang digunakan adalah Prophet dengan regressors yang berupa metrik-metrik yang akan dimanipulasi
- Metrik-Metrik yang digunakan adalah:
    - Jumlah order
    - Jumlah produk terjual
    - Jumlah traffic
    - Average Order Value (AOV)
    - Jumlah Active Customer

## Output

- Output dari projek ini adalah sebuah dash app yang menunjukkan:
    - Grafik hasil forecast dengan default values
    - Grafik data aktual
    - Grafik target
    - Grafik hasil forecast dengan values yang dimanipulasi user
    - RMSE, MAE , MAPE, Actual Difference, dan Percent Difference yang didapat dari dilakukannya testing pada data minggu lalu
    - Total Sales untuk Minggu depan

INSERT GAMBAR

## Project Flow

1. Wrangling and Preprocessing
    1. Get the training dataset and the testing dataset (updated weekly from s3)
    2. Rename column sesuai dengan kaidah penamaan prophet
    
    ```python
    def wrangle(path):
        df_tmp = pd.read_csv(path)
        df_tmp['order_date'] =  pd.to_datetime(df_tmp['order_date'])
        df_tmp.sort_values(by='order_date')
        df_tmp.reset_index(drop=True)
        df_tmp.rename(columns = {'order_date':'ds', 'sales_total':'y'}, inplace = True)
        return df_tmp
    ```
    
    **Parameters:**
    
    **path**    :    ***str, path object or file-like object***
    
    path untuk sebuah file csv
    
    c. Menggabungkan training and testing dataset untuk menghasilkan forecasting dataset
    
     
    
    ```python
    def append_df(df1,df2):
        df3 = df1.append(df2, ignore_index=True)
        return df3
    ```
    
    **Parameters:**
    
    **df1**    :    ***Pandas Dataframe***
    
    dataset training
    
    **df2    :    *Pandas Dataframe***
    
    dataset testing