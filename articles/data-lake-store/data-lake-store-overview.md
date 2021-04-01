---
title: Mi az az 1. generációs Azure Data Lake Storage? | Microsoft Docs
description: A Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) és a más adattárakban elérhető érték áttekintése
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149235"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Mi az az 1. generációs Azure Data Lake Storage?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

A Azure Data Lake Storage Gen1 a big data analitikus számítási feladatokhoz használható, nagyvállalati szintű, Hyper-skála tárház. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

Data Lake Storage Gen1 a WebHDFS-kompatibilis REST API-k használatával érhető el a Hadoop (HDInsight-fürttel elérhető). A szolgáltatás úgy lett kialakítva, hogy lehetővé tegye az elemzést a tárolt adatokon, valamint az adatelemzési forgatókönyvek teljesítményének finomhangolását. A Data Lake Storage Gen1 minden nagyvállalati szintű funkciót tartalmaz: biztonság, kezelhetőség, skálázhatóság, megbízhatóság és rendelkezésre állás.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Főbb képességek

A Data Lake Storage Gen1 főbb képességei a következők:

### <a name="built-for-hadoop"></a>Hadoop-kompatibilis

Data Lake Storage Gen1 egy Apache Hadoop fájlrendszer, amely kompatibilis a Hadoop elosztott fájlrendszer (HDFS) rendszerrel, és együttműködik a Hadoop-ökoszisztémával. A WebHDFS API-t használó meglévő HDInsight-alkalmazások és-szolgáltatások könnyen integrálhatók Data Lake Storage Gen1val. A Data Lake Storage Gen1 a WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazásokhoz.

A Data Lake Storage Gen1ban tárolt adatelemzéseket egyszerűen elemezheti Hadoop analitikus keretrendszerek, például a MapReduce vagy a kaptár használatával. Kiépítheti az Azure HDInsight-fürtöket, és úgy konfigurálhatja őket, hogy közvetlenül hozzáférhessenek Data Lake Storage Gen1 tárolt adatszolgáltatásokhoz.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

A Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és számos különböző adatelemzést képes tárolni. Nem ró korlátozásokat a fiók méretére, a fájlméretre vagy egy adattóban tárolható adatmennyiségre. Az egyes fájlok mérete a kilobájt és a petabájt között lehet. A rendszer több másolat készítésével tárolja az tartósan. Az adattárolóban tárolt adatmennyiség időtartamára nincs korlát.

### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva

A Data Lake Storage Gen1 nagy méretű elemzési rendszerek futtatására készült, amelyek nagy mennyiségű adatok lekérdezéséhez és elemzéséhez szükségesek. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

### <a name="enterprise-ready-highly-available-and-secure"></a>Vállalati használatra kész: magasan elérhető és biztonságos

A Data Lake Storage Gen1 iparági szintű rendelkezésre állást és megbízhatóságot biztosít. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is biztosít a tárolt adattároláshoz. További információ: az [adatok biztonságossá tétele a Azure Data Lake Storage Gen1ban](#DataLakeStoreSecurity).

### <a name="all-data"></a>Minden adattal

A Data Lake Storage Gen1 a natív formátumban tárolhatja az összes olyan adatforrást, amely korábbi átalakítások nélkül is elvégezhető. A Data Lake Storage Gen1 nem igényli, hogy a rendszer az adatgyűjtés előtt Definiáljon egy sémát, így az egyedi elemzési keretrendszerbe kerül, hogy értelmezze az adatelemzést, és Definiáljon egy sémát az elemzés időpontjában. Tetszőleges méretű fájlok és formátumok tárolásának lehetősége lehetővé teszi, hogy a Data Lake Storage Gen1 strukturált, részben strukturált és strukturálatlan adatmennyiségeket kezeljenek.

Data Lake Storage Gen1 tárolók alapvetően mappák és fájlok. A tárolt adatai az SDK-k, a Azure Portal és az Azure PowerShell használatával működnek. Ha ezen felületek használatával helyezi el az adatait az áruházba, és a megfelelő tárolókat használja, bármilyen típusú adatait tárolhatja. A Data Lake Storage Gen1 nem végez semmilyen speciális adatkezelést az általa tárolt adatok típusától függően.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Adatok védelme

A Data Lake Storage Gen1 a Azure Active Directory (Azure AD) használatával hitelesítő és hozzáférés-vezérlési listákat (ACL-eket) használ az adataihoz való hozzáférés kezeléséhez.

| Szolgáltatás | Leírás |
| --- | --- |
| Hitelesítés |A Data Lake Storage Gen1 az Azure AD-val integrálható az identitás-és hozzáférés-kezeléshez a Data Lake Storage Gen1 tárolt összes adattal. Az integráció miatt Data Lake Storage Gen1 az összes Azure AD-szolgáltatás előnyeit, többek között a többtényezős hitelesítést, a feltételes hozzáférést, az Azure szerepköralapú hozzáférés-vezérlését, az alkalmazások használatának figyelését, a biztonsági monitorozást és a riasztásokat, és így tovább. A Data Lake Storage Gen1 támogatja a OAuth 2,0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [Data Lake Storage Gen1 hitelesítés](data-lakes-store-authentication-using-azure-active-directory.md).|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által elérhető POSIX stílusú engedélyek támogatásával. Engedélyezheti a hozzáférés-vezérlési listákat a gyökérkönyvtárban, az almappákban és az egyes fájlokban. További információ arról, hogy az ACL-ek hogyan működnek a Data Lake Storage Gen1 kontextusában: [Data Lake Storage Gen1 hozzáférés-vezérlése](data-lake-store-access-control.md). |
| Titkosítás |A Data Lake Storage Gen1 a fiókban tárolt adathalmazok titkosítását is biztosítja. Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Megadhatja, hogy az adatai titkosítva legyenek, vagy a titkosítás nélkül is legyenek kiválasztva. További információ: [titkosítás Data Lake Storage Gen1ban](data-lake-store-encryption.md). A titkosítással kapcsolatos konfiguráció megadására vonatkozó utasításokért lásd: [a Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md). |

A Data Lake Storage Gen1ban található adatvédelmet az [Azure Data Lake Storage Gen1ban lévő adatvédelmet](data-lake-store-secure-data.md)ismertető cikkben találhatja meg.

## <a name="application-compatibility"></a>Alkalmazáskompatibilitás

A Data Lake Storage Gen1 kompatibilis a Hadoop-ökoszisztémában található legtöbb nyílt forráskódú összetevővel. Emellett más Azure-szolgáltatásokkal is integrálható. Ha többet szeretne megtudni arról, hogyan használhatja a Data Lake Storage Gen1 nyílt forráskódú összetevőkkel és egyéb Azure-szolgáltatásokkal, használja az alábbi hivatkozásokat:

- Tekintse meg az [Azure Data Lake Storage Gen1-kompatibilis alkalmazásokat és szolgáltatásokat](data-lake-store-compatible-oss-other-applications.md) a Data Lake Storage Gen1ekkel együttműködő nyílt forráskódú alkalmazások listájának megtekintéséhez.
- Tekintse meg az [integráció más Azure-szolgáltatásokkal](data-lake-store-integrate-with-other-services.md) című témakört, amelyből megtudhatja, hogyan használhatja a Data Lake Storage Gen1 más Azure-szolgáltatásokkal a forgatókönyvek szélesebb körének engedélyezéséhez
- Tekintse meg az [Data Lake Storage Gen1 használatának forgatókönyveit](data-lake-store-data-scenarios.md) , amelyekből megtudhatja, hogyan használhatja a Data Lake Storage Gen1 olyan helyzetekben, mint például az adatok betöltése, az adatok feldolgozása, az adatok letöltése és az adatok megjelenítése.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 fájlrendszer

A Data Lake Storage Gen1 a Hadoop-környezetekben elérhető fájlrendszer Azuredatalakefilesystemet (adl://) keresztül érhető el (a HDInsight-fürttel érhető el). A adl://-t használó alkalmazások és szolgáltatások kihasználhatják a WebHDFS-ben jelenleg nem elérhető további teljesítmény-optimalizálást. Ennek eredményeképpen a Data Lake Storage Gen1 rugalmasságot biztosít a legjobb teljesítmény kihasználásához a adl://használatának javasolt beállításával vagy a meglévő kódok fenntartásával, ha továbbra is a WebHDFS API-t használja közvetlenül. Az Azure HDInsight teljes mértékben kihasználja a Azuredatalakefilesystemet, hogy a lehető legjobb teljesítményt nyújtsa Data Lake Storage Gen1on.

Az adatai Data Lake Storage Gen1 használatával érhetők el `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . További információ a Data Lake Storage Gen1 lévő adatok eléréséről: [a tárolt adatok tulajdonságainak megtekintése](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Következő lépések

- [A Data Lake Storage Gen1 használatának első lépései a Azure Portal](data-lake-store-get-started-portal.md)
- [A Data Lake Storage Gen1 használatának első lépései a .NET SDK-val](data-lake-store-get-started-net-sdk.md)
- [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)