# RDD

1. 转换

    1.map
    
        map是对RDD中的每个元素都执行一个指定的函数来产生一个新的RDD；RDD之间的元素是一对一关系；
        val rdd1 = sc.parallelize(1 to 9, 3)
        val rdd2 = rdd1.map(x => x*2)
        rdd2.collect
        res3: Array[Int] = Array(2, 4, 6, 8, 10, 12, 14, 16, 18)

    2.filter
    
        Filter是对RDD元素进行过滤；返回一个新的数据集，是经过func函数后返回值为true的原元素组成；
        val rdd3 = rdd2.filter (x => x> 10)
        rdd3.collect
        res4: Array[Int] = Array(12, 14, 16, 18)
    
    3.flatMap
    
        flatMap类似于map，但是每一个输入元素，会被映射为0到多个输出元素（因此，func函数的返回值是一个Seq，而不是单一元素），RDD之间的元素是一对多关系；
        val rdd4 = rdd3.flatMap (x => x to 20)
        res5: Array[Int] = Array(12, 13, 14, 15, 16, 17, 18, 19, 20, 14, 15, 16, 17, 18, 19, 20, 16, 17, 18, 19, 20, 18, 19, 20)
    
    4.mapPartitions
    
        mapPartitions是map的一个变种。map的输入函数是应用于RDD中每个元素，而mapPartitions的输入函数是每个分区的数据，也就是把每个分区中的内容作为整体来处理的。
    
    5.mapPartitionsWithIndex
    
        mapPartitionsWithSplit与mapPartitions的功能类似， 只是多传入split index而已，所有func 函数必需是 (Int, Iterator<T>) =>Iterator<U> 类型。
    
    6.sample
    
        sample(withReplacement,fraction,seed)是根据给定的随机种子seed，随机抽样出数量为frac的数据。withReplacement：是否放回样；fraction：比例，0.1表示10% ；
        val a = sc.parallelize(1 to 10000, 3)
        a.sample(false, 0.1, 0).count
        res24: Long = 960
    
    7.union
    
        union(otherDataset)是数据合并，返回一个新的数据集，由原数据集和otherDataset联合而成。
        val rdd8 = rdd1.union(rdd3)
        rdd8.collect
        res14: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 12, 14, 16, 18)
    
    8.intersection
    
        intersection(otherDataset)是数据交集，返回一个新的数据集，包含两个数据集的交集数据；
        val rdd9 = rdd8.intersection(rdd1)
        rdd9.collect
        res16: Array[Int] = Array(6, 1, 7, 8, 2, 3, 9, 4, 5)
    
    9.distinct
    
        distinct([numTasks]))是数据去重，返回一个数据集，是对两个数据集去除重复数据，numTasks参数是设置任务并行数量。
        val rdd10 = rdd8.union(rdd9).distinct
        rdd10.collect
        res19: Array[Int] = Array(12, 1, 14, 2, 3, 4, 16, 5, 6, 18, 7, 8, 9)
    
    10.groupByKey
    
        groupByKey([numTasks])是数据分组操作，在一个由（K,V）对组成的数据集上调用，返回一个（K,Seq[V])对的数据集。
        val rdd0 = sc.parallelize(Array((1,1), (1,2) , (1,3) , (2,1) , (2,2) , (2,3)), 3)
        val rdd11 = rdd0.groupByKey()
        rdd11.collect
        res33: Array[(Int, Iterable[Int])] = Array((1,ArrayBuffer(1, 2, 3)), (2,ArrayBuffer(1, 2, 3)))
    
    11.reduceByKey
    
        reduceByKey(func, [numTasks])是数据分组聚合操作，在一个（K,V)对的数据集上使用，返回一个（K,V）对的数据集，key相同的值，都被使用指定的reduce函数聚合到一起。
        val rdd12 = rdd0.reduceByKey((x,y) => x + y)
        rdd12.collect
        res34: Array[(Int, Int)] = Array((1,6), (2,6))
    
    12.aggregateByKey
    
        aggreateByKey(zeroValue: U)(seqOp: (U, T)=> U, combOp: (U, U) =>U) 和reduceByKey的不同在于，reduceByKey输入输出都是(K,
        V)，而aggreateByKey输出是(K,U)，可以不同于输入(K, V) ，aggreateByKey的三个参数：
        zeroValue: U，初始值，比如空列表{} ；
        seqOp: (U,T)=> U，seq操作符，描述如何将T合并入U，比如如何将item合并到列表 ；
        combOp: (U,U) =>U，comb操作符，描述如果合并两个U，比如合并两个列表 ；
        所以aggreateByKey可以看成更高抽象的，更灵活的reduce或group 。
        val z = sc.parallelize(List(1,2,3,4,5,6), 2)
        z.aggreate(0)(math.max(_, _), _ + _)
        res40: Int = 9
        val z = sc.parallelize(List((1, 3), (1, 2), (1, 4), (2, 3)))
        z.aggregateByKey(0)(math.max(_, _), _ + _)
        res2: Array[(Int, Int)] = Array((2,3), (1,9))
    
    13.combineByKey
    
        combineByKey是对RDD中的数据集按照Key进行聚合操作。聚合操作的逻辑是通过自定义函数提供给combineByKey。
        combineByKey[C](createCombiner: (V) ⇒ C, mergeValue: (C, V) ⇒ C, mergeCombiners: (C, C)
        ⇒ C, numPartitions: Int):RDD[(K, C)]把(K,V) 类型的RDD转换为(K,C)类型的RDD，C和V可以不一样。combineByKey三个参数：
        val data = Array((1, 1.0), (1, 2.0), (1, 3.0), (2, 4.0), (2, 5.0), (2, 6.0))
        val rdd = sc.parallelize(data, 2)
        val combine1 = rdd.combineByKey(createCombiner = (v:Double) => (v:Double, 1),
        mergeValue = (c:(Double, Int), v:Double) => (c._1 + v, c._2 + 1),
        mergeCombiners = (c1:(Double, Int), c2:(Double, Int)) => (c1._1 + c2._1, c1._2 + c2._2),numPartitions = 2 )combine1.collect
        res0: Array[(Int, (Double, Int))] = Array((2,(15.0,3)), (1,(6.0,3)))
    
    14.sortByKey
    
        sortByKey([ascending],[numTasks])是排序操作，对(K,V)类型的数据按照K进行排序，其中K需要实现Ordered方法。
        val rdd14 = rdd0.sortByKey()
        rdd14.collect
        res36: Array[(Int, Int)] = Array((1,1), (1,2), (1,3), (2,1), (2,2), (2,3))
    
    15.join
    
        join(otherDataset, [numTasks])是连接操作，将输入数据集(K,V)和另外一个数据集(K,W)进行Join， 得到(K, (V,W))；该操作是对于相同K的V和W集合进行笛卡尔积 操作，也即V和W的所有组合；
        val rdd15 = rdd0.join(rdd0)
        rdd15.collect
        res37: Array[(Int, (Int, Int))] = Array((1,(1,1)), (1,(1,2)), (1,(1,3)), (1,(2,1)), (1,(2,2)), (1,(2,3)), (1,(3,1)), (1,(3,2)), (1,(3,3)), (2,(1,1)),(2,(1,2)), (2,(1,3)), (2,(2,1)), (2,(2,2)), (2,(2,3)), (2,(3,1)), (2,(3,2)), (2,(3,3)))
        连接操作除join 外，还有左连接、右连接、全连接操作函数： leftOuterJoin、rightOuterJoin、fullOuterJoin。
    
    16.cogroup
    
        cogroup(otherDataset, [numTasks])是将输入数据集(K, V)和另外一个数据集(K, W)进行cogroup，得到一个格式为(K, Seq[V], Seq[W])的数据集。
        val rdd16 = rdd0.cogroup(rdd0)
        rdd16.collect
        res38: Array[(Int, (Iterable[Int], Iterable[Int]))] = Array((1,(ArrayBuffer(1, 2, 3),ArrayBuffer(1, 2, 3))), (2,(ArrayBuffer(1, 2,3),ArrayBuffer(1, 2, 3))))
    
    17.cartesian
    
        cartesian(otherDataset)是做笛卡尔积：对于数据集T和U 进行笛卡尔积操作， 得到(T, U)格式的数据集。
        val rdd17 = rdd1.cartesian(rdd3)
        rdd17.collect
        res39: Array[(Int, Int)] = Array((1,12), (2,12), (3,12), (1,14), (1,16), (1,18), (2,14), (2,16), (2,18), (3,14), (3,16), (3,18), (4,12), (5,12),(6,12), (4,14), (4,16), (4,18), (5,14), (5,16), (5,18), (6,14), (6,16), (6,18), (7,12), (8,12), (9,12), (7,14), (7,16), (7,18), (8,14), (8,16),(8,18), (9,14), (9,16), (9,18))

2. 动作

    1.reduce
    
        reduce(func)是对数据集的所有元素执行聚集(func)函数，该函数必须是可交换的。
        val rdd1 = sc.parallelize(1 to 9, 3)
        val rdd2 = rdd1.reduce(_ + _)
        rdd2: Int = 45
    
    2.collect
    
        collect相当于toArray，toArray已经过时不推荐使用，collect将分布式的RDD返回为一个单机的scala Array数组。在这个数组上运用scala的函数式操作。
        rdd1.collect()
        res8: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9)
    
    3.count
    
        返回数据集中元素的个数。
        rdd1.count()
        res9: Long = 9
    
    4.first
    
        返回数据集中的第一个元素， 类似于take(1)。
        rdd1.first()
        res10: Int = 1
    
    5.take
    
        Take(n)返回一个包含数据集中前n个元素的数组， 当前该操作不能并行。
        rdd1.take(3)
        res11: Array[Int] = Array(1, 2, 3)
    
    6.takeSample
    
        takeSample(withReplacement,num, [seed])返回包含随机的num个元素的数组，和Sample不同，takeSample 是行动操作，所以返回的是数组而不是RDD ， 其中第一个参数withReplacement是抽样时是否放回，第二个参数num会精确指定抽样数，而不是比例。
        rdd1.takeSample(true, 4)
        res15: Array[Int] = Array(9, 5, 5, 6)
    7.takeOrdered
    
        takeOrdered(n， [ordering])是返回包含随机的n个元素的数组，按照顺序输出。
        rdd1.takeOrdered(4)
        res16: Array[Int] = Array(1, 2, 3, 4)
    
    8.saveAsTextFile
    
        把数据集中的元素写到一个文本文件，Spark会对每个元素调用toString方法来把每个元素存成文本文件的一行。存储到HDFS的指定目录。
    
    9.countByKey
    
        对于(K, V)类型的RDD. 返回一个(K, Int)的map， Int为K的个数。
    
    10.foreach
    
        foreach(func)是对数据集中的每个元素都执行func函数。不返回RDD和Array，而是返回Uint。
    
    11.saveAsObjectFile
    
        saveAsObjectFile将分区中的每10个元素组成一个Array，然后将这个Array序列化，映射为（Null，BytesWritable（Y））的元素，写入HDFS为SequenceFile的格式。
    
    12.collectAsMap
    
        collectAsMap对（K，V）型的RDD数据返回一个单机HashMap。对于重复K的RDD元素，后面的元素覆盖前面的元素。
    
    13.reduceByKeyLocally
    
        实现的是先reduce再collectAsMap的功能，先对RDD的整体进行reduce操作，然后再收集所有结果返回为一个HashMap。
    
    14.lookup
    
        Lookup函数对（Key，Value）型的RDD操作，返回指定Key对应的元素形成的Seq。这个函数处理优化的部分在于，如果这个RDD包含分区器，则只会对应处理K所在的分区，然后返回由（K，V）形成的Seq。如果RDD不包含分区器，则需要对全RDD元素进行暴力扫描处理，搜索指定K对应的元素。
    
    15.top
    
        top可返回最大的k个元素。
    
    16.reduce
    
        reduce函数相当于对RDD中的元素进行reduceLeft函数的操作。函数实现如下。
        Some（iter.reduceLeft（cleanF））
        reduceLeft先对两个元素<K，V>进行reduce函数操作，然后将结果和迭代器取出的下一个元素<k，V>进行reduce函数操作，直到迭代器遍历完所有元素，得到最后结果。
        在RDD中，先对每个分区中的所有元素<K，V>的集合分别进行reduceLeft。每个分区形成的结果相当于一个元素<K，V>，再对这个结果集合进行reduceleft操作。
    
    17.fold
    
        fold和reduce的原理相同，但是与reduce不同，相当于每个reduce时，迭代器取的第一个元素是zeroValue。
