---
title: GYIK – Azure szinapszis Analytics
description: Az Azure szinapszis Analytics szolgáltatással kapcsolatos gyakori kérdések
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695821"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure szinapszis Analytics – gyakori kérdések

Ebben az útmutatóban az Azure szinapszis Analytics szolgáltatással kapcsolatos leggyakrabban feltett kérdéseket találja.

## <a name="general"></a>Általános kérdések

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>K: Hogyan használhatom a RBAC-szerepköröket a munkaterület biztonságossá tételéhez?

A: az Azure szinapszis számos olyan szerepkört és hatókört vezet be, amelyekkel hozzárendelheti azokat, így egyszerűbbé válik a munkaterület biztonságossá tétele.

Szinapszis RBAC szerepkörei:
* Szinapszis-rendszergazda
* Szinapszis SQL-rendszergazda
* Szinapszis Spark-rendszergazda
* Szinapszis közreműködő (előzetes verzió)
* Szinapszis-összetevő közzétevője (előzetes verzió)
* Szinapszis-összetevő felhasználója (előzetes verzió)
* Szinapszis számítási operátor (előzetes verzió)
* Szinapszis hitelesítő felhasználója (előzetes verzió)

A szinapszis-munkaterület biztonságossá tételéhez rendelje hozzá a RBAC szerepköröket a következő RBAC-hatókörökhöz:
* Munkaterületek
* Spark-készletek
* Integrációs modulok
* Társított szolgáltatások
* Hitelesítő adatok

Emellett a dedikált SQL-készletekkel azonos biztonsági funkciókkal rendelkezik, amelyeket ismer és szeret.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>K: Hogyan a dedikált SQL-készletek, a kiszolgáló nélküli SQL-készletek és a kiszolgáló nélküli Spark-készletek vezérlését?

A: kiindulási pontként az Azure szinapszis az Azure-előfizetési szinten elérhető beépített Cost Analysis and Cost-riasztásokkal működik.

- Dedikált SQL-készletek – közvetlen láthatósággal láthatja el a költségeket, és szabályozhatja a költségeket, mivel a dedikált SQL-készletek méretét hozza létre és adja meg. Az Azure RBAC szerepköreivel tovább vezérelheti, hogy mely felhasználók hozhatnak létre vagy méretezhetők fel dedikált SQL-készleteket.

- Kiszolgáló nélküli SQL-készletek – olyan figyelési és költséghatékonysági szabályozással rendelkezik, amelyek napi, heti és havi szinten teszik lehetővé a költségek megtartását. További információkért [lásd: a kiszolgáló nélküli SQL-készlet költséghatékonysága](./sql/data-processed.md) . 

- Kiszolgáló nélküli Spark-készletek – korlátozhatja, hogy kik hozhatnak létre RBAC-szerepkörökkel rendelkező Spark-készleteket.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>K: a szinapszis munkaterület támogatja az objektumok szervezetét és a részletességet a GA-ban?

A: A szinapszis-munkaterületek támogatja a felhasználó által definiált mappákat.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>K: Csatolhatok több Power BI munkaterületet egyetlen Azure szinapszis-munkaterülethez?
    
A: jelenleg csak egyetlen Power BI munkaterületet kapcsolhat egy Azure szinapszis-munkaterülethez. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>K: a szinapszis Cosmos DB GA-ra mutató hivatkozást mutat?

A: a Apache Spark szinapszis hivatkozása a GA. A kiszolgáló nélküli SQL-készlethez tartozó szinapszis-hivatkozás nyilvános előzetes verzióban érhető el.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>K: az Azure szinapszis munkaterület támogatja a CI/CD-t? 

Válasz: igen! A folyamat összes összetevője, jegyzetfüzete, SQL-szkriptje és Spark-feladatának definíciója a git-ben fog megjelenni. Az összes készlet-definíciót a git as ARM-sablonokban tárolja a rendszer. A dedikált SQL Pool-objektumokat (sémákat, táblákat, nézeteket stb.) a CI/CD-támogatással rendelkező adatbázis-projektekkel felügyeli a rendszer.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>K: Hogyan arról, hogy tudom, milyen hitelesítő adatokat használ a folyamat futtatásához? 

A: a szinapszis-folyamat minden tevékenysége a társított szolgáltatáson belül megadott hitelesítő adatok használatával fut.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>K: a SSIS IRs támogatja a szinapszis integrálását?

A: jelenleg nem. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>K: Hogyan áttelepíteni a meglévő folyamatokat Azure Data Factoryról egy Azure-beli szinapszis-munkaterületre?

A: jelenleg manuálisan újra létre kell hoznia a Azure Data Factory-folyamatokat és a kapcsolódó összetevőket. ehhez exportálnia kell a JSON-t az eredeti folyamatból, és importálnia kell a szinapszis-munkaterületre.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>K: mi a különbség a szinapszis és a Apache Spark Apache Spark között?

A: a Szinapszishoz Apache Spark Apache Spark a más szolgáltatásokkal (HRE, AzureML stb.) és további kódtárakkal (mssparktuils, kolibri) és előre beállított teljesítmény-konfigurációkkal való integráció támogatásával.

A Apache Spark jelenleg futó munkaterhelések Apache Spark az Azure szinapszis-on való módosítás nélkül futnak. 

### <a name="q-what-versions-of-spark-are-available"></a>K: milyen verziójú Spark érhető el?

A: az Azure szinapszis Apache Spark teljes mértékben támogatja a Spark 2,4-et. Az alapvető összetevők és a jelenleg támogatott verziók teljes listáját lásd: [Apache Spark verzió támogatása](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>K: az Azure szinapszis Spark DButils egyenértékű?

A: igen, az Azure szinapszis Apache Spark biztosítja a **mssparkutils** könyvtárat. A segédprogram teljes dokumentációját lásd: [a Microsoft Spark segédprogramok bemutatása](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>K: Hogyan a munkamenet paramétereinek beállítása a Apache Sparkban?

A: a munkamenet paramétereinek megadásához használja a (z)%% configure Magic elérhető lehetőséget. A paraméterek érvénybe léptetéséhez munkamenet-újraindítás szükséges. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>K: Hogyan a fürt szintjének paramétereit kiszolgáló nélküli Spark-készletben?

A: a fürt szintjének paramétereinek megadásához megadhat egy Spark. conf fájlt a Spark-készlethez. Ez a készlet ezt követően a konfigurációs fájlban lévő paramétereket is tiszteletben tartja. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>K: Futtathatok többfelhasználós Spark-fürtöt az Azure szinapszis Analytics szolgáltatásban?
 
A: az Azure szinapszis adott felhasználási esetekhez biztosít célra épülő motorokat. A Szinapszishoz Apache Spark-t nem fürtözött modellként tervezték. Két forgatókönyv van, ahol a felhasználók egy többfelhasználós fürt modelljét kérik.

**Forgatókönyv #1: sok felhasználó fér hozzá a fürthöz az adatok BI-ban való kiszolgálásához.**

Ennek a feladatnak a megvalósításának legegyszerűbb módja, ha az adatokat a Sparktal szeretné főzni, majd kihasználja a szinapszis SQL kiszolgáló képességeinek előnyeit, hogy az adatkészletekhez Power BI tudnak csatlakozni.

**#2 forgatókönyv: több fejlesztő egyetlen fürtön való megtakarítása.**
 
A forgatókönyv kielégítése érdekében minden fejlesztőnek olyan kiszolgáló nélküli Spark-készletet kell adnia, amely kis számú Spark-erőforrás használatára van beállítva. Mivel a kiszolgáló nélküli Spark-készletek nem járnak semmibe, amíg aktívan nem használják őket, a több fejlesztő esetében is csökkentheti a költségeket. A készletek megosztják a metaadatokat (Spark-táblázatok), így könnyen dolgozhatnak egymással.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>K: Hogyan a kódtárakat, a felügyeletet és a telepítést?

Válasz: külső csomagokat telepíthet requirements.txt fájl segítségével a Spark-készlet létrehozásakor, a szinapszis munkaterületen vagy a Azure Portal. Lásd: [a Apache Spark könyvtárak kezelése az Azure szinapszis Analyticsben](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Dedikált SQL-készletek

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>K: Milyen különbségek vannak a dedikált SQL-készletek és a kiszolgáló nélküli készletek között?

A: teljes listát talál a [T-SQL szolgáltatásbeli különbségekről a SZINAPSZIS SQL-ben](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>K: most, hogy az Azure szinapszis a GA, hogyan helyezhetek át olyan dedikált SQL-készleteket, amelyek korábban önállóak voltak az Azure Szinapszisban? 

A: nincs "Move" vagy "Migration". Választhat, hogy engedélyezi-e az új munkaterület-szolgáltatásokat a meglévő készleteken. Ha így tesz, az új funkciók, például a szinapszis Studio, a Spark és a kiszolgáló nélküli SQL-készletek is használhatók.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>K: mi a dedikált SQL-készletek alapértelmezett telepítése? 

A: alapértelmezés szerint az összes új dedikált SQL-készletet egy munkaterületre telepíti a rendszer. Ha azonban továbbra is létre kell hoznia egy dedikált SQL-készletet (korábban SQL DW) egy önálló űrlapon. 

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)
