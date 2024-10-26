# FINAL PROJECT MYSKILL DATA ANALYSIS ECOMMERCE

## PROJECT OVERVIEW
This repository contains a data analysis project utilizing SQL, Python, and Looker Studio to gain meaningful insights from business data. The project is divided into three main parts:
- **SQL Analysis**: This section involves querying databases to extract, clean, and analyze data. Using SQL, we explored the data to uncover trends and patterns, such as sales performance across different periods, which provided foundational insights for further analysis.
- **Python Data Processing and Visualization**: In this part, Python was employed to automate data processing tasks and create visual representations. Leveraging libraries like Pandas, Matplotlib, and Seaborn, we cleaned and transformed data, conducted exploratory data analysis (EDA), and generated visualizations that highlighted key metrics and trends.
- **Looker Studio Dashboard**: Finally, we used Looker Studio to build a comprehensive, interactive dashboard for data visualization. This dashboard consolidates metrics like sales, profit, and customer counts by category and payment method, providing a clear and interactive view of the business data for stakeholders.

Each tool plays a vital role in the project, with SQL handling data extraction, Python enabling data manipulation and visualization, and Looker Studio bringing it all together in a dashboard format. This project showcases an end-to-end approach to data analytics, starting from raw data extraction to interactive data presentation.

## TOOLS USED
This project leverages various tools and technologies across data extraction, analysis, and visualization stages:
- **PostgreSQL (SQL)**: Used for querying and extracting data from the relational database. PostgreSQL was chosen for its robustness in handling complex queries and aggregations, allowing efficient data manipulation and retrieval to explore sales trends, customer behaviors, and other key metrics.
- **Google Colab and Jupyter Notebook (Python)**: Python was used for data cleaning, transformation, and exploratory data analysis (EDA).
  - **Google Colab**: This cloud-based platform allows you to run Python code without local setup, making it easier to share notebooks and collaborate.
  - **Jupyter Notebook**: Used for local analysis and testing. Jupyter offers a flexible environment to combine code, outputs, and markdown documentation, making the analysis process both intuitive and reproducible.
- **Google Sheets**: Used for lightweight data cleaning, manipulation, and preliminary visualizations. Google Sheets provided a quick way to manage and organize data, especially when making minor adjustments or calculations before importing into other tools.
- **Looker Studio**: Google Looker Studio (formerly Data Studio) was used to build an interactive dashboard for visualizing key insights. It offers powerful, customizable visualizations and supports multiple data sources, making it ideal for creating a dynamic reporting interface for stakeholders. Looker Studio allowed the integration of various dimensions and metrics to provide an intuitive, user-friendly dashboard experience.

Each tool played a unique role in facilitating the end-to-end data analysis process, from data extraction to final reporting and visualization. Together, they provided a comprehensive solution for understanding and presenting business insights effectively.

## PROJECT HIGHLIGHTS
### 1. DATA ANALYSIS USING SQL IN POSTGRESQL
For detail explanation in Bahasa, you can check here :
[FINAL PROJECT SQL MYSKILL - MOCHAMAD SYAHRIZAL.pdf](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_SQL/FINAL%20PROJECT%20SQL%20MYSKILL%20-%20MOCHAMAD%20SYAHRIZAL.pdf)

And for full query you can check here :
[SQL Query File](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_SQL/query%20exercise%20%20finpro%20sql.sql)

#### QUESTIONS
1. In 2021, in which month was the highest total transaction value (after_discount) recorded? Use is_valid = 1 to filter transactions.
Source: order_detail
2. In 2022, which category generated the highest transaction value? Use is_valid = 1 to filter transactions.
Source: order_detail, sku_detail
3. Compare transaction values for each category in 2021 and 2022. Identify categories with increased or decreased transaction values from 2021 to 2022. Use is_valid = 1 to filter transactions.
Source: order_detail, sku_detail
4. Show the top 5 most popular payment methods used in 2022 (based on total unique orders). Use is_valid = 1 to filter transactions.
Source: order_detail, payment_method
5. Rank the following 5 products by transaction value: Samsung, Apple, Sony, Huawei, Lenovo. Use is_valid = 1 to filter transactions.
Source: order_detail, sku_detail

#### SOME ANALYSIS & INSIGHT
**Answer number 1**
```sql
SELECT
	EXTRACT (MONTH FROM order_date) AS Transaction_Month,
	SUM(after_discount) AS Total_Transaction
FROM order_detail
WHERE
	EXTRACT(YEAR FROM order_date) = 2021
	AND is_valid = 1
GROUP BY
	1
ORDER BY
	2 DESC
LIMIT 7
;
```

![Data_analysis_SQL\hasilquery1.jpg](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_SQL/hasilquery1.jpg)


**Insight** : Based on the data obtained, August (the 8th month) in 2021 recorded the highest total transaction amount, totaling 227,862,744 or 17.22% of the total transactions for 2021 (1,322,726,912).

**Answer number 2**
```sql
SELECT
	sd.category AS Category_Product,
	SUM(od.after_discount) AS Total_Transaction
FROM
	order_detail AS od
	LEFT JOIN sku_detail AS sd ON od.sku_id = sd.id
WHERE
	EXTRACT(YEAR FROM od.order_date) = 2022
	AND is_valid = 1
GROUP BY 1
ORDER BY 2 DESC
LIMIT 7;
```

![Data_analysis_SQL\hasilquery2.jpg](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_SQL/hasilquery2.jpg)

**Insight** : Based on the data obtained, the Mobile & Tablets category recorded the highest transaction value throughout 2022, with a total transaction amount of 918,451,576, accounting for 39.17% of the total transactions in 2022 (2,344,848,413).

**Answer number 3**
```sql
WITH penjualan_tahunan AS (
	SELECT
		sd.category AS kategori,
		SUM(CASE WHEN EXTRACT(YEAR from od.order_date) = 2021 then after_discount END) AS total_sales_2021,
		SUM(CASE WHEN EXTRACT(YEAR from od.order_date) = 2022 then after_discount END) AS total_sales_2022,
		SUM(CASE WHEN EXTRACT(YEAR from od.order_date) = 2022 then after_discount END)
		-
		SUM(CASE WHEN EXTRACT(YEAR from od.order_date) = 2021 then after_discount END) AS perbedaan
	FROM order_detail AS od
	LEFT JOIN sku_detail AS sd
		ON od.sku_id = sd.id
	WHERE
		EXTRACT(YEAR from od.order_date) IN (2021,2022)
		AND od.is_valid = 1
	GROUP BY sd.category
	ORDER BY perbedaan DESC
)

SELECT
	kategori,
	total_sales_2021,
	total_sales_2022,
	perbedaan,
	CASE WHEN total_sales_2022 < total_sales_2021 THEN 'Penurunan'
	ELSE 'Kenaikan'
	END AS keterangan
FROM penjualan_tahunan
;

```
![Data_analysis_SQL\hasilquery1.jpg](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_SQL/hasilquery3.jpg)


**Insight** : Based on the data obtained, the categories that showed an increase in 2022 compared to 2021 include Mobiles & Tablets, Entertainment, Appliances, Men’s Fashion, Computing, Home & Living, Health & Sport, Women’s Fashion, School & Education, Superstore, Soghaat, Kids & Baby, and Beauty & Grooming. On the other hand, the categories that experienced a decline were Others and Books.

### 2. DATA ANALYSIS USING PYTHON IN GOOGLE COLAB (JUPYTER NOTEBOOK)
For detail explanation in Bahasa, you can check here :
[FINAL PROJECT PYTHON MYSKILL - MOCHAMAD SYAHRIZAL.pdf](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_python/FINAL%20PROJECT%20PYTHON%20MYSKILL%20-%20MOCHAMAD%20SYAHRIZAL.pdf)

And for full query you can check here :
[Full Jupyter Notebook](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_python/Copy_of_Mentoring_Python_DA11.ipynb)

#### ONE OF QUESTION ON THE PROJECT
Dear Data Analyst,

At the end of this year, the company will be awarding prizes to customers who win the Year-End Festival competition. The Marketing Team needs assistance in determining the estimated prizes to be awarded to the competition winners. These prizes will be selected from the Top 5 Products in the Mobiles & Tablets category throughout 2022, based on the highest sales quantity (valid = 1).

Please assist us by providing this data to the Marketing Team before the end of this month. We thank you in advance for your support.

Regards,
Marketing Team

#### ANALYSIS AND INSIGHT
```python
top5 = pd.DataFrame(
    df[
        (df['category'] == 'Mobiles & Tablets') &  # Memfilter kategori 'Mobiles & Tablets'
        (df['is_valid'] == 1) &  # Memfilter transaksi valid (is_valid = 1)
        (df['order_date'].dt.year == 2022)  # Memfilter transaksi yang terjadi di tahun 2022
    ]
    # Mengelompokkan data berdasarkan SKU (sku_name) dan menjumlahkan qty_ordered
    .groupby(by=["sku_name"])['qty_ordered']
    .sum()  # Menjumlahkan total qty_ordered untuk setiap sku_name
    .sort_values(ascending=False)  # Mengurutkan hasil dari terbesar ke terkecil
    .head()  # Mengambil 5 SKU dengan jumlah qty_ordered tertinggi
    .reset_index(name='quantity_order_2022')  # Mengatur ulang indeks dan mengganti nama kolom hasil agregasi
)

# Menampilkan DataFrame top 5 produk dengan qty_ordered tertinggi di kategori 'Mobiles & Tablets' untuk tahun 2022
top5

# Mengurutkan DataFrame terlebih dahulu (jika belum)
top5 = top5.sort_values(by='quantity_order_2022', ascending=False)

# Membuat horizontal bar chart
top5.plot(
    x='sku_name',
    y='quantity_order_2022',
    kind='barh',
    grid=True,
    xlabel='Quantity',
    ylabel='Product Name',
    figsize=(12, 6),
    title='TOP 5 Product'
)

# Membalik sumbu y agar nilai terbesar di atas
plt.gca().invert_yaxis()


plt.show()
```
![Data_analysis_python\syntax no 1 visual result.jpg](https://github.com/MochSyahrizal/Final-Project-Data-Analysis-MySkill-Ecommerce/blob/main/Data_analysis_python/syntax%20no%201%20visual%20result.jpg)


**Insight** : Data analysis reveals that the five best-selling products in the Mobiles & Tablets category are the Idroid BALRX7-Gold, Idroid BALRX7-Jet Black, Infinix Hot 4-Gold, Samsung Grand Prime Plus-Black, and Infinix Zero 4-Grey. Among these products, the Idroid BALRX7-Gold ranks first, with sales reaching 1,000 units, making it a favorite choice among consumers and a compelling gift for the winners of the Year-End Festival.