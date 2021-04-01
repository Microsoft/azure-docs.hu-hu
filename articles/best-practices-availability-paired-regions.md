---
title: Az üzletmenet folytonosságának biztosítása & a vész-helyreállítási Azure párosított régiók használatával
description: Az alkalmazások rugalmasságának biztosítása az Azure regionális párosításával
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511229"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Az Azure párosított régiói

## <a name="what-are-paired-regions"></a>Mik azok a párosított régiók?

Az Azure-régiók olyan adatközpontokból állnak, amelyek egy késleltetéssel meghatározott kerületben találhatók, és egy dedikált, kis késésű hálózaton keresztül kapcsolódnak egymáshoz.  Ez biztosítja, hogy az Azure-régión belüli Azure-szolgáltatások a lehető legjobb teljesítményt és biztonságot nyújtsanak.  

Az Azure földrajza a világ egy olyan területét határozza meg, amely legalább egy Azure-régiót tartalmaz. A földrajzi területek olyan diszkrét piacot határoznak meg, amely általában két vagy több régiót tartalmaz, amelyek megőrizik az adattárolási és megfelelőségi határokat.  Az Azure globális infrastruktúrájának további információit [itt](https://azure.microsoft.com/global-infrastructure/regions/) találja

A regionális párok két régióból állnak, ugyanazon a földrajzon belül. Az Azure szerializálja a platform frissítéseit (tervezett karbantartás) a regionális párok között, így biztosítva, hogy egyszerre csak egy régió legyen az egyes párok frissítései. Ha egy kimaradás több régióra is hatással van, az egyes párokban legalább egy régiót rangsorolni kell a helyreállításhoz.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Bizonyos Azure-szolgáltatások a párosított régiók további előnyeit biztosítják az üzletmenet folytonosságának biztosításához és az adatvesztés elleni védelemhez.  Az Azure számos [tárolási megoldást](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) kínál, amelyek kihasználják a párosított régiókat az adatelérhetőség biztosításához. Az [Azure geo-redundáns tároló](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) például automatikusan replikálja az adatforrásokat egy másodlagos régióba, így biztosítva, hogy az adatvédelem még abban az esetben is megmaradjon, ha az elsődleges régió nem helyreállítható. 

Vegye figyelembe, hogy az összes Azure-szolgáltatás nem replikálja automatikusan az adatait, és az összes Azure-szolgáltatás nem áll automatikusan vissza egy sikertelen régióból a párja felé.  Ilyen esetekben a helyreállítást és a replikálást az ügyfélnek kell konfigurálnia.

## <a name="can-i-select-my-regional-pairs"></a>Kijelölhetem a regionális párokat?

Nem. Bizonyos Azure-szolgáltatások regionális párokra támaszkodnak, mint például az Azure [redundáns tárterülete](./storage/common/storage-redundancy.md). Ezek a szolgáltatások nem teszik lehetővé, hogy új regionális párosításokat hozzon létre.  Hasonlóképpen, mivel az Azure a regionális párok tervezett karbantartási és helyreállítási rangsorolását vezérli, nem határozhatja meg a saját regionális párokat, hogy kihasználhassa ezeket a szolgáltatásokat. Ugyanakkor saját vész-helyreállítási megoldást is létrehozhat, ha a szolgáltatásokat tetszőleges számú régióban hozza létre, és az Azure-szolgáltatásokat kihasználva párosítja őket. 

Például az Azure-szolgáltatások, például a [AzCopy](./storage/common/storage-use-azcopy-v10.md) használatával ütemezhetik az adatbiztonsági másolatokat egy másik régióban lévő Storage-fiókba.  A [Azure DNS és az Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)segítségével az ügyfelek rugalmas architektúrát tervezhetnek az alkalmazásokhoz, amelyek megőrzik az elsődleges régió elvesztését.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Csak a regionális párokon belüli szolgáltatások használatára van szükségem?

Nem. Míg egy adott Azure-szolgáltatás a regionális párokra támaszkodhat, más szolgáltatásokat is üzemeltetheti bármely régióban, amely megfelel az Ön üzleti igényeinek.  Az Azure GRS Storage-megoldás az USA keleti régiójában található, a Kelet-Kanadában lévő partnerekkel is párosíthatja az adataikat a közép-Kanadában.  

## <a name="must-i-use-azure-regional-pairs"></a>Az Azure regionális párokat kell használniuk?

Nem. Az Azure-szolgáltatásokkal az Azure-szolgáltatások rugalmas szolgáltatást állíthatnak be anélkül, hogy az Azure regionális párokra kellene támaszkodnia.  Azt javasoljuk azonban, hogy az üzletmenet-folytonossági vész-helyreállítást (BCDR) a regionális párokban konfigurálja, hogy kihasználhassa az [elkülönítést](./security/fundamentals/isolation-choices.md) és javítsa a [rendelkezésre állást](./availability-zones/az-overview.md). A több aktív régiót is támogató alkalmazások esetében, ahol lehetséges, a régiópárok mindkét tagjának használatát javasoljuk. Ez biztosítja az alkalmazások optimális rendelkezésre állását és a lehető legkevesebb helyreállítási időt vészhelyzet esetén. Amikor csak lehetséges, tervezze meg az alkalmazását a [rugalmasság](/azure/architecture/framework/resiliency/overview) és a könnyű [helyreállítás](/azure/architecture/framework/resiliency/backup-and-recovery)érdekében.

## <a name="azure-regional-pairs"></a>Azure regionális párok

| Földrajzi hely | A regionális pár | B regionális pár  |
|:--- |:--- |:--- |
| Asia-Pacific |Kelet-Ázsia (Hongkong) | Délkelet-Ázsia (Szingapúr) |
| Ausztrália |Kelet-Ausztrália |Délkelet-Ausztrália |
| Ausztrália |Ausztrália középső régiója |Ausztrália 2. középső régiója |
| Brazília |Dél-Brazília |USA déli középső régiója |
| Kanada |Közép-Kanada |Kelet-Kanada |
| Kína |Észak-Kína |Kelet-Kína|
| Kína |Észak-Kína 2 |Kelet-Kína 2|
| Európa |Észak-Európa (Írország) |Nyugat-Európa (Hollandia) |
| Franciaország |Közép-Franciaország|Dél-Franciaország|
| Németország |Közép-Németország |Északkelet-Németország |
| India |Közép-India |Dél-India |
| India |Nyugat-India |Dél-India |
| Japán |Kelet-Japán |Nyugat-Japán |
| Dél-Korea |Dél-Korea középső régiója |Dél-Korea déli régiója |
| Észak-Amerika |USA keleti régiója |USA nyugati régiója |
| Észak-Amerika |USA 2. keleti régiója |Az USA középső régiója |
| Észak-Amerika |USA északi középső régiója |USA déli középső régiója |
| Észak-Amerika |USA 2. nyugati régiója |USA nyugati középső régiója |
| Norvégia | Kelet-Norvégia | Norvégia nyugati régiója |
| Dél-afrikai Köztársaság | Dél-Afrika északi régiója |Dél-Afrika nyugati régiója |
| Svájc | Észak-Svájc |Nyugat-Svájc |
| Egyesült Királyság |Az Egyesült Királyság nyugati régiója |Az Egyesült Királyság déli régiója |
| Egyesült Arab Emírségek | Észak-Egyesült Arab | UAE középső régiója
| Egyesült Államok Védelmi Minisztériuma |US DoD – Kelet |US DoD – Középső régió |
| Egyesült Államok – Államigazgatás |USA-beli államigazgatás – Arizona |USA-beli államigazgatás – Texas |
| Egyesült Államok – Államigazgatás |US Gov Iowa |USA-beli államigazgatás – Virginia |
| Egyesült Államok – Államigazgatás |USA-beli államigazgatás – Virginia |USA-beli államigazgatás – Texas |

> [!Important]
> - Nyugat-India csak egy irányban párosítható. Nyugat-India másodlagos régiója Dél-India, Dél-India másodlagos régiója azonban Közép-India.
> - A Dél-Brazília egyedi, mert a földrajzon kívüli régióval párosítva van. A Dél-Brazília déli középső régiója, az USA déli régiója. Dél-Közép-USA másodlagos régiója nem Dél-Brazília.


## <a name="an-example-of-paired-regions"></a>Példa a párosított régiókra
Az alábbi ábra egy feltételezett alkalmazást mutat be, amely a regionális párt használja a vész-helyreállításhoz. A zöld számok kiemelik a három Azure-szolgáltatás (Azure-beli számítási, tárolási és adatbázis) régiók közötti tevékenységeit, valamint azt, hogy ezek hogyan vannak konfigurálva a régiók közötti replikálásra. A párosított régiókban való üzembe helyezés egyedi előnyeit a narancssárga számok kiemelik.

![A párosított régió előnyeinek áttekintése](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

2. ábra – feltételezett Azure regionális pár

## <a name="cross-region-activities"></a>Régiók közötti tevékenységek
A 2. ábrán említettek szerint.

1. **Azure-beli számítási (IaaS)** – további számítási erőforrásokat kell kiépíteni, hogy az erőforrások egy másik régióban is elérhetők legyenek a katasztrófák során. További információ: az [Azure rugalmasságával kapcsolatos technikai útmutató](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** – ha felügyelt lemezeket használ, ismerkedjen meg a [régiók közötti biztonsági mentésekkel](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) Azure Backupával, és [replikálja a virtuális gépeket](./site-recovery/azure-to-azure-tutorial-enable-replication.md) az egyik régióból a másikba Azure site Recovery használatával. Ha Storage-fiókokat használ, a Geo-redundáns tárolás (GRS) alapértelmezés szerint be van állítva egy Azure Storage-fiók létrehozásakor. A GRS az adatai automatikusan replikálódnak az elsődleges régión belül, és háromszor a párosított régióban. További információ: [Azure Storage redundancia-beállítások](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – a Azure SQL Database geo-replikációval a tranzakciók aszinkron replikációját konfigurálhatja a világ bármely régiójába. javasoljuk azonban, hogy ezeket az erőforrásokat egy párosított régióban telepítse a legtöbb vész-helyreállítási forgatókönyv esetében. További információ: [geo-replikáció a Azure SQL Databaseban](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager** – A Resource Manager eredendően biztosítja az összetevők régiók közötti logikai elkülönítését. Ez azt jelenti, hogy az egyik régióban a logikai hibák valószínűleg kevésbé befolyásolják a másikat.

## <a name="benefits-of-paired-regions"></a>A párosított régiók előnyei

5. **Fizikai elkülönítés** – ha lehetséges, az Azure legalább 300 mérföld távolságot részesíti előnyben a regionális párokban található adatközpontok között, bár ez nem praktikus vagy lehetséges az összes földrajzi helyen. A fizikai adatközpontok elkülönítése csökkenti a természeti katasztrófák, a zavargások, az áramkimaradások vagy a fizikai hálózatok kimaradásának valószínűségét egyszerre mindkét régióban. Az elkülönítést a földrajz (földrajzi méret, energia/hálózati infrastruktúra rendelkezésre állása, rendeletek stb.) korlátai határozzák meg.  

6. **Platform által biztosított replikálás** – egyes szolgáltatások, például a Geo-Redundant tárolók biztosítják a párosított régióba való automatikus replikálást.

7. **Régió helyreállítási sorrendje** – széles kimaradás esetén az egyik régió helyreállítása minden pár közül rangsorolva van. A párosított régiókban üzembe helyezett alkalmazások esetében az egyik régió garantáltan előnyben részesül a helyreállításkor. Ha egy alkalmazás központi telepítése nem párosított régiók között történik, előfordulhat, hogy a helyreállítás késleltethető – a legrosszabb esetben a kiválasztott régiók az utolsó két helyre állíthatók vissza.

8. **Szekvenciális frissítések** – a tervezett Azure-rendszerfrissítéseket a rendszer a párosított régiókba (nem pedig egyidőben), az állásidő minimalizálására, a hibák hatására és a logikai hibákra a rossz frissítés ritka eseménye esetén.

9. **Adattárolás** – a régió ugyanabban a földrajzi helyen található, mint a párja (a Dél-Brazília kivételével), hogy megfeleljen az adó-és bűnüldözési joghatósági céloknak az adattárolásra vonatkozó követelményeinek.