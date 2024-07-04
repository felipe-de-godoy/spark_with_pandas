# spark_with_pandas

# Leveraging PySpark.Pandas for Efficient Data Analysis
In the world of big data, Spark has become a pivotal tool for handling and processing large datasets efficiently. However, if you're a data scientist or a data analyst accustomed to the simplicity and power of Pandas, you might find transitioning to Spark a bit daunting. That's where the Pandas API on Spark comes in! It brings the familiar Pandas syntax to the Spark ecosystem, allowing you to leverage the distributed computing power of Spark while working with a Pandas-like interface.

### Why Use Pandas API on Spark?

The Pandas API on Spark allows you to:

1. **Handle Larger-Than-Memory Data**: Work with datasets that exceed the memory capacity of a single machine.
2. **Leverage Distributed Computing**: Benefit from the parallel processing power of a Spark cluster.
3. **Use Familiar Syntax**: Transition smoothly from Pandas to Spark without having to learn a completely new API.

### Setting Up Your Environment

To get started, we'll use Docker to set up a local PySpark environment. Open your terminal and run the following command:

```sh

docker run -it -p 8888:8888 jupyter/pyspark-notebook

```

Once the container is running, open your browser and navigate to the second link to access your PySpark environment.

![[Pasted image 20240703225654.png]]
### Getting the Data

We'll use a dataset from Kaggle for this example. You can find the dataset here: [Students Performance Dataset](https://www.kaggle.com/rabieelkharoua/students-performance-dataset). Download the CSV file and place it in the appropriate location within your Docker container (you can drag it to jupyter tab in your browser).

### Processing Data with Pandas API on Spark

With the environment set up and the file in the correct place, you can run the following code to read, treat, visualize, and save the data to S3.

**Step 1: Import Libraries and Initialize Spark Session**

```python
!pip install boto3 plotly
import pandas as pd
import numpy as np
import pyspark.pandas as ps
from pyspark.sql import SparkSession
import boto3
spark = SparkSession.builder.appName("PandasOnSparkExample").getOrCreate()
```

**Step 2: Read Data from CSV**

```python
columns = ['StudentID', 'Age', 'Gender', 'Ethnicity', 'ParentalEducation',
'StudyTimeWeekly', 'Absences', 'Tutoring', 'ParentalSupport',
'Extracurricular', 'Sports', 'Music', 'Volunteering', 'GPA', 'GradeClass']
psdf = ps.read_csv('Student_performance_data _.csv', names=columns, header=0)
```

**Step 3: Exploring the Data**

Check the first few rows of the dataset to ensure it's loaded correctly:

```python
print(psdf.head())
```

Print column names and data types:

```python
print(psdf.columns)
print(psdf.dtypes)
```

**Step 4: Handling Missing Data**

Handle missing data by either dropping rows with missing values:

```python
psdf_cleaned = psdf.dropna()
print(psdf_cleaned.head())
```

Or filling them with a specific value:

```python
psdf_filled = psdf.fillna(value=0)
print(psdf_filled.head())
```

**Step 5: Data Manipulations and Insights**

Group your data and apply aggregate functions:

```python
grouped_psdf = psdf.groupby('Gender').mean()
print(grouped_psdf)
```

Sort your DataFrame by values:

```python
sorted_psdf = psdf.sort_values(by='GPA', ascending=False)
print(sorted_psdf.head())
```

**Step 6: Visualization**

Plot the GPA distribution using plotly (it must be installed):

```python
psdf['GPA'].plot(kind='hist')
```

**Step 7: Save as Compressed Parquet and Upload to S3**

Save the DataFrame as a compressed Parquet file:

```python
parquet_file = 'student_data.parquet.gzip'
psdf.to_parquet(parquet_file, compression='gzip')
```

Upload the Parquet file to S3 using `boto3`:

```python
s3_bucket = 'your-s3-bucket-name'
s3_key = 'path/to/save/student_data.parquet.gzip'

# Initialize a session using Amazon S3
s3 = boto3.client('s3')

# Upload the file to S3
s3.upload_file(parquet_file, s3_bucket, s3_key)
print(f"File uploaded to s3://{s3_bucket}/{s3_key}")
```

### Conclusion

The Pandas API on Spark bridges the gap between Pandas and Spark, offering you the best of both worlds. Whether you're handling massive datasets or looking to scale your data processing pipelines effortlessly, this API empowers you to harness the full power of Spark with the simplicity of Pandas.

Try it out and supercharge your data analytics workflow today!

For more details, you can refer to [Spark's official documentation](https://spark.apache.org/docs/latest/api/python/getting_started/index.html).

Happy data wrangling!
