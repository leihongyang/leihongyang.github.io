# spark 对复杂json处理，且json字段长短不一

### 背景：一条json消息中根据某个字段不同，会有不同的数据结构，json有多重嵌套

主要使用了
   * explode： 展开array或map为多行
   * inline：展开可以把array[struct[XXX]]直接展开成XXX
   
```
scala> val res0 = spark.read.json("file:///home/hdfs/20200422.csv")
res0: org.apache.spark.sql.DataFrame = [collectTime: string, factory: string ... 2 more fields]

scala> res0.printSchema
root
 |-- collectTime: string (nullable = true)
 |-- factory: string (nullable = true)
 |-- infoList: array (nullable = true)
 |    |-- element: struct (containsNull = true)
 |    |    |-- info: struct (nullable = true)
 |    |    |    |-- EnergyStorageDevice: array (nullable = true)
 |    |    |    |    |-- element: struct (containsNull = true)
 |    |    |    |    |    |-- batterynum: long (nullable = true)
 |    |    |    |    |    |-- dianliu: long (nullable = true)
 |    |    |    |    |    |-- no: long (nullable = true)
 |    |    |    |    |    |-- one_voltages: array (nullable = true)
 |    |    |    |    |    |    |-- element: long (containsNull = true)
 |    |    |    |    |    |-- this_batterynum: long (nullable = true)
 |    |    |    |    |    |-- this_no: long (nullable = true)
 |    |    |    |    |    |-- voltage: long (nullable = true)
 |    |    |    |-- EnergyStorageDeviceTemperature: array (nullable = true)
 |    |    |    |    |-- element: struct (containsNull = true)
 |    |    |    |    |    |-- no: long (nullable = true)
 |    |    |    |    |    |-- probe_num: long (nullable = true)
 |    |    |    |    |    |-- probe_values: array (nullable = true)
 |    |    |    |    |    |    |-- element: long (containsNull = true)
 |    |    |    |-- alarmSign: string (nullable = true)
 |    |    |    |-- brakeStatus: long (nullable = true)
 |    |    |    |-- chargeStatus: long (nullable = true)
 |    |    |    |-- dcdc: long (nullable = true)
 |    |    |    |-- driveMotor: array (nullable = true)
 |    |    |    |    |-- element: struct (containsNull = true)
 |    |    |    |    |    |-- controllerT: long (nullable = true)
 |    |    |    |    |    |-- current: long (nullable = true)
 |    |    |    |    |    |-- inputVoltage: long (nullable = true)
 |    |    |    |    |    |-- no: long (nullable = true)
 |    |    |    |    |    |-- rev: long (nullable = true)
 |    |    |    |    |    |-- status: long (nullable = true)
 |    |    |    |    |    |-- temperature: long (nullable = true)
 |    |    |    |    |    |-- torque: long (nullable = true)
 |    |    |    |-- engineFaultCount: long (nullable = true)
 |    |    |    |-- engineFaultList: string (nullable = true)
 |    |    |    |-- faultList: string (nullable = true)
 |    |    |    |-- gears: long (nullable = true)
 |    |    |    |-- latitude: string (nullable = true)
 |    |    |    |-- location: long (nullable = true)
 |    |    |    |-- longitude: string (nullable = true)
 |    |    |    |-- maxAlarmLevel: long (nullable = true)
 |    |    |    |-- maxCodeV: long (nullable = true)
 |    |    |    |-- maxProbeNoT: long (nullable = true)
 |    |    |    |-- maxSysNoT: long (nullable = true)
 |    |    |    |-- maxSysNoV: long (nullable = true)
 |    |    |    |-- maxT: long (nullable = true)
 |    |    |    |-- maxValueV: double (nullable = true)
 |    |    |    |-- minCodeV: long (nullable = true)
 |    |    |    |-- minProbeNoT: long (nullable = true)
 |    |    |    |-- minSysNoT: long (nullable = true)
 |    |    |    |-- minSysNoV: long (nullable = true)
 |    |    |    |-- minT: long (nullable = true)
 |    |    |    |-- minValueV: double (nullable = true)
 |    |    |    |-- motorFaultCount: long (nullable = true)
 |    |    |    |-- motorFaultList: string (nullable = true)
 |    |    |    |-- number: long (nullable = true)
 |    |    |    |-- odo: double (nullable = true)
 |    |    |    |-- otherFaultCount: long (nullable = true)
 |    |    |    |-- otherFaultList: string (nullable = true)
 |    |    |    |-- runMode: long (nullable = true)
 |    |    |    |-- sir: long (nullable = true)
 |    |    |    |-- soc: double (nullable = true)
 |    |    |    |-- speed: double (nullable = true)
 |    |    |    |-- storedFaultCount: long (nullable = true)
 |    |    |    |-- totalCurrent: long (nullable = true)
 |    |    |    |-- totalVoltage: long (nullable = true)
 |    |    |    |-- travel: double (nullable = true)
 |    |    |    |-- vehicleStatus: long (nullable = true)
 |    |    |-- infoType: long (nullable = true)
 |-- vin: string (nullable = true)

scala> res0.show()
+--------------+-----------+--------------------+-----------------+
|   collectTime|    factory|            infoList|              vin|
+--------------+-----------+--------------------+-----------------+
|20200422131027|     futian|[[[,,, 0, 3, 2,,,...|LVBV3J0B6JE900137|
|20200422131026|yiqijiefang|[[[,,, 0, 3, 1,,,...|LFNA4LDA1JAX07996|
+--------------+-----------+--------------------+-----------------+


scala> val res1 = res0.select(res0("collectTime"), res0("factory"), expr("inline(infoList)"), res0("vin"))
res1: org.apache.spark.sql.DataFrame = [collectTime: string, factory: string ... 3 more fields]

scala> res1.printSchema
root
 |-- collectTime: string (nullable = true)
 |-- factory: string (nullable = true)
 |-- info: struct (nullable = true)
 |    |-- EnergyStorageDevice: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- batterynum: long (nullable = true)
 |    |    |    |-- dianliu: long (nullable = true)
 |    |    |    |-- no: long (nullable = true)
 |    |    |    |-- one_voltages: array (nullable = true)
 |    |    |    |    |-- element: long (containsNull = true)
 |    |    |    |-- this_batterynum: long (nullable = true)
 |    |    |    |-- this_no: long (nullable = true)
 |    |    |    |-- voltage: long (nullable = true)
 |    |-- EnergyStorageDeviceTemperature: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- no: long (nullable = true)
 |    |    |    |-- probe_num: long (nullable = true)
 |    |    |    |-- probe_values: array (nullable = true)
 |    |    |    |    |-- element: long (containsNull = true)
 |    |-- alarmSign: string (nullable = true)
 |    |-- brakeStatus: long (nullable = true)
 |    |-- chargeStatus: long (nullable = true)
 |    |-- dcdc: long (nullable = true)
 |    |-- driveMotor: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- controllerT: long (nullable = true)
 |    |    |    |-- current: long (nullable = true)
 |    |    |    |-- inputVoltage: long (nullable = true)
 |    |    |    |-- no: long (nullable = true)
 |    |    |    |-- rev: long (nullable = true)
 |    |    |    |-- status: long (nullable = true)
 |    |    |    |-- temperature: long (nullable = true)
 |    |    |    |-- torque: long (nullable = true)
 |    |-- engineFaultCount: long (nullable = true)
 |    |-- engineFaultList: string (nullable = true)
 |    |-- faultList: string (nullable = true)
 |    |-- gears: long (nullable = true)
 |    |-- latitude: string (nullable = true)
 |    |-- location: long (nullable = true)
 |    |-- longitude: string (nullable = true)
 |    |-- maxAlarmLevel: long (nullable = true)
 |    |-- maxCodeV: long (nullable = true)
 |    |-- maxProbeNoT: long (nullable = true)
 |    |-- maxSysNoT: long (nullable = true)
 |    |-- maxSysNoV: long (nullable = true)
 |    |-- maxT: long (nullable = true)
 |    |-- maxValueV: double (nullable = true)
 |    |-- minCodeV: long (nullable = true)
 |    |-- minProbeNoT: long (nullable = true)
 |    |-- minSysNoT: long (nullable = true)
 |    |-- minSysNoV: long (nullable = true)
 |    |-- minT: long (nullable = true)
 |    |-- minValueV: double (nullable = true)
 |    |-- motorFaultCount: long (nullable = true)
 |    |-- motorFaultList: string (nullable = true)
 |    |-- number: long (nullable = true)
 |    |-- odo: double (nullable = true)
 |    |-- otherFaultCount: long (nullable = true)
 |    |-- otherFaultList: string (nullable = true)
 |    |-- runMode: long (nullable = true)
 |    |-- sir: long (nullable = true)
 |    |-- soc: double (nullable = true)
 |    |-- speed: double (nullable = true)
 |    |-- storedFaultCount: long (nullable = true)
 |    |-- totalCurrent: long (nullable = true)
 |    |-- totalVoltage: long (nullable = true)
 |    |-- travel: double (nullable = true)
 |    |-- vehicleStatus: long (nullable = true)
 |-- infoType: long (nullable = true)
 |-- vin: string (nullable = true)

scala> res1.show()
+--------------+-----------+--------------------+--------+-----------------+
|   collectTime|    factory|                info|infoType|              vin|
+--------------+-----------+--------------------+--------+-----------------+
|20200422131027|     futian|[,,, 0, 3, 2,,,,,...|       1|LVBV3J0B6JE900137|
|20200422131027|     futian|[,,,,,, [[18, 0, ...|       2|LVBV3J0B6JE900137|
|20200422131027|     futian|[[[152, 10000, 1,...|       8|LVBV3J0B6JE900137|
|20200422131027|     futian|[, [[1, 38, [60, ...|       9|LVBV3J0B6JE900137|
|20200422131027|     futian|[,,,,,,,,,,,,,,, ...|       6|LVBV3J0B6JE900137|
|20200422131027|     futian|[,,,,,,,,,,, 39.7...|       5|LVBV3J0B6JE900137|
|20200422131027|     futian|[,, 0000000000000...|       7|LVBV3J0B6JE900137|
|20200422131026|yiqijiefang|[,,, 0, 3, 1,,,,,...|       1|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[,,,,,, [[26, 30,...|       2|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[,,,,,,,,,,, 39.8...|       5|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[,,,,,,,,,,,,,,, ...|       6|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[,, 0000000000000...|       7|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[[[160, 10320, 1,...|       8|LFNA4LDA1JAX07996|
|20200422131026|yiqijiefang|[, [[1, 32, [58, ...|       9|LFNA4LDA1JAX07996|
+--------------+-----------+--------------------+--------+-----------------+


scala> res1.filter("infoType=8").select(res1("collectTime"), res1("factory"), expr("inline(info.EnergyStorageDevice)"), res1("infoType"),res1("vin")).show()
+--------------+-----------+----------+-------+---+--------------------+---------------+-------+-------+--------+-----------------+
|   collectTime|    factory|batterynum|dianliu| no|        one_voltages|this_batterynum|this_no|voltage|infoType|              vin|
+--------------+-----------+----------+-------+---+--------------------+---------------+-------+-------+--------+-----------------+
|20200422131027|     futian|       152|  10000|  1|[38927, 56335, 56...|            152|      1|   6180|       8|LVBV3J0B6JE900137|
|20200422131026|yiqijiefang|       160|  10320|  1|[40972, 41740, 50...|            160|      1|   5200|       8|LFNA4LDA1JAX07996|
+--------------+-----------+----------+-------+---+--------------------+---------------+-------+-------+--------+-----------------+


scala> res1.filter("infoType=5").select(res1("collectTime"), res1("factory"), res1("info.latitude"), res1("infoType"),res1("vin")).show()
+--------------+-----------+---------+--------+-----------------+
|   collectTime|    factory| latitude|infoType|              vin|
+--------------+-----------+---------+--------+-----------------+
|20200422131027|     futian|39.718566|       5|LVBV3J0B6JE900137|
|20200422131026|yiqijiefang|39.875452|       5|LFNA4LDA1JAX07996|
+--------------+-----------+---------+--------+-----------------+
```




