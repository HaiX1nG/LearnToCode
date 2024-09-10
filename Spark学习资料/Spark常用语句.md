```scala
// 加载读取文件
val data = sc. textFile("file:///you/file/path")

val data_detail = data.map(_.split(","))
// 定义样例类
case class Person(name: String, sex: String)
case class OrderInfo(OrderID: Int, TaxiID: Int, CreateTime: String, TotalTime: Int, TotalLong: Double, GetOnNumber: Int, DropOffNumber: Int, Fee1: Double, Fee2: Double, Fee3: Double, Fee4: Double, TotalFee: Double, TaxiCompany: String, GetOnLa: Double, GetOnlo: Double, DropOffLa: Double, DropOffLo: Double)

val OrderData = data_detail.map(x=>OrderInfo(x(0).toInt, x(1).toInt, x(2).toString, x(3).toInt, x(4).toDouble, x(5).toInt, x(6).toInt, x(7).toDouble, x(8).toDouble, x(9).toDouble, x(10).toDouble, x(11).toDouble, x(12).toString, x(13).toDouble, x(14).toDouble, x(15).toDouble, x(16).toDouble))

val OrderData_DF = OrderData.toDF()

OrderData_DF.printSchema

OrderData_DF.select("TaxiID")

resN.show

OrderData_DF.select("TaxiID", "TaxiCompany").groupBy("TaxiCompany")

resN.show
```

