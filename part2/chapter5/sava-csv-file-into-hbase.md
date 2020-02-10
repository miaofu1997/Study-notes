# Spark Streaming

## Sava csv file into HBase

| id | name |
| :---: | :---: |
| 1 | kuangyuefeng |
| 2 | fumiao |
| 3 | hupeng |
| 4 | hequnxiong |

```scala
object SparkOperateHBase {
  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setAppName(s"${this.getClass.getSimpleName}").setMaster("local")
    val sc = new SparkContext(sparkConf)
    val hbaseConf = HBaseConfiguration.create()
    hbaseConf.set("hbase.zookeeper.property.clientPort", "2181")
    hbaseConf.set("hbase.zookeeper.quorum", "bigdata02,bigdata03,bigdata01")
    hbaseConf.set("zookeeper.znode.parent", "/hbase")
    val hbaseConn = ConnectionFactory.createConnection(hbaseConf)
    val admin = hbaseConn.getAdmin

    val jobConf = new JobConf(hbaseConf, this.getClass)
    jobConf.set(TableOutputFormat.OUTPUT_TABLE, "spark_test")

    if (!admin.tableExists(TableName.valueOf("spark_test"))) {
      val desc = new HTableDescriptor(TableName.valueOf("spark_test"))
      val hcd = new HColumnDescriptor("cf1")
      desc.addFamily(hcd)
      admin.createTable(desc)
    }

    val job = Job.getInstance(jobConf)
    job.setOutputKeyClass(classOf[ImmutableBytesWritable])
    job.setOutputValueClass(classOf[Result])
    job.setOutputFormatClass(classOf[TableOutputFormat[ImmutableBytesWritable]])

    // 创建RDD
    val input: RDD[String] = sc.textFile("file:///D:/testData/test.csv")
    val headerAndRows = input.map(line => line.split(",").map(_.trim))
    // 把表头和数据分开
    val header = headerAndRows.first()
    val data = headerAndRows.filter(_(0) != header(0))
    // 数据转为数组 unnecessary！
    val array = data.collect()

    // 写入Hbase
    val rdd = data.map{ arr => {
      val put = new Put(Bytes.toBytes(arr(0)))
      put.addColumn(Bytes.toBytes("cf1"), Bytes.toBytes("name"),Bytes.toBytes(arr(1)))
      (new ImmutableBytesWritable, put)
    }}

    rdd.saveAsNewAPIHadoopDataset(job.getConfiguration)

    sc.stop()
  }
}
```

