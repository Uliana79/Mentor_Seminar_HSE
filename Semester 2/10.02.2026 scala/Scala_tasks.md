**Проверка, что Spark установлен и работает**

Цель: убедиться, что кластер реально готов к вычислениям.



spark-submit --version

или

spark-shell



test:

sc.parallelize(1 to 1000000).sum()



-------------------------------------------------------------



**RDD vs DataFrame**

Цель: показать разные уровни API.



val rdd = sc.parallelize(Seq(1,2,3,4,5))

rdd.map(\_ \* 2).collect()



val df = spark.range(5)

df.selectExpr("id \* 2 as value").show()



*RDD — низкоуровневый API*

*DataFrame — оптимизируемый (Catalyst)*



-------------------------------------------------------------



**Lazy evaluation**

Цель: показать, что Spark не считает без action.



val df = spark.range(1000000)

val df2 = df.filter("id % 2 = 0")



df2.count()



*Transformation ≠ execution*

*Action запускает DAG*



--------------------------------------------------------------



**Агрегации и shuffle**

Цель: показать groupBy → shuffle.



val data = Seq(

&nbsp; ("user1","A",100),

&nbsp; ("user1","B",200),

&nbsp; ("user2","A",50)

)

val df = spark.createDataFrame(data).toDF("user","category","amount")

df.groupBy("user").sum("amount").show()



*groupBy = shuffle*

*network + disk*



--------------------------------------------------------------



**Join и broadcast**

Цель: объяснить, почему join — опасная операция.



val users = Seq(

&nbsp; ("user1","Berlin"),

&nbsp; ("user2","Munich")

).toDF("user","city")

val orders = Seq(

&nbsp; ("user1",100),

&nbsp; ("user1",50),

&nbsp; ("user2",300)

).toDF("user","amount")

users.join(orders, "user").show()



broadcast:

import org.apache.spark.sql.functions.broadcast

broadcast(users).join(orders, "user").show()



*Broadcast меняет shuffle на in-memory*



--------------------------------------------------------------



**Cache / Persist**

Цель: показать повторное использование DataFrame.



val df = spark.range(5000000)

df.cache()

df.count()

df.filter("id % 2 = 0").count()

df.filter("id % 3 = 0").count()



*cache — память*

*persist — память + диск*



---------------------------------------------------------------



**Запись данных (Parquet)**

Цель: показать финальный шаг пайплайна.



spark.range(10000).write.mode("overwrite").parquet("hdfs:///tmp/spark\_demo")



Проверка:

hdfs dfs -ls /tmp/spark\_demo



*Parquet — columnar*

*overwrite = delete + write (важно для sticky bit)*



