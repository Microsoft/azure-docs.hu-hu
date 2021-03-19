---
title: Az Azure Data Share terminológiája
description: Ismerje meg az Azure-adatmegosztásban használt erőforrások (adatszolgáltató, adatfogyasztó, adatmegosztás, megosztási előfizetés, pillanatkép, meghívás, címzett) leírására használt általános kifejezéseket.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73468554"
---
# <a name="azure-data-share-concepts"></a>Az Azure-adatmegosztás fogalmai 

Az Azure-beli adatmegosztás néhány új, az adatmegosztással kapcsolatos terminológiát mutat be. Ez a cikk néhány olyan gyakran használt kifejezést ismertet, amelyet láthat a szolgáltatás során. 

## <a name="data-provider"></a>Adatszolgáltató

Az adatszolgáltató az a szervezet, amelyik az adatmegosztást a fogyasztókkal. Általában az adatszolgáltató lehet a tulajdonos vagy az adatkezelő. Az adatszolgáltatók különböző típusú adattípusokat oszthatnak meg. Néhány példa arra, hogy egy adatszolgáltató szeretne-e megosztani nyers adatmennyiséget, például az értékesítési pontot vagy az idősorozat-adatmennyiséget. Előfordulhat, hogy egy adatszolgáltató meg szeretné osztani az előre feldolgozható, az elemzéseket és az elemzéseket is tartalmazó, korábban feldolgozatlan, megtekintett adatgyűjtési 

## <a name="data-consumer"></a>Adatfogyasztó 

Az adatfogyasztó az a szervezet, amely adatszolgáltatótól fogadja az adatforrást. Előfordulhat, hogy az adatfogyasztó a saját adattal szeretné csatlakoztatni a megosztott adatforrásokat az elemzések kinyeréséhez. Bizonyos esetekben előfordulhat, hogy az adatfogyasztó olyan, már feldolgozott adatkérést kap, amely már fel van dolgozva. 

## <a name="data-share"></a>Data Share

Adatmegosztásoknak nevezzük az egy entitásként megosztott adatkészletekből álló csoportokat. Több olyan Azure-adatforrásból is származhatnak az adatkészletek, amelyek használhatók az Azure Data Share szolgáltatással. Egyelőre csak az Azure Blob Storage és az Azure Data Lake Store lehet ilyen adatforrás az Azure Data Share használatakor. 

## <a name="share-subscription"></a>Előfizetés megosztása 

A megosztási előfizetések akkor jönnek létre, amikor az adatfogyasztó elfogad egy adatmegosztási meghívást egy adatszolgáltatótól. Az adatszolgáltatók megtekinthetik az aktív megosztás-előfizetéseket **, ha az** Azure-beli adatmegosztási fiókjában navigálnak az **Elküldett megosztásokhoz**

Egy adatfogyasztó megtekintheti, hogy rendelkezik-e aktív megosztási előfizetéssel, ha a **kapott megosztásokra** navigál, és megtekinti a kapott megosztások állapotát. 

## <a name="snapshot"></a>Pillanatkép

Adatmegosztási meghívást elfogadó adatfogyasztó létrehozhat pillanatképet. Ha elfogadják a meghívót, elindíthatják a velük megosztott adatok teljes pillanatképét. A pillanatkép az adatok másolata azon a ponton, amikor az adatfogyasztó létrehozta a pillanatképet. 

A pillanatképek két típusa létezik – teljes és növekményes. A teljes pillanatkép az adatmegosztáson belüli összes adatát tartalmazza. A növekményes Pillanatképek a legutóbbi pillanatkép elindítása óta frissített/hozzáadott összes adatértéket tartalmazzák. 

## <a name="snapshot-settings-in-azure-data-share"></a>Pillanatkép-beállítások az Azure-beli adatmegosztásban
 
Egy adatszolgáltató engedélyezheti az adatmegosztás pillanatkép-beállítását. Ez a beállítás lehetővé teszi, hogy az adatfogyasztók megkapják a növekményes frissítéseket. Ezt a beállítást engedélyezni kell, ha az adatszolgáltató szeretné, hogy az adatkezelők a megosztott adatkezelési szolgáltatásban is megkapják a frissítéseket. 

Ha egy adatszolgáltató engedélyezi ezt a beállítást, egy ismétlődési időköz is kiválasztható. Az ismétlődési időköz lehet óránként vagy naponta. 

Egy adatfogyasztónak lehetősége van arra, hogy a pillanatkép-ütemterven belül fogadja a növekményes frissítéseket, beleértve azokat az adattípusokat is, amelyek megváltoztak, mert először létrehoztak egy új pillanatképet. 

## <a name="invitation"></a>Meghívás

Egy adatszolgáltató több címzettet is meghívhat az adatmegosztásra. Ezt úgy teheti meg, hogy a címzetteket hozzáadja az adatmegosztáshoz. A meghívókat egy adatmegosztás létrehozása után is fel lehet venni. 

Egy adatszolgáltató törölhet egy meghívót, miután elküldték, ha még nem fogadták el. Ha az adatszolgáltató töröl egy meghívót, és még nem fogadta el, az adatfogyasztó nem fogja tudni elfogadni. 

A meghívások naponta akár ötször is elküldhetők. 

## <a name="recipient"></a>Címzett

A címzett olyan személy, aki meghívót kap egy adatmegosztásra. Egy adatszolgáltató jellemzően a létrehozott adatmegosztáshoz adja hozzá a címzetteket. Ha egy meghívás címzettjei elfogadják a meghívót, az adatfogyasztónak lesznek.  

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
