---
title: Data Factory használati eset – termékre vonatkozó javaslatok
description: További információ a Azure Data Factory használatával megvalósított használati esetekről, valamint más szolgáltatásokkal.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b20eef90cebab59f823e1766d28eb355f317463d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387189"
---
# <a name="use-case---product-recommendations"></a>Használati eset – termékkel kapcsolatos javaslatok
Azure Data Factory a megoldási gyorssegédek Cortana Intelligence Suite megvalósításához használt számos szolgáltatás egyike.  A csomaggal kapcsolatos részletekért tekintse meg [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) oldalt. Ebben a dokumentumban általános használati esetet ismertetünk, amelyet az Azure-felhasználók már megoldottak és implementáltak Azure Data Factory és más Cortana Intelligence összetevő-szolgáltatások használatával.

## <a name="scenario"></a>Eset
Az online kereskedők gyakran szeretnék megcsábítani ügyfeleiket termékeik megvásárlására azáltal, hogy olyan termékekkel látják el őket, amelyek nagy valószínűséggel érdeklik, és így nagy valószínűséggel vásárolnak. Ennek elvégzéséhez az online kiskereskedőknek személyre szabott termékre vonatkozó javaslatokkal kell testreszabnia a felhasználó online élményét. Ezeket a személyre szabott javaslatokat a jelenlegi és a korábbi vásárlási viselkedési adatok, a termékinformáció, az újonnan bevezetett márkák, valamint a termékek és az ügyfelek szegmentálási adatai alapján kell elvégezni.  Emellett a felhasználói termékre vonatkozó javaslatokat is megadhatnak a felhasználók összesített viselkedésének elemzése alapján.

Ezeknek a kereskedőknek a célja, hogy optimalizálják a felhasználók kattintás utáni átalakítását, és magasabb értékesítési bevételt nyerjenek.  Ezt a konverziót a kontextust, viselkedésen alapuló termékre vonatkozó ajánlásokat biztosítanak az ügyfelek érdekei és műveletei alapján. Ebben a használati esetben az online kiskereskedőket használjuk példaként az ügyfeleknek optimalizálni kívánt vállalkozásokra. Ezek az alapelvek azonban minden olyan vállalkozásra érvényesek, amely az ügyfeleit a termékeit és szolgáltatásait szeretné felvenni, és a személyre szabott termékekkel kapcsolatos javaslatokkal fokozza ügyfeleik vásárlási tapasztalatát.

## <a name="challenges"></a>Problémák
Az ilyen típusú használati eset megvalósítására tett kísérlet során számos kihívással szembesülnek az online kereskedők. 

Először is, a különböző méretek és formák adatait több adatforrásból kell bevenni, a helyszínen és a felhőben egyaránt. Ezek az adatforgalom tartalmazza a termékadatokat, a korábbi ügyfél-viselkedési információkat és a felhasználói információkat, amikor a felhasználó megkeresi az online kereskedelmi webhelyet. 

Másodszor, a személyre szabott termékre vonatkozó ajánlásokat ésszerűen és pontosan ki kell számítani, és előre jelezni kell. A termékre, a márkára, valamint az ügyfél viselkedésére és a böngészőre vonatkozó adatok mellett az online kereskedőknek is szerepelniük kell az ügyfelekkel kapcsolatos visszajelzések a múltbeli vásárlásokkal kapcsolatban, hogy a legjobb termékre vonatkozó javaslatok meglegyenek a felhasználó számára. 

Harmadszor, a felhasználónak azonnal el kell látnia a felhasználót, hogy zökkenőmentes böngészési és vásárlási élményt nyújtson, és megadja a legfrissebb és a vonatkozó javaslatokat. 

Végezetül a kiskereskedőknek a megközelítésük eredményességét kell mérniük azáltal, hogy nyomon követik a teljes értékesítési és az értékesítésre való kattintásra való áttérést, és a későbbi javaslatokhoz igazodnak.

## <a name="solution-overview"></a>Megoldási áttekintés
A példában szereplő használati esetet a Azure Data Factory és más Cortana Intelligence összetevő-szolgáltatások, például a [HDInsight](https://azure.microsoft.com/services/hdinsight/) és a [Power bi](https://powerbi.microsoft.com/)használatával oldották meg és implementálták a valódi Azure-felhasználók.

Az online kiskereskedő egy Azure BLOB-tárolót, egy helyszíni SQL Server, Azure SQL Databaset és egy, a munkafolyamaton belüli adattárolási lehetőségekkel rendelkező kapcsolati data martt használ.  A blob-tároló a vásárlói adatokat, az ügyfél viselkedési adatait és a termékinformáció adatait tartalmazza. A termékinformáció-adatok tartalmazzák a termékre vonatkozó információkat és a helyszínen tárolt termékkatalógust az Azure szinapszis Analyticsben. 

Az összes információ össze van építve és egy termék-ajánlási rendszerbe kerül, hogy személyre szabott ajánlásokat nyújtson az ügyfelek érdeklődési köre és műveletei alapján, míg a felhasználó a webhelyen lévő katalógusban böngészhet a termékek között. Az ügyfelek olyan termékeket is láthatnak, amelyek a termékhez kapcsolódnak, és a webhelyek általános használati mintái alapján működnek, amelyek nem kapcsolódnak egyetlen felhasználóhoz sem.

![használati eset diagramja](./media/data-factory-product-reco-usecase/diagram-1.png)

A rendszer a nyers webnapló fájljait naponta generálja az online kereskedő webhelyéről, félig strukturált fájlok formájában. A nyers webes naplófájlok és az ügyfél-és Termékkatalógus-információk rendszeresen bekerülnek az Azure Blob Storage-ba az Data Factory globálisan üzembe helyezett adatáthelyezési szolgáltatásával. A nap nyers naplófájljai a blob Storage-ban (év és hónap) particionálva lesznek a hosszú távú tároláshoz.  Az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) a blob-tárolóban található nyers naplófájlok particionálására, valamint a kaptár és a Pig szkriptek használatával feldolgozza a felhasznált naplókat. A rendszer feldolgozza a particionált webes naplók adatait, hogy kibontsa a gépi tanulási javaslatrendszer szükséges bemeneteit a személyre szabott termékre vonatkozó javaslatok létrehozásához.

Az ebben a példában a gépi tanuláshoz használt ajánlási rendszer az [Apache Mahout](https://mahout.apache.org/)nyílt forráskódú gépi tanulási javaslati platformja.  A forgatókönyvre bármilyen [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) vagy egyéni modell alkalmazható.  A Mahout modell a webhelyhez tartozó elemek közötti hasonlóság előrejelzésére szolgál a teljes használati minták alapján, valamint a személyre szabott javaslatok létrehozásához az egyes felhasználók alapján.

Végezetül a személyre szabott termékekre vonatkozó ajánlásokat a kiskereskedelmi webhelynek való felhasználás céljából áthelyezik egy rokon data martra.  Az eredményhalmaz közvetlenül is elérhető a blob Storage-ból egy másik alkalmazásból, vagy áthelyezhető további üzletekbe más felhasználók és használati esetek számára.

## <a name="benefits"></a>Előnyök
A termékre vonatkozó ajánlási stratégia optimalizálásával és az üzleti célokkal való összehangolásával a megoldás megfelelt az online kiskereskedelmi értékesítési és marketing-célkitűzéseknek. Emellett hatékony, megbízható és költséghatékony módon működővé tenni és kezelhetik a termékre vonatkozó ajánlási munkafolyamatot. A módszer megkönnyíti számukra a modell frissítését és a hatékonyság finomhangolását az értékesítésre kattintásra váltás sikeressége alapján. A Azure Data Factory használatával elhagyták az időigényes és költséges kézi felhőalapú erőforrás-kezelést, és áthelyezhetik az igény szerinti felhőalapú erőforrás-kezelést. Így időt és pénzt takaríthat meg, és csökkentheti a megoldás üzembe helyezéséhez szükséges időt. Az adatbányászati nézetek és az operatív szolgáltatások állapota könnyen láthatóvá és elhárítható a Azure Portal elérhető intuitív Data Factory monitorozási és felügyeleti felhasználói felülettel. A megoldásuk mostantól ütemezhető és felügyelhető, így a befejezett adatmennyiséget megbízhatóan állítják elő és szállítják a felhasználók számára, és az adatkezelési függőségeket a rendszer automatikusan emberi beavatkozás nélkül kezeli.

Ennek a személyre szabott vásárlási élménynek a biztosításával az online kiskereskedő versenyképesebb és vonzó felhasználói élményt nyújt, így növelheti az értékesítést és az ügyfelek teljes elégedettségét.

