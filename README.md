# Distributed ML on Banking Data

A small project I built to learn how the Hadoop / Spark stack handles a real-world banking dataset end to end.

The notebook walks through five things on the Portuguese bank marketing dataset (`bank.csv`, about 4.5k rows):

1. Loading the data into a Hive-style partitioned Parquet warehouse.
2. Some EDA with Spark SQL and the DataFrame API.
3. Repartitioning the data and comparing single vs multi-partition execution time.
4. Training a few classifiers (Logistic Regression, Random Forest, GBT) to predict whether a client will subscribe to a term deposit, then tuning the best one with cross-validation.
5. A small Spark Structured Streaming demo that consumes simulated transactions, runs sliding-window aggregations and flags high-value events.

## Dataset

`bank.csv` from the UCI Bank Marketing repository. 17 columns - age, job, balance, contact details, previous campaign outcomes, and the target `y` (subscribed: yes/no).

Place the file at `MyDrive/data/bank.csv` if you're running in Colab.

## How to run

Open `Banking_Distributed_ML_Project.ipynb` in Google Colab and run all cells. Takes around 6-8 minutes on the free tier.

Locally you just need:

```
pip install pyspark==3.5.2 pandas matplotlib seaborn scikit-learn
```

## Results

Best model (tuned GBT / RF) lands around **AUC 0.88** and ~89% accuracy on the held-out test set. The most important feature by a wide margin is `duration` - how long the last sales call lasted - which lines up with what you'd expect from a telemarketing dataset.

## Notes

- Schema is declared explicitly rather than inferred, mostly out of habit from working with larger files.
- The Hive part is simulated through Spark's built-in catalog since Colab doesn't give you a real HDFS cluster, but the SQL is the same as on a real one.
- The streaming source is a watch-folder of CSVs instead of Kafka, again because of Colab. Swapping it for Kafka is a one-line change.
