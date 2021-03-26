---
title: Az Azure HDInsight Apache Hive az átjáró részletes ismertetése és ajánlott eljárásai
description: Ismerje meg, hogyan navigálhat a kaptár-lekérdezések Azure HDInsight-átjárón való futtatásának ajánlott eljárásaiban
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 549eab1547b75eb9461b23df2c157290943b4ed9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869786"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Az Azure HDInsight Apache Hive az átjáró részletes ismertetése és ajánlott eljárásai

Az Azure HDInsight-átjáró (átjáró) a HDInsight-fürtök HTTPS-felülete. Az átjáró feladata a hitelesítés, a gazdagépek feloldása, a szolgáltatások felderítése és a modern elosztott rendszerekhez szükséges egyéb hasznos funkciók. Az átjáró által biztosított funkciók némelyike olyan terhelést eredményez, amelyre ez a dokumentum a navigálni kívánt ajánlott eljárásokat írja le. Az átjárókkal kapcsolatos hibaelhárítási technikákat is tárgyaljuk.

## <a name="the-hdinsight-gateway"></a>Az HDInsight-átjáró

Az HDInsight-átjáró a HDInsight-fürt egyetlen része, amely nyilvánosan elérhető az interneten keresztül. Az átjáró szolgáltatás a `clustername-int.azurehdinsight.net` belső átjáró végpontjának használatával érhető el anélkül, hogy a nyilvános interneten keresztül kellene meghaladnia. A belső átjáró végpontja lehetővé teszi, hogy a kapcsolatok az átjáró szolgáltatáshoz legyenek létrehozva a fürt virtuális hálózatának bezárása nélkül. Az átjáró kezeli a fürtnek küldött összes kérelmet, és továbbítja ezeket a kérelmeket a megfelelő összetevőkhöz és fürt gazdagépekhez.

Az alábbi ábrán egy durva ábrán látható, hogy az átjáró hogyan nyújt absztrakciót a HDInsight belüli különböző gazdagép-feloldási lehetőségek előtt.

:::image type="content" source="./media/gateway-best-practices/host-resolution-diagram.png " alt-text="Gazdagép-feloldási diagram" border="true":::

## <a name="motivation"></a>Motiváció

Az átjárónak a HDInsight-fürtök előtt történő elhelyezésének indítéka a szolgáltatások felderítésére és a felhasználói hitelesítésre szolgáló felület biztosítása. Az átjáró által biztosított hitelesítési mechanizmusok különösen az ESP-kompatibilis fürtök esetében fontosak.

A szolgáltatások felderítése esetében az átjáró előnye, hogy a fürtben lévő minden egyes összetevő az átjáró webhelye () alatt lévő különböző végpontként érhető el `clustername.azurehdinsight.net/hive2` , szemben számos `host:port` párosítással.

A hitelesítéshez az átjáró lehetővé teszi a felhasználók számára, hogy `username:password` hitelesítő adatokat használjanak. Az ESP-kompatibilis fürtök esetében ez a hitelesítő adat a felhasználó tartományának felhasználónevét és jelszavát fogja használni. Az átjárón keresztüli HDInsight-fürtök hitelesítéséhez nem szükséges, hogy az ügyfél Kerberos jegyet szerezzen. Mivel az átjáró fogadja a `username:password` hitelesítő adatokat, és beolvassa a felhasználó Kerberos-jegyét a felhasználó nevében, a biztonságos kapcsolatok bármely ügyfél-gazdagépről elérhetik az átjárót, beleértve a különböző AA-DDS tartományokhoz csatlakozó ügyfeleket, mint az (ESP) fürtöt.

## <a name="best-practices"></a>Ajánlott eljárások

Az átjáró egyetlen szolgáltatás (a terheléselosztás két gazdagép között) felelős a kérelmek továbbításához és hitelesítéséhez. Előfordulhat, hogy az átjáró egy adott méretet meghaladó mértékben szűk keresztmetszetet okoz a struktúra-lekérdezéseknél. A lekérdezési teljesítmény romlása akkor fordulhat elő, ha a nagyon nagy **választó** lekérdezések végrehajtása az átjárón az ODBC vagy a JDBC használatával történik. A "nagyon nagy" olyan lekérdezések, amelyek több mint 5 GB adatmennyiséget tesznek elérhetővé sorokon vagy oszlopokon belül. A lekérdezés tartalmazhatja a sorok hosszú listáját és az oszlopok széles számát.

A nagy méretű lekérdezéseknél az átjáró teljesítménybeli romlása azért van, mert az adatokat át kell vinni a mögöttes adattárból (ADLS Gen2) a következő helyre: a HDInsight kaptár-kiszolgáló, az átjáró, végül pedig a JDBC vagy az ODBC-illesztőn keresztül az ügyfél gazdagépe.

A következő ábra a SELECT lekérdezés lépéseit szemlélteti.

:::image type="content" source="./media/gateway-best-practices/result-retrieval-diagram.png " alt-text="Eredmény diagram" border="true":::

A Apache Hive egy HDFS-kompatibilis fájlrendszerhez kapcsolódó összevont absztrakció. Ez az absztrakció azt jelenti, hogy a struktúra **Select** utasításai a fájlrendszerben lévő **olvasási** műveleteknek felelnek meg. Az **olvasási** műveleteket a rendszer a megfelelő sémára fordítja, mielőtt a jelentést a felhasználónak. A folyamat késése az adatmérettel és a végfelhasználók eléréséhez szükséges összes ugrással nő.

A nagyméretű adatok **létrehozására** vagy **beszúrására** vonatkozó utasítások végrehajtásakor hasonló viselkedés fordulhat elő, mivel ezek a parancsok a mögöttes fájlrendszer **írási** műveleteinek felelnek meg. Az **Insert** vagy a **Load**(betöltés) helyett érdemes lehet olyan adatírást használni, mint például a nyers ork a fájlrendszer/datalake.

Az Enterprise Security Pack-kompatibilis fürtökön a megfelelően összetett Apache Ranger-házirendek a lekérdezés fordítási idejének lassulását okozhatják, ami az átjáró időtúllépéséhez vezethet. Ha egy ESP-fürtön egy átjáró időtúllépését észlelte, érdemes lehet csökkenteni vagy kombinálni a Ranger-szabályzatok számát.

## <a name="troubleshooting-techniques"></a>Hibaelhárítási technikák

A fenti viselkedés részeként több helyszín áll rendelkezésre a teljesítménybeli problémák enyhítésére és megismerésére. Ha a lekérdezés teljesítményének romlását tapasztalja a HDInsight-átjárón keresztül, használja a következő feladatlistát:

* Nagyméretű **választó** lekérdezések futtatásakor használja a **limit** záradékot. A **limit** záradék csökkenti az ügyfél gazdagépének jelentett összes sort. A **limit** záradék csak az eredmény generálására vonatkozik, és nem módosítja a lekérdezési tervet. Ha a **limit** záradékot szeretné alkalmazni a lekérdezési tervre, használja a konfigurációt `hive.limit.optimize.enable` . A **korlát** kombinálható egy eltolással az argumentumok **x, y korlátjának** használatával.

* Adja meg az oszlopok érdeklődését a **Select lekérdezések** futtatásakor a **Select \*** parancs használata helyett. Kevesebb oszlop kiválasztásával csökkentheti az olvasott adatmennyiséget.

* Próbálja meg az Apache Beeline használatával futtatni az érdeklődési lekérdezést. Ha az Apache Beeline használatával történő lekérés hosszabb időt vesz igénybe, a rendszer késlelteti az azonos eredmények külső eszközökön keresztüli lekérését.

* Egy alapszintű kaptár-lekérdezés tesztelésével biztosíthatja, hogy a HDInsight-átjáróval létesített kapcsolatok is meghozhatók. Futtasson egy alapszintű lekérdezést két vagy több külső eszközről, és győződjön meg arról, hogy egyetlen eszköz sem fut a problémák között.

* Tekintse át az Apache Ambari-riasztásokat, és győződjön meg arról, hogy a HDInsight szolgáltatások kifogástalan állapotban vannak. A Ambari-riasztásokat a jelentéskészítés és az elemzés Azure Monitor integrálhatja.

* Ha külső kaptár-Metaadattár használ, ellenőrizze, hogy a struktúra Metaadattár tartozó Azure SQL DB-DTU nem érte-e el a korlátot. Ha a DTU eléri a korlátot, meg kell emelnie az adatbázis méretét.

* Győződjön meg arról, hogy a harmadik féltől származó eszközök (például a PBI vagy a tabló) tördelést használnak a táblák vagy adatbázisok megjelenítéséhez. A tördeléssel kapcsolatos segítségért forduljon a támogatási partnerekhez ezekhez az eszközökhöz. A tördeléshez használt fő eszköz a JDBC `fetchSize` paraméter. A kis lekéréses méret csökkentett teljesítményű átjárót eredményezhet, de a lekérési méret túl nagy lehet, ha az átjáró időtúllépést okoz. A beolvasási méret finomhangolását munkaterhelés alapján kell elvégezni.

* Ha az adatcsatorna nagy mennyiségű adat olvasását foglalja magában a HDInsight-fürt mögöttes tárolójában, érdemes lehet olyan eszközt használni, amely közvetlenül az Azure Storage-hoz, például a Azure Data Factory

* Az interaktív munkaterhelések futtatásakor érdemes Apache Hive LLAP használni, mivel a LLAP zökkenőmentesebb élményt biztosíthat a lekérdezési eredmények gyors visszaadásához

* Érdemes lehet növelni a kaptár Metaadattár szolgáltatás számára elérhető szálak számát a használatával `hive.server2.thrift.max.worker.threads` . Ez a beállítás különösen akkor fontos, ha nagy számú egyidejű felhasználó küld lekérdezést a fürtnek

* Csökkentse az átjáró bármely külső eszközről való eléréséhez használt újrapróbálkozások számát. Ha több újrapróbálkozás van használatban, érdemes lehet egy exponenciális visszalépési újrapróbálkozási szabályzatot alkalmazni.

* Érdemes lehet a tömörítési struktúrát a konfigurációk és a használatával engedélyezni `hive.exec.compress.output` `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Következő lépések

* [Apache Beeline a HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [HDInsight-átjáró időtúllépésével kapcsolatos hibaelhárítási lépések](./troubleshoot-gateway-timeout.md)
* [HDInsight virtuális hálózatok](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight expressz útvonallal](../connect-on-premises-network.md)