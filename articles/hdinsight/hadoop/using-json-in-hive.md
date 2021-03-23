---
title: '& feldolgozása JSON elemzése Apache Hive-Azure HDInsight'
description: Megtudhatja, hogyan használhatja a JSON-dokumentumokat, és hogyan elemezheti őket az Azure HDInsight Apache Hive használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868936"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>JSON-dokumentumok feldolgozása és elemzése Apache Hive használatával az Azure HDInsight

Megtudhatja, hogyan dolgozhatja fel és elemezheti JavaScript Object Notation (JSON) fájlokat az Azure HDInsight Apache Hive használatával. Ez a cikk a következő JSON-dokumentumot használja:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

A fájl a következő címen érhető el: `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Az Azure Blob Storage és a HDInsight használatával kapcsolatos további információkért lásd: [HDFS-kompatibilis Azure Blob Storage használata a HDInsight-ben való Apache Hadoop](../hdinsight-hadoop-use-blob-storage.md). A fájlt átmásolhatja a fürt alapértelmezett tárolójába.

Ebben a cikkben a Apache Hive-konzolt használja. A kaptár-konzol megnyitásával kapcsolatos utasításokért lásd: az [Apache Ambari kaptár nézet használata a HDInsight-ben való Apache Hadoop](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> A kaptár nézet már nem érhető el a HDInsight 4,0-ben.

## <a name="flatten-json-documents"></a>JSON-dokumentumok összeolvasztása

A következő szakaszban felsorolt metódusok megkövetelik, hogy a JSON-dokumentum egyetlen sorból legyen kibontva. Ezért a JSON-dokumentumot egy sztringbe kell lelapulnia. Ha a JSON-dokumentum már össze van tömörítve, kihagyhatja ezt a lépést, és közvetlenül a következő szakaszhoz juthat a JSON-adatok elemzéséhez. A JSON-dokumentum lelapulához futtassa a következő parancsfájlt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

A nyers JSON-fájl a következő helyen található: `wasb://processjson@hditutorialdata.blob.core.windows.net/` . A **StudentsRaw** struktúra tábla a nem LAPÍTOTT nyers JSON-dokumentumra mutat.

A **StudentsOneLine** -struktúra tábla a **/JSON/Students/** elérési útja alatt tárolja az HDInsight alapértelmezett fájlrendszerében tárolt értékeket.

Az **Insert** utasítás feltölti a **StudentOneLine** TÁBLÁT az összeolvasztott JSON-adatokkal.

A **Select** utasítás csak egy sort ad vissza.

Itt látható a **Select** utasítás kimenete:

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="A JSON-dokumentum HDInsight összeolvasztása" border="true":::

## <a name="analyze-json-documents-in-hive"></a>JSON-dokumentumok elemzése a kaptárban

A kaptár három különböző mechanizmust biztosít a lekérdezések JSON-dokumentumokon való futtatásához, vagy saját maga is írhat:

* Használja a get_json_object felhasználó által definiált függvényt (UDF).
* Használja az json_tuple UDF-t.
* Használja az egyéni szerializáló/deszerializáló (SerDe) használatát.
* Saját UDF-t írhat a Python vagy más nyelvek használatával. A saját Python-kódok struktúrával való futtatásával kapcsolatos további információkért lásd: [PYTHON UDF Apache Hive és Apache Pig](./python-udf-hdinsight.md)használatával.

### <a name="use-the-get_json_object-udf"></a>Az get_json_object UDF használata

A kaptár egy [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) nevű beépített UDF-t biztosít, amely a JSON-t kérdezi le futásidőben. Ez a metódus két argumentumot vesz fel: a tábla nevét és a metódus nevét. A metódus neve az összeolvasztott JSON-dokumentummal és az elemezni kívánt JSON-mezővel rendelkezik. Lássunk egy példát, hogy lássuk, hogyan működik ez az UDF.

A következő lekérdezés az egyes tanulók vezetéknevét és vezetéknevét adja vissza:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

A következő kimenet jelenik meg, amikor futtatja ezt a lekérdezést a konzol ablakban:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="A Apache Hive JSON-objektumot kap UDF" border="true":::

A get_json_object UDF korlátai vannak:

* Mivel a lekérdezés egyes mezői a lekérdezés újraelemzését igénylik, hatással vannak a teljesítményre.
* **Get \_ A JSON_OBJECT ()** függvény egy tömb karakterlánc-ábrázolását adja vissza. Ahhoz, hogy a tömböt egy struktúra-tömbre konvertálja, reguláris kifejezésekkel kell helyettesítenie a szögletes zárójeleket ([) és a "]"), majd meg kell hívnia a felosztást a tömb beszerzéséhez.

Az átalakítás oka, hogy a kaptár wiki a **json_tuple** használatát javasolja.  

### <a name="use-the-json_tuple-udf"></a>Az json_tuple UDF használata

A kaptár által biztosított egy másik UDF-t [json_tuplenak](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)nevezzük, amely jobb, mint [get_ JSON-_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ez a metódus kulcsok és JSON-karakterláncok készletét veszi figyelembe. Ezután visszaadja az értékek egy rekordját. A következő lekérdezés a tanulói azonosítót és a JSON-dokumentum fokozatát adja vissza:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A szkript kimenete a kaptár-konzolon:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive JSON-lekérdezés eredményei" border="true":::

Az `json_tuple` UDF az [oldalirányú nézet](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) szintaxisát használja a kaptárban, amely lehetővé teszi, hogy a JSON- \_ rekord hozzon létre egy virtuális táblázatot úgy, hogy a UDT függvényt alkalmazza az eredeti tábla minden egyes sorára. Az összetett JSON-okból az **oldalirányú nézet** ismételt használata miatt túlságosan nehézkesek lesznek. Emellett a **JSON_TUPLE** nem tudja kezelni a beágyazott JSON-ket.

### <a name="use-a-custom-serde"></a>Egyéni SerDe használata

A SerDe a legjobb választás a beágyazott JSON-dokumentumok elemzéséhez. Lehetővé teszi a JSON-séma definiálását, majd a séma segítségével elemezheti a dokumentumokat. Útmutatásért lásd: [Egyéni JSON-SerDe használata Microsoft Azure HDInsight használatával](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Összefoglalás

A kiválasztott kaptárban található JSON-operátor típusa a forgatókönyvtől függ. Ha egy egyszerű JSON-dokumentummal és egy mezővel keres, válassza ki a kaptár UDF **get_json_object**. Ha egynél több kulcsot keres, akkor használhatja a **json_tuple**. Beágyazott dokumentumok esetén használja a **JSON-SerDe**.

## <a name="next-steps"></a>Következő lépések

Kapcsolódó cikkek esetében lásd:

* [A Apache Hive és a HiveQL használata a HDInsight Apache Hadoop a minta Apache log4j-fájl elemzéséhez](./hdinsight-use-hive.md)
* [Repülési késleltetési adatelemzések elemzése a HDInsight interaktív lekérdezés használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)