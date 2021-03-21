---
title: A hálózati erőforrások védelme Azure Security Center
description: Ez a dokumentum olyan Azure Security Center javaslatokat tartalmaz, amelyek segítenek az Azure-hálózati erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: c24e3473c07759aa256a077d11edb20c616629f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439510"
---
# <a name="protect-your-network-resources"></a>A hálózati erőforrások megóvása
Azure Security Center folyamatosan elemzi az Azure-erőforrások biztonsági állapotát a hálózati biztonsággal kapcsolatos ajánlott eljárásokhoz. Ha Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

A hálózatkezeléssel kapcsolatos javaslatok teljes listáját lásd: [hálózatkezelési javaslatok](recommendations-reference.md#recs-networking).

Ez a cikk az Azure-erőforrásokra vonatkozó ajánlásokat tárgyalja a hálózati biztonsági szempontból. Hálózatkezelési javaslatok központ a következő generációs tűzfalak, hálózati biztonsági csoportok, JIT VM-hozzáférés, túlzottan megengedhető bejövő forgalmi szabályok és egyéb funkciók köré. A hálózati javaslatok és a javítási műveletek listáját lásd: [biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md).

A Security Center **hálózatkezelési** funkciói a következők: 

- Hálózati Térkép (Azure Defender szükséges hozzá)
- [Adaptív hálózat megerősítése](security-center-adaptive-network-hardening.md) (Azure Defender szükséges hozzá)
- Hálózatkezeléssel kapcsolatos biztonsági javaslatok
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>A hálózati erőforrások és a javaslataik megtekintése

Az [eszközök leltározása lapon](asset-inventory.md)az erőforrástípus szűrő használatával válassza ki a vizsgálni kívánt hálózati erőforrásokat:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Tárgyieszköz-leltár hálózati erőforrás-típusai" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Hálózati Térkép

Az interaktív hálózati Térkép grafikus nézetet biztosít a biztonsági átfedésekkel, így javaslatokat és bepillantást nyerhet a hálózati erőforrások megerősítéséhez. A Térkép használatával megtekintheti az Azure-beli számítási feladatok hálózati topológiáját, a virtuális gépek és az alhálózatok közötti kapcsolatokat, valamint a térképről adott erőforrásokra és az ezekre az erőforrásokra vonatkozó javaslatokkal való részletezés lehetőségét.

A hálózati Térkép megnyitása:

1. A Security Center menüjében nyissa meg az Azure Defender irányítópultját, és válassza a **hálózati Térkép** lehetőséget.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Hálózati Térkép megnyitása az Azure Defender irányítópultján" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Válassza a **rétegek** menüt, majd a **topológia** lehetőséget.
 
A topológiai Térkép alapértelmezett nézete a következőket jeleníti meg:

- Az Azure-ban kiválasztott előfizetések. A Térkép több előfizetést is támogat.
- A Resource Manager erőforrástípus virtuális gépek, alhálózatok és virtuális hálózatok (a klasszikus Azure-erőforrások nem támogatottak)
- Társ virtuális hálózatok
- Csak a magas vagy közepes súlyosságú [hálózati javaslatokkal](security-center-recommendations.md) rendelkező erőforrások  
- Internet felé irányuló erőforrások
- A Térkép az Azure-ban kiválasztott előfizetésekhez van optimalizálva. Ha módosítja a kijelölést, a rendszer az új beállítások alapján újraszámítja és újraoptimalizálja a térképet.  

[![Hálózati topológia térképe](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>A hálózati Térkép ismertetése

A hálózati Térkép az Azure-erőforrásokat **topológiai** nézetben és **forgalmi** nézetben jelenítheti meg. 

### <a name="the-topology-view"></a>A topológia nézet

A hálózati Térkép **topológia** nézetében a következő információkat tekintheti meg a hálózati erőforrásokkal kapcsolatban:

- A belső körben a kiválasztott előfizetéseken belüli összes virtuális hálózatok megtekintheti, a következő kör pedig az összes alhálózatot, a külső kör pedig az összes virtuális gép.
- A Térkép erőforrásaihoz csatlakozó sorokban megtudhatja, hogy mely erőforrások kapcsolódnak egymáshoz, és hogy az Azure-hálózat hogyan épül fel. 
- A súlyossági mutatók segítségével gyorsan áttekintheti, hogy mely erőforrások rendelkeznek a Security Center által megnyitott javaslatokkal.
- Rákattinthat bármelyik erőforrásra a részletezéshez, és megtekintheti az adott erőforrás részleteit és javaslatait közvetlenül, valamint a hálózati Térkép kontextusában.  
- Ha túl sok erőforrás jelenik meg a térképen, Azure Security Center a saját saját algoritmusát használja az erőforrások intelligens fürtözéséhez, kiemelve a legkritikusabb állapotú erőforrásokat, és a legtöbb súlyossági szintű javaslatot. 

Mivel a Térkép interaktív és dinamikus, minden csomópont kattintható, a nézet pedig a szűrők alapján változhat:

1. A hálózat térképen a felül látható szűrők használatával módosíthatja a megjelenő tudnivalókat. A térképen a következők alapján lehet összpontosítani:

   -  **Biztonsági állapot**: az Azure-erőforrások súlyossága (magas, közepes, alacsony) alapján szűrheti a térképet.
   - **Javaslatok**: kiválaszthatja, hogy mely erőforrások jelenjenek meg az adott erőforrásokon aktív javaslatok alapján. Megtekintheti például azokat az erőforrásokat, amelyekhez Security Center azt javasolja, hogy engedélyezze a hálózati biztonsági csoportokat.
   - **Hálózati zónák**: alapértelmezés szerint a Térkép csak az internetre irányuló erőforrásokat jeleníti meg, a belső virtuális gépeket is kiválaszthatja.
 
2. A bal felső sarokban lévő **visszaállítás** gombra kattintva bármikor visszatérhet a térképhez az alapértelmezett állapotba.

Erőforrások részletezése:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat nyújt az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, valamint az erőforrásra vonatkozó ajánlásokat. Ez ugyanaz a viselkedés a kiválasztott erőforrásokhoz tartozó típusoknál. 
2. Ha a Térkép egyik csomópontja fölé viszi a mutatót, megtekintheti az erőforrással kapcsolatos általános információkat, beleértve az előfizetést, az erőforrás típusát és az erőforráscsoportot is.
3. A hivatkozással nagyíthatja az elemleírást, és áthelyezheti a leképezést az adott csomóponton. 
4. Ha a térképet egy adott csomópontról szeretné áthelyezni, nagyítsa ki.

### <a name="the-traffic-view"></a>A forgalom nézet

A **forgalmi** nézet az erőforrások közötti lehetséges adatforgalomról nyújt térképet. Ez egy vizualizációs térképet biztosít az összes konfigurált szabályhoz, amely meghatározza, hogy mely erőforrások tudnak kommunikálni. Ez lehetővé teszi a hálózati biztonsági csoportok meglévő konfigurációjának megtekintését, valamint a számítási feladatokban lévő lehetséges kockázatos konfigurációk gyors azonosítását.

### <a name="uncover-unwanted-connections"></a>Nemkívánatos kapcsolatok felfedése

Ennek a nézetnek az erőssége, hogy megmutassa ezeket az engedélyezett kapcsolatokat a meglévő sebezhetőségekkel együtt, így az adatok több szakasza segítségével elvégezheti az erőforrások szükséges megerősítését. 

Előfordulhat például, hogy észlelhető két olyan gép, amelyről nem tudott kommunikálni, így könnyebben elkülönítheti a munkaterheléseket és az alhálózatokat.

### <a name="investigate-resources"></a>Erőforrások vizsgálata

Erőforrások részletezése:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat nyújt az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, valamint az erőforrásra vonatkozó ajánlásokat. Ez ugyanaz a viselkedés a kiválasztott erőforrásokhoz tartozó típusoknál. 
2. A **forgalom** lehetőségre kattintva megtekintheti a lehetséges kimenő és bejövő adatforgalom listáját az erőforráson – ez egy átfogó lista, amely képes kommunikálni az erőforrással, és hogy ki tud kommunikálni a szolgáltatással, és mely protokollokat és portokat használhatja. Ha például kiválaszt egy virtuális gépet, az összes kommunikálni képes virtuális gép megjelenik, és amikor kiválaszt egy alhálózatot, megjelenik az összes olyan alhálózat, amelyről kommunikálni tud.

**Ezek az információk a hálózati biztonsági csoportok és a speciális gépi tanulási algoritmusok elemzésén alapulnak, amelyek több szabályt elemeznek a crossover-és interakciók megismerése érdekében.** 

[![Hálózati forgalom térképe](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következőket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](./asset-inventory.md)