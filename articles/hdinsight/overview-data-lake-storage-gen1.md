---
title: A HDInsight Azure Data Lake Storage Gen1 áttekintése
description: A HDInsight Data Lake Storage Gen1 áttekintése.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938876"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>A HDInsight Azure Data Lake Storage Gen1 áttekintése

A Azure Data Lake Storage Gen1 egy nagyvállalati szintű nagy kapacitású adattár big data analitikus számítási feladatokhoz. A Azure Data Lake használatával bármilyen méretű, típusú és betöltési sebességet rögzíthet. És egy helyen az operatív és a felderítő elemzéshez.

A WebHDFS-kompatibilis REST API-k használatával férhet hozzá Data Lake Storage Gen1 a Hadoop (HDInsight-fürthöz elérhető). Data Lake Storage Gen1 úgy lett kialakítva, hogy lehetővé tegye az elemzést a tárolt adatokon, és az adatelemzési forgatókönyvekben teljesítményre hangolva. A Gen1 magában foglalja a valós nagyvállalati használati esetekben elengedhetetlen képességeket. Ezen képességek közé tartozik a biztonság, a kezelhetőség, az alkalmazkodóképesség, a megbízhatóság és a rendelkezésre állás.

További információ a Azure Data Lake Storage Gen1ről: [Azure Data Lake Storage Gen1 részletes áttekintése](../data-lake-store/data-lake-store-overview.md).

A Data Lake Storage Gen1 főbb képességei a következők:

## <a name="compatibility-with-hadoop"></a>Kompatibilitás a Hadoop

A Data Lake Storage Gen1 a HDFS és a Hadoop környezettel kompatibilis Apache Hadoop-fájlrendszer.  A WebHDFS API-t használó alkalmazások és szolgáltatások könnyen integrálhatók a Data Lake Storage Gen1okkal. A Data Lake Storage Gen1 a WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazásokhoz.

A Data Lake Storage Gen1ban tárolt adatai könnyen elemezhetők a Hadoop analitikai keretrendszerek használatával. Keretrendszerek, például MapReduce vagy struktúra. Az Azure HDInsight-fürtök üzembe helyezhetők és konfigurálhatók úgy, hogy közvetlenül hozzáférhessenek Data Lake Storage Gen1ban tárolt adatszolgáltatásokhoz.

## <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és alkalmas a különböző típusú adatelemzések tárolására. Nem korlátozza a fiók méreteit vagy a fájlméretet. Vagy egy adattóban tárolható adatmennyiség. Az egyes fájlok mérete kilobájtból petabájt, így a Data Lake Storage Gen1 kiváló választás bármilyen típusú adatok tárolására. A rendszer több másolat készítésével tárolja az tartósan. Az adattárban azonban nem lehet korlátozni az adattárolást.

## <a name="performance-tuning-for-big-data-analytics"></a>Teljesítmény-Finomhangolás a big data elemzésekhez

Data Lake Storage Gen1 analitikai rendszerekhez készült. A nagy mennyiségű adatok lekérdezéséhez és elemzéséhez szükséges nagy átviteli sebességet igénylő rendszerek. A adat-tó több különálló Storage-kiszolgálón is elterjed egy fájl részein. Az adatok elemzésekor ez a telepítő javítja az olvasási sebességet, ha a fájl párhuzamosan van beolvasva.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Nagyvállalati készültség: kiválóan elérhető és biztonságos

A Data Lake Storage Gen1 iparági szintű rendelkezésre állást és megbízhatóságot biztosít. Az adategységek tárolása tartósan történik: a redundáns másolatok megvédik a váratlan hibákat. A vállalatok a meglévő adatplatformjuk fontos részeként használhatják a Data Lake Storage Gen1 a megoldásokban.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is biztosít a tárolt adattároláshoz. További információ: az [adatok biztonságossá tétele a Azure Data Lake Storage Gen1ban](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Rugalmas adatstruktúrák

A Data Lake Storage Gen1 a natív formátumban tárolhatja az összes olyan adatforrást, amely korábbi átalakítások nélkül is használható. Data Lake Storage Gen1 nem szükséges sémát meghatározni az betöltés előtt. Az egyéni elemzési keretrendszer értelmezi az adatelemzést, és definiál egy sémát az elemzés időpontjában. A Data Lake Storage Gen1 képes kezelni a strukturált adatmennyiséget. És félig strukturált és strukturálatlan adat.

Data Lake Storage Gen1 tárolók alapvetően mappák és fájlok. A tárolt adatai SDK-k, a Azure Portal és a Azure PowerShell használatával működnek. Ezekkel a csatolókkal és tárolókkal az áruházba helyezett adattípusok tárolhatók. Data Lake Storage Gen1 nem végez semmilyen speciális adatkezelést az adattípusok alapján.

## <a name="data-security-in-data-lake-storage-gen1"></a>Adatbiztonság a Data Lake Storage Gen1ban

A Data Lake Storage Gen1 a hitelesítéshez Azure Active Directory használ, és hozzáférés-vezérlési listákat (ACL-eket) használ az adataihoz való hozzáférés kezelésére.

| **Szolgáltatás** | **Leírás** |
| --- | --- |
| Hitelesítés |A Data Lake Storage Gen1 az Azure Active Directory (Azure AD) szolgáltatással integrálható az identitás-és hozzáférés-kezeléshez a Data Lake Storage Gen1 tárolt összes adattal. Az integráció miatt Data Lake Storage Gen1 az Azure AD összes funkciójának előnyeit. Ezek a szolgáltatások többek között a következők: többtényezős hitelesítés, feltételes hozzáférés és Azure szerepköralapú hozzáférés-vezérlés. Emellett az alkalmazások használatának figyelése, a biztonsági monitorozás és a riasztások stb. A Data Lake Storage Gen1 támogatja a OAuth 2,0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [Azure Data Lake Storage Gen1on belüli hitelesítés Azure Active Directory használatával](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által közzétett POSIX stílusú engedélyek támogatásával. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. Az ACL-ek Data Lake Storage Gen1 környezetében való működésével kapcsolatos további információkért lásd: [Data Lake Storage Gen1 hozzáférés-vezérlése](../data-lake-store/data-lake-store-access-control.md). |
| Titkosítás |A Data Lake Storage Gen1 a fiókban tárolt adathalmazok titkosítását is biztosítja. Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Megadhatja, hogy az adatai titkosítva legyenek, vagy a titkosítás nélkül is legyenek kiválasztva. További információ: [titkosítás Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-encryption.md). A titkosítással kapcsolatos konfiguráció megadására vonatkozó utasításokért lásd: [a Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md). |

Ha többet szeretne megtudni az Data Lake Storage Gen1-beli adatvédelmet illetően, tekintse meg [a Azure Data Lake Storage Gen1 tárolt adatainak biztonságossá](../data-lake-store/data-lake-store-secure-data.md)tételét ismertető témakört.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1-kompatibilis alkalmazások

A Data Lake Storage Gen1 kompatibilis a Hadoop-környezet legtöbb nyílt forráskódú összetevőjével. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Az alábbi hivatkozásokat követve további információkat tudhat meg arról, hogyan használhatók a Data Lake Storage Gen1 a nyílt forráskódú összetevőkkel és más Azure-szolgáltatásokkal is.

* Tekintse [meg a Azure Data Lake Storage Gen1val működő nyílt forráskódú Big Data alkalmazásokat](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Tekintse meg a [Azure Data Lake Storage Gen1 integrációja más Azure-szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) című témakört, amelyből megtudhatja, hogyan Data Lake Storage Gen1 használhatja más Azure-szolgáltatásokkal a különböző forgatókönyvek széles skáláját.
* Lásd: [a Azure Data Lake Storage Gen1 használata Big Data követelményekhez](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fájlrendszer (adl://)

A Hadoop-környezetekben az új fájlrendszer, a Azuredatalakefilesystemet (adl://) használatával férhet hozzá a Data Lake Storage Gen1hoz. A által használt alkalmazások és szolgáltatások teljesítménye olyan `adl://` módon optimalizálható, amely jelenleg nem érhető el a WebHDFS-ben. Ennek eredményeképpen a rugalmasságot úgy érheti el, hogy a javasolt adl://használatával kihasználja a legjobb teljesítményt. Vagy a meglévő kód fenntartása úgy, hogy továbbra is a WebHDFS API-t használja közvetlenül. Az Azure HDInsight teljes mértékben kihasználja a Azuredatalakefilesystemet, hogy a lehető legjobb teljesítményt nyújtsa Data Lake Storage Gen1on.

A következő URI használatával férhet hozzá az adataihoz Data Lake Storage Gen1ban:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

A Data Lake Storage Gen1 lévő adatok elérésével kapcsolatos további információkért lásd: [a tárolt adatokon elérhető műveletek](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Következő lépések

* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)