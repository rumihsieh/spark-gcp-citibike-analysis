# Apache Spark GCP Data Engineering Practice (Citibike + Custom Partitioner)

This project demonstrates large-scale data processing using **Apache Spark**, **Scala**, and **Google Cloud Platform (GCP)**. It includes reading Citibike trip data from a Google Cloud Storage bucket, computing trip durations, aggregating statistics using RDD operations, and implementing a custom geospatial partitioner based on New York City zipcode polygons.

The project replicates real-world data engineering workflows involving large data files, performance comparison across partitioning strategies, and the use of geospatial data to optimize Spark shuffling behavior.

---

##  Project Overview

This analysis performs:

- Loading a **15GB Citibike dataset** from Google Cloud Storage  
- Parsing CSV into Spark DataFrames and RDDs  
- Calculating trip durations using custom helper functions  
- Aggregating statistics such as:  
  - total number of records  
  - average trip duration  
  - number of partitions  
- Using **combineByKey** to compute average duration per station  
- Comparing performance of:
  - no partitioning  
  - hash partitioning  
  - reusing a hash partition  
- Reading zipcode polygons from GeoJSON and mapping lat/lon to zipcode  
- Implementing a **custom Spark Partitioner** to partition trip records by zipcode  
- Using the custom partitioner to reduce shuffle and improve performance  

---

##  Technologies Used

- Apache Spark (RDD API + DataFrame reader)
- Scala
- Google Cloud Storage (GCS)
- GeoJSON data
- Functional programming (map, filter, reduce, combineByKey)
- Custom Spark Partitioner (advanced topic)

---

##  Repository Structure

spark-gcp-citibike-analysis/
│
├── Assignment-2.ipynb # Main notebook (Spark + GCP + Custom Partitioner)
└── README.md # Project documentation

---

##  Key Analysis Steps

### **1. Data Preparation**
- Load ~15GB citibike CSV file from GCS
- Split each row, remove corrupted records
- Compute duration using timestamps (`time_difference`)
- Produce an RDD:  
(ride_id, rideable_type, started_at, ended_at, start_station, end_station, lat, lon, duration)

### **2. Core Statistics**
- Total number of trips (`count`)
- Average trip duration (`reduce`)
- Number of partitions in the RDD (`getNumPartitions`)

### **3. Aggregation Using combineByKey**
Compute average duration per **starting station**, then sort and extract the top 10 stations.

### **4. Hash Partitioning**
- Apply `HashPartitioner(numStations)`
- Compare runtime between:
1. no partitioning  
2. hash partitioning  
3. reusing a pre-hashed RDD  

This demonstrates how partitioning reduces shuffle and improves performance.

### **5. Geospatial Partitioning (Advanced)**
- Read zipcode polygons from GeoJSON
- Identify zipcode based on `(lat, lon)`
- Transform data into:
((lat, lon), (station, duration))
- Implement a **ZipcodePartitioner**:
- determines partition by zipcode
- handles missing or invalid coordinates
- Repartition the dataset using the custom partitioner

---

##  Example Results

Total trips: 76531242
Average duration: ~1689 seconds
Number of partitions: 110
Top stations by average duration:
(Prospect Ave & E 151 St, ...)
(Grand Concourse & E 144 St, ...)
...


Performance comparison (simplified):

No partition: slowest
Hash partition: faster
Reusing hash partition: fastest

---

##  How to Run

1. Install Spark and Scala  
2. Open the notebook in Jupyter  
3. Ensure access to Google Cloud bucket paths (or replace with local paths)  
4. Run cells sequentially  

---

##  Future Improvements

- Convert workflow to Spark DataFrame + SQL API  
- Add geospatial visualization  
- Export partitioned data to Parquet  
- Benchmark cluster execution on Dataproc  

---

##  License
This project is for educational and portfolio purposes.
