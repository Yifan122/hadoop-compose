DELTA Streamer
```
docker exec -it adhoc-2 /bin/bash

# Run the following spark-submit command to execute the delta-streamer and ingest to stock_ticks_cow table in HDFS
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type COPY_ON_WRITE \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts  \
  --target-base-path /user/hive/warehouse/stock_ticks_cow \
  --target-table stock_ticks_cow --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider

# Run the following spark-submit command to execute the delta-streamer and ingest to stock_ticks_mor table in HDFS
spark-submit \
  --class org.apache.hudi.utilities.deltastreamer.HoodieDeltaStreamer $HUDI_UTILITIES_BUNDLE \
  --table-type MERGE_ON_READ \
  --source-class org.apache.hudi.utilities.sources.JsonKafkaSource \
  --source-ordering-field ts \
  --target-base-path /user/hive/warehouse/stock_ticks_mor \
  --target-table stock_ticks_mor \
  --props /var/demo/config/kafka-source.properties \
  --schemaprovider-class org.apache.hudi.utilities.schema.FilebasedSchemaProvider \
  --disable-compaction

```

HDFS URL
http://namenode:50070

HIVE
```
docker exec -it adhoc-2 /bin/bash
beeline -u jdbc:hive2://hiveserver:10000 \
  --hiveconf hive.input.format=org.apache.hadoop.hive.ql.io.HiveInputFormat \
  --hiveconf hive.stats.autogather=false
```

SPARK SQL
```
docker exec -it adhoc-1 /bin/bash
$SPARK_INSTALL/bin/spark-shell \
  --jars $HUDI_SPARK_BUNDLE \
  --master local[2] \
  --driver-class-path $HADOOP_CONF_DIR \
  --conf spark.sql.hive.convertMetastoreParquet=false \
  --deploy-mode client \
  --driver-memory 1G \
  --executor-memory 3G \
  --num-executors 1 \
  --packages org.apache.spark:spark-avro_2.11:2.4.4
```

Presto
```
docker exec -it presto-worker-1 presto --server presto-coordinator-1:8090
```