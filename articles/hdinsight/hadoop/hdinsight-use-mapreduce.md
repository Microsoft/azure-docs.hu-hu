---
title: MapReduce Apache Hadoop on HDInsight
description: Ismerje meg, hogyan futtathat Apache MapReduce-feladatokat Apache Hadoop HDInsight-fürtökön.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944251"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>A MapReduce használata a HDInsight-alapú Apache Hadoopban

Ismerje meg, hogyan futtathat MapReduce-feladatokat HDInsight-fürtökön.

## <a name="example-data"></a>Példaadatok

A HDInsight különböző, a és a címtárban tárolt adatkészleteket biztosít `/example/data` `/HdiSamples` . Ezek a könyvtárak a fürt alapértelmezett tárolójában találhatók. Ebben a dokumentumban a `/example/data/gutenberg/davinci.txt` fájlt használjuk. Ez a fájl tartalmazza a Leonardo da Vinci jegyzetfüzeteit.

## <a name="example-mapreduce"></a>Példa MapReduce

A HDInsight-fürt tartalmazza például a MapReduce Word Count alkalmazást. Ez a példa a `/example/jars/hadoop-mapreduce-examples.jar` fürt alapértelmezett tárolójában található.

A következő Java-kód a fájlban található MapReduce-alkalmazás forrása `hadoop-mapreduce-examples.jar` :

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

A saját MapReduce alkalmazások írására vonatkozó utasításokért lásd: [Java MapReduce-alkalmazások fejlesztése a HDInsight-hez](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>A MapReduce futtatása

A HDInsight különféle módszerekkel képes futtatni a HiveQL-feladatokat. A következő táblázat segítségével eldöntheti, hogy melyik módszer a legmegfelelőbb, majd kövesse a bemutató hivatkozását.

| **Használja**... | **... Ehhez** |  ... Ebből az **ügyféloldali operációs rendszerből** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |A Hadoop parancs használata **SSH** -n keresztül |Linux, UNIX, Mac OS X vagy Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |A feladatot távolról küldje el a **Rest** használatával |Linux, UNIX, Mac OS X vagy Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |A feladatot távolról küldje el a **Windows PowerShell** használatával  |Windows |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az HDInsight-beli adatkezelésről, tekintse meg a következő dokumentumokat:

* [Java MapReduce-programok fejlesztése a HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Apache Hive használata a HDInsight](./hdinsight-use-hive.md)
