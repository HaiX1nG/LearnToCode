# Spark RDD操作

## 1.数据加载

```scala
scala> val rdd = sc.textFile("/data/Employee_salary_first_half.csv")
scala>val count = rdd.count
res0: Long = 1753
```

## 2.处理数据

```scala
// 根据条件过滤数据，满足条件就保留
scala> val data = rdd.filter(x=>x.contains("/"))
data: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[2] at filter at <console>:23

scala> data.count
res1: Long = 1752

scala> rdd.take(2)
res2: Array[String] = Array(EmpID,Name,Gender,Date_of_Birth,Age,GROSS,Net_Pay,Deduction,Designation,Department, 20438,Deana Lucero,F,2/11/96,23,30171,27825,2346,Technical Solutions Engineer.Associate.,AmaTec - APJ TSE)

scala> data.take(2)
res3: Array[String] = Array(20438,Deana Lucero,F,2/11/96,23,30171,27825,2346,Technical Solutions Engineer.Associate.,AmaTec - APJ TSE, 21921,Delbert Krause,M,24/06/1993,27,30250,26911,3339,Systems Engineer.Associate.,Product - Custom)
// 根据 `,` 分割字符 
scala> val data_date = data.map(x=>x.split(",")(3))
data_date: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[3] at map at <console>:23

scala> data_date.take(3)
res4: Array[String] = Array(2/11/96, 24/06/1993, 6/5/94)
// 根据 `,` 分割字符
scala> val data_year = data_date.map(x=>x.split("/")(2))
data_year: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[4] at map at <console>:23

scala> data_year.take(10)
res5: Array[String] = Array(96, 1993, 94, 97, 95, 91, 92, 97, 1997, 93)
// 判断长度补全年份
scala> val year = data_year.map(x=>{
     |   if(x.length==2)
     |     "19" + x
     |   else
     |     x
     | })
year: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[5] at map at <console>:23

scala> val EmpID = data.map(x=>x.split(",")(0).toInt)
EmpID: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[7] at map at <console>:23

scala> EmpID.take(10)
res8: Array[Int] = Array(20438, 21921, 23036, 23032, 23033, 18466, 19836, 22437, 22531, 19203)

scala> val EmpID_sorted = EmpID.sortBy(x=>x)
EmpID_sorted: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[12] at sortBy at <console>:23

scala> EmpID_sorted.take(100)
res9: Array[Int] = Array(1934, 2809, 2810, 2815, 2823, 2824, 2830, 2840, 2924, 2930, 2944, 3034, 3041, 3068, 3156, 3245, 3272, 3273, 3357, 3361, 3395, 3434, 3727, 3753, 3796, 3860, 3892, 3914, 3936, 3942, 3960, 3976, 3989, 4004, 4028, 4032, 4033, 4039, 4057, 4069, 4070, 4074, 4076, 4086, 4158, 4160, 4165, 4202, 4203, 4212, 4226, 4231, 4270, 4359, 4380, 4388, 4403, 4410, 4421, 4422, 4463, 4512, 4513, 4580, 4583, 4585, 4607, 4608, 4646, 4653, 4672, 4677, 4680, 4693, 4694, 4748, 4757, 4760, 4761, 4772, 4773, 4778, 4782, 4807, 4830, 4842, 4886, 4900, 4907, 4909, 4941, 4957, 4961, 4977, 4979, 4991, 4995, 4998, 5004, 5005)

scala> val EmpInfo_sortBy_id = data.map(x=>(x.split(",")(0).toInt, x)).sortBy(x=>x._1)
EmpInfo_sortBy_id: org.apache.spark.rdd.RDD[(Int, String)] = MapPartitionsRDD[24] at sortBy at <console>:23

scala> EmpInfo_sortBy_id.take(10)
res10: Array[(Int, String)] = Array((1934,1934,Kristin Cooley,F,7/2/78,42,84794,64902,19892,Solutions Architect.Senior II.,Service Delivery Special Projects), (2809,2809,Lessie Donovan,F,11/10/75,44,101096,75082,26014,Manager..Travel and Vendor Management,Global Procurement), (2810,2810,Kristine Werner,F,30/06/1978,42,213442,110865,102577,Director..Program Management,Wizards Program), (2815,2815,Young Knapp,F,20/04/1970,50,73138,48557,24581,Accountant.Senior.,Finance - Accounting), (2823,2823,Stephan Benjamin,M,2/11/75,44,188721,103452,85269,Manager.Senior.Platform Operations,GPO PMO & Business Operations), (2824,2824,Milo Tapia,M,21/10/1979,40,247196,124512,122684,Manager.Senior.Engineering,Media Analytics), (2830,2830,Micheal Wall,M,21/10/1975,44,145104,69795...
```

## 使用collect()方法查询数据

- collect()方法是一种行动操作，可以将RDD转换为少量数据储存



<hr />

## 什么是(Transformation)转换算子和(Action)行动算子

__Transformation转换算子:__

- 将所有转换算子在执行完毕后，都会返回一个新的RDD
- 说有的转换算子都是【Lazy惰性】，并不会立即执行，仅仅是在定义计算的规则
- 转换算子必须遇到【Action动作算子】才会触发执行

__Action动作算子:__

- 动作算子在执行后，不会返回一个RDD。【要么没有返回值，要么返回的数据类型不是RDD】
- 动作算子都是【立即执行】的，一个动作算子就会产生一个Job的任务，并且会执行这个动作算子所依赖的其他所有RDD

<hr />

宽依赖和窄依赖:

- 窄依赖：表现为一个父RDD的分区对应一个RDD的分区或者多个父RDD分区对应一个子RDD
- 宽依赖：表现为多个父RDD
