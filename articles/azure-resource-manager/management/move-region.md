---
title: Azure-erőforrások áthelyezése másik régióba
description: Áttekintést nyújt az Azure-erőforrások Azure-régiók közötti áthelyezéséről.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007638"
---
# <a name="moving-azure-resources-across-regions"></a>Azure-erőforrások áthelyezése régiók között

Ez a cikk az Azure-erőforrások Azure-régiók közötti áthelyezésével kapcsolatos információkat tartalmaz.

Az Azure földrajzi régiói, régiói és rendelkezésre állási zónák alkotják az Azure globális infrastruktúrájának alapjait. Az Azure [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) terület általában két vagy több [Azure-régiót](https://azure.microsoft.com/global-infrastructure/regions/)tartalmaz. A régió a földrajzon belüli terület, amely Availability Zoneseket és több adatközpontot tartalmaz. 

Az erőforrások adott Azure-régióban való üzembe helyezése után számos oka lehet, hogy az erőforrásokat egy másik régióba kívánja áthelyezni.

- **Igazítás régióhoz – indítás**: helyezze át az erőforrásokat egy újonnan bevezetett Azure-régióba, amely korábban nem volt elérhető.
- **Szolgáltatások/szolgáltatások igazítása**: az erőforrások áthelyezésével kihasználhatja az adott régióban elérhető szolgáltatások és szolgáltatások előnyeit.
- **Válaszadás az üzleti fejleményekre**: erőforrások áthelyezése egy régióba az üzleti változások, például az összeolvadások vagy a beszerzések megválaszolására válaszul.
- **Közelség**: erőforrások áthelyezése a vállalat helyi régiójába.
- Az **adattárolási követelmények teljesítése**: helyezze át az erőforrásokat az adattárolási követelményekhez vagy az adatbesorolási igényekhez való igazodás érdekében. [További információ](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Válaszadás a központi telepítési követelményekre**: a hiba miatt üzembe helyezett erőforrások áthelyezése vagy a kapacitási igényekre való áttérés. 
- **Válasz a leszerelésre**: erőforrások áthelyezése a régiók leszerelése miatt.

## <a name="move-resources-with-resource-mover"></a>Erőforrások áthelyezése az erőforrás-mozgatóval

Az erőforrásokat áthelyezheti egy másik régióba az [Azure-erőforrás-mozgató](../../resource-mover/overview.md)használatával. Az erőforrás-mozgató a következőket biztosítja:

- Egyetlen központ az erőforrások régiók közötti áthelyezéséhez.
- Csökkentheti a mozgatási időt és a bonyolultságot. Minden, amire szüksége van, egyetlen helyen kell lennie.
- Egyszerű és egységes felület a különböző típusú Azure-erőforrások áthelyezéséhez.
- Egyszerű módszer az áthelyezni kívánt erőforrások függőségeinek azonosítására. Ezzel a művelettel a kapcsolódó erőforrásokat együtt helyezheti át, így minden a várt módon működik a megcélzott régióban, az áthelyezést követően.
- A forrás régió erőforrásainak automatikus törlése, ha az áthelyezés után törölni szeretné őket.
- Tesztelés: Kipróbálhatja a mozgatást, majd elvetheti, ha nem szeretne teljes áthelyezést végezni.

Az erőforrásokat egy másik régióba helyezheti át, néhány különböző módszer használatával:

- **Erőforrások áthelyezésének megkezdése egy** erőforráscsoporthoz: ezzel a módszerrel kiindulhat a régióból egy erőforráscsoporthoz. Az áthelyezni kívánt erőforrások kiválasztása után a folyamat folytatódik az erőforrás-mozgató központban, az erőforrás-függőségek vizsgálatához és az áthelyezési folyamat előkészítéséhez. [További információ](../../resource-mover/move-region-within-resource-group.md).
- **Erőforrások áthelyezésének megkezdése közvetlenül az erőforrás-mozgató hubhoz**: ezzel a módszerrel közvetlenül a központban helyezheti el a régió áthelyezése folyamatot. [További információ](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Régió áthelyezésének támogatása

Jelenleg az erőforrás-mozgató használatával helyezheti át ezeket az erőforrásokat egy másik régióba:

- Azure-beli virtuális gépek és kapcsolódó lemezek
- Hálózati adapterek (NIC-k)
- Rendelkezésre állási csoportok
- Azure-beli virtuális hálózatok
- Nyilvános IP-címek
- Hálózati biztonsági csoportok (NSG-k)
- Belső és nyilvános terheléselosztó
- Azure SQL Database-adatbázisok és rugalmas készletek

## <a name="region-move-process"></a>Régió mozgatása folyamat

Az erőforrások régiók közötti áthelyezésének tényleges folyamata az áthelyezett erőforrásoktól függ. Vannak azonban gyakori lépések:

1. **Előfeltételek ellenőrzése**: az előfeltételek közé tartozik annak biztosítása, hogy a szükséges erőforrások elérhetők legyenek a céltartományban, ellenőrizze, hogy van-e elegendő kvóta, és ellenőrizze, hogy az előfizetése hozzáférhet-e a célcsoporthoz.
2. **Függőségek elemzése**: az erőforrások függőségei lehetnek más erőforrásokon. Az áthelyezés előtt állapítsa meg a függőségeket, hogy az áthelyezett erőforrások továbbra is a várt módon működjenek az áthelyezés után.
3. **Felkészülés az áthelyezésre**: ezeket a lépéseket az áthelyezés előtt az elsődleges régióban hajthatja végre. Előfordulhat például, hogy exportálnia kell egy Azure Resource Manager sablont, vagy el kell kezdenie az erőforrások replikálását a forrásról a célhelyre.
4. **Helyezze át az erőforrásokat: az** erőforrások áthelyezésének módja attól függ, hogy mi a teendő. Előfordulhat, hogy telepítenie kell egy sablont a célként megadott régióban, vagy feladatátvételt kell végeznie a célhelyre.
5. **Megcélzott erőforrások elvetése**: az erőforrások áthelyezése után érdemes megtekinteni az erőforrásokat a célként megadott régióban, és el kell döntenie, hogy van-e olyan teendője, amelyre nincs szüksége.
6. **Az áthelyezés véglegesítése**: miután megtörtént az erőforrások ellenőrzése a céltartományban, egyes erőforrásokhoz végső véglegesítési műveletre lehet szükség. Előfordulhat például, hogy egy olyan célcsoportban, amely most az elsődleges régió, lehet, hogy a vész-helyreállítást egy új másodlagos régióra kell beállítania. 
7. **A forrás tisztítása**: végül, miután minden üzembe lett hozva az új régióban, törölheti és leszerelheti az elsődleges régiójában lévő áthelyezéshez és erőforrásokhoz létrehozott erőforrásokat.



## <a name="next-steps"></a>Következő lépések

[További](../../resource-mover/about-move-process.md) információ az áthelyezési folyamatról az erőforrás-mozgatóban.
