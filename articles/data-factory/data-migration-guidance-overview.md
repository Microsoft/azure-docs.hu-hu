---
title: Adatok migrálása a adatközpontból és az adatraktárból az Azure-ba
description: Az adatok áttelepíthetők az Azure-ba a Azure Data Factory használatával.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367707"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha át szeretné telepíteni a (Microsoft Azure) vagy a vállalati adattárházat (EDW), vegye fontolóra Azure Data Factory használatát. A Azure Data Factory a következő esetekben alkalmas:

- Big adatmunkaterhelés-áttelepítés az Amazon Simple Storage szolgáltatásból (Amazon S3) vagy egy helyszíni Hadoop elosztott fájlrendszer (HDFS) az Azure-ba
- EDW-áttelepítés Oracle-Exadata, Netezza, Teradata vagy Amazon vöröseltolódásról az Azure-ba

A Azure Data Factory áthelyezheti a petabájt (PB) adatait a Lake Migration adatforgalmához, és több tízezer terabájt (TB) adat adattárház-áttelepítéshez.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Miért Azure Data Factory használható az adatáttelepítéshez

- A Azure Data Factory könnyedén méretezheti a feldolgozási teljesítmény mennyiségét, hogy kiszolgáló nélküli módon helyezze át az adatátvitelt a nagy teljesítmény, a rugalmasság és a méretezhetőség révén. És csak a ténylegesen használt funkciókért kell fizetnie. Vegye figyelembe a következőket is: 
  - Azure Data Factory nem rendelkezik az adatmennyiséggel vagy a fájlok számával kapcsolatos korlátozásokkal.
  - A Azure Data Factory teljes mértékben használhatja a hálózat és a tároló sávszélességét, hogy a lehető legnagyobb mennyiségű adatátviteli sebességet tudja elérni a környezetben.
  - A Azure Data Factory utólagos elszámolású módszert használ, így csak az Azure-ba történő adatáttelepítés során ténylegesen használt idő után kell fizetnie.  
- Azure Data Factory egyszerre egy egyszeri betöltést és ütemezett növekményes terhelést is végrehajthat.
- A Azure Data Factory az Azure Integration Runtime (IR) használatával helyezi át az adatátvitelt a nyilvánosan elérhető adattó és a raktári végpontok között. Emellett saját üzemeltetésű integrációs modult is használhat az Azure Virtual Network (VNet) vagy egy tűzfal mögött található adattó-és raktár-végpontok adatáthelyezéséhez.
- A Azure Data Factory nagyvállalati szintű biztonsággal rendelkezik: az Windows Installer (MSI) vagy a szolgáltatás identitását használhatja a biztonságos szolgáltatások közötti integrációhoz, vagy használhat Azure Key Vault a hitelesítő adatok kezeléséhez.
- A Azure Data Factory egy ingyenes, beépített monitorozási irányítópultot biztosít a kód nélküli szerzői műveletekhez.  

## <a name="online-vs-offline-data-migration"></a>Online és offline adatáttelepítés

A Azure Data Factory egy szabványos Online áttelepítési eszköz, amely hálózaton keresztül (Internet, er vagy VPN) továbbítja az adatok átvitelét. Míg az offline adatok áttelepítése esetén a felhasználók fizikailag szállítanak adatátviteli eszközöket a szervezetéről egy Azure-adatközpontba.  

Az online és az offline áttelepítési módszer közül három fő szempontot kell figyelembe venni:  

- Migrálni kívánt adatméret
- Hálózati sávszélesség
- Áttelepítési ablak

Tegyük fel például, hogy az adatáttelepítés két héten belül történő elvégzéséhez tervezi Azure Data Factory használatát (az *áttelepítési ablak*). Figyelje meg a rózsaszín/kék határvonalat a következő táblázatban. Az adott oszlop legalacsonyabb rózsaszín cellája azt az adatméretet/hálózati sávszélesség-párosítást jeleníti meg, amelynek áttelepítési ablaka a legközelebb van, de kevesebb, mint két hét. (A kék cellában lévő bármilyen méretű vagy sávszélességű párosítás Online áttelepítési időszaka több mint két hétig tart.) 

![online vagy offline ](media/data-migration-guidance-overview/online-offline.png) – Ez a táblázat segít eldönteni, hogy a tervezett áttelepítési időszakot az adatforgalom és a rendelkezésre álló hálózati sávszélesség alapján tudja-e teljesíteni az online áttelepítéssel (Azure Data Factory). Ha az online áttelepítési ablak kettőnél több hetet használ, offline áttelepítést érdemes használni.

> [!NOTE]
> Az online áttelepítés használatával a korábbi és a növekményes adatbevitelt is elérheti egyetlen eszközön keresztül.  Ezzel a megközelítéssel az adatokat szinkronizálni lehet a meglévő tároló és az új áruház között a teljes áttelepítési időszak alatt. Ez azt jelenti, hogy újraépítheti az ETL-logikát az új tárolóban a frissített adatértékekkel.


## <a name="next-steps"></a>Következő lépések

- [Adatok migrálása az AWS S3-ból az Azure-ba](data-migration-guidance-s3-azure-storage.md)
- [Adatok migrálása helyszíni Hadoop-fürtről az Azure-ba](data-migration-guidance-hdfs-azure-storage.md)
- [Adatok migrálása helyszíni Netezza-kiszolgálóról az Azure-ba](data-migration-guidance-netezza-azure-sqldw.md)
