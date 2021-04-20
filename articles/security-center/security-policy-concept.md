---
title: A biztonsági szabályzatok, kezdeményezések és javaslatok Azure Security Center
description: Ismerje meg a biztonsági szabályzatokat, kezdeményezéseket és javaslatokat a Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738971"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Mik azok a biztonsági szabályzatok, kezdeményezések és javaslatok?

Security Center biztonsági kezdeményezéseket alkalmaz az előfizetéseire. Ezek a kezdeményezések egy vagy több biztonsági szabályzatot tartalmaznak. A szabályzatok mindegyikének eredménye egy biztonsági javaslat, amely javítja a biztonsági helyzetét. Ez az oldal részletesen ismerteti ezeket az ötleteket.


## <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzat?

A Azure Policy létrehozott Azure-szabályzatdefiníciók meghatározott biztonsági feltételekre vonatkozó szabályok. A beépített definíciók olyan lehetőségeket tartalmaznak, mint például az üzembe helyezhető erőforrások típusának szabályozása vagy a címkék használatának kényszerítése az összes erőforráson. Saját egyéni szabályzatdefiníciókat is létrehozhat.

A szabályzatdefiníciók megvalósításához (akár beépített, akár egyéni) hozzá kell rendelnie őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

A szabályzatok különböző Azure Policy. Security Center "Naplózás" szabályzatokat használ, amelyek ellenőrzik az adott feltételeket és konfigurációkat, majd jelentést készítnek a megfelelőségről. Emellett "Kikényszeríteni" szabályzatok is használhatók a biztonságos beállítások alkalmazáshoz.

## <a name="what-is-a-security-initiative"></a>Mi az a biztonsági kezdeményezés?

Az Azure-kezdeményezések azure-szabályzatdefiníciók vagy szabályok gyűjteményei, amelyek egy adott cél vagy cél elérése érdekében vannak csoportosítva. Az Azure-kezdeményezések leegyszerűsítik a szabályzatok kezelését azáltal, hogy logikailag egyetlen elemként csoportosítják a szabályzatokat.

A biztonsági kezdeményezés határozza meg a számítási feladatok kívánt konfigurációját, és segít biztosítani, hogy megfeleljen a vállalat vagy a szabályozások biztonsági követelményeinek.

A biztonsági szabályzatok Security Center a kezdeményezések is a Azure Policy. A szabályzatokat [Azure Policy](../governance/policy/overview.md) kezelheti, kezdeményezéseket építhet ki, és kezdeményezéseket rendelhet hozzá több előfizetéshez vagy teljes felügyeleti csoportokhoz.

Az alapértelmezett kezdeményezés automatikusan hozzá van rendelve a Azure Security Center összes előfizetéséhez az Azure biztonsági teljesítményteszt. Ez a teljesítményteszt a Microsoft által írt, Azure-specifikus irányelvek készlete a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. Ez a széles körben elterjedt teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít. További tudnivalók az [Azure-biztonsági teljesítménytesztről](https://docs.microsoft.com/security/benchmark/azure/introduction).

Security Center következő lehetőségeket kínálja a biztonsági kezdeményezésekkel és szabályzatokkal való munkavégzéshez:

- **A beépített alapértelmezett** kezdeményezés megtekintése és szerkesztése – Ha engedélyezi a Security Center, az "Azure Security Benchmark" nevű kezdeményezés automatikusan hozzá lesz rendelve az Security Center összes regisztrált előfizetéshez. A kezdeményezés testreszabásához engedélyezheti vagy letilthatja az egyes szabályzatokat a szabályzat paramétereinek szerkesztésével. A beépített biztonsági [szabályzatok listájából](./policy-reference.md) további információkhoz is láthatja a beépített lehetőségeket.

- **Saját egyéni kezdeményezések hozzáadása** – Ha testre szeretné szabni az előfizetéséhez alkalmazott biztonsági kezdeményezéseket, ezt a saját Security Center. Ezután javaslatokat kap, ha a gépek nem követik a létrehozott szabályzatokat. Útmutatás egyéni szabályzatok létrehozásához és hozzárendeléshez: [Egyéni biztonsági kezdeményezések és szabályzatok használata.](custom-security-policies.md)

-  Szabályozási megfelelőségi szabványok felvétele kezdeményezésekként – az Security Center szabályozási megfelelőségi irányítópultja egy adott szabvány vagy szabályozás (például az Azure CIS, az NIST SP 800-53 R4, a SWIFT CSP CSCF-v2020) kontextusában jeleníti meg a környezetben végzett értékelések állapotát. További információ: [A jogszabályi megfelelőség javítása.](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslat?

A szabályzatok Security Center rendszeresen elemzi az erőforrások megfelelőségi állapotát a lehetséges biztonsági konfigurációk és gyengeségek azonosítása érdekében. Ezután javaslatokat tesz a problémák megoldásához. A javaslatok az erőforrások megfelelő szabályzatokkal való értékelésének és a meghatározott követelményeknek nem megfelelő erőforrások azonosításának az eredménye.

Security Center a kiválasztott kezdeményezések alapján teszi a biztonsági javaslatait. Ha a kezdeményezés egy szabályzatát összeveti az erőforrásokkal, és talál egy vagy több nem megfelelő szabályzatot, az a szabályzatot a Security Center.

A javaslatok olyan műveletek, amelyek az erőforrások biztonságossá és biztonságossá te tehének. Minden javaslat a következő információkat biztosítja:

- A probléma rövid leírása
- A javaslat megvalósításához szükséges javítási lépések
- Az érintett erőforrások

A gyakorlatban ez a következő:

1. Az Azure biztonsági teljesítményteszt egy ***kezdeményezés***
1. Tartalmaz egy ***szabályzatot,*** amely megköveteli, hogy az összes Azure Storage-fiók korlátozza a hálózati hozzáférést a támadási felület csökkentése érdekében. Ennek a szabályzatnak a neve " A tárfiókok virtuális hálózati szabályokkal korlátozzák a hálózati hozzáférést", és letilthatók vagy engedélyezhetők a Azure Policy
1. Ha Azure Security Center valamelyik védett előfizetésben talál Azure Storage-fiókot, akkor kiértékeli ezeket a fiókokat, és megállapítja, hogy virtuális hálózati szabályokkal vannak-e védve. Ha nem, megjelenik egy javaslat, hogy javítsa ki ***ezt*** a helyzetet, és javítsa az erőforrások biztonságát. 

Így a kezdeményezés (1) olyan szabályzatokat (2) tartalmaz, amelyek szükség esetén javaslatokat hoznak létre (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Javaslat és szabályzat közötti kapcsolat megtekintése

Ahogy korábban említettük, Security Center beépített javaslatok az Azure biztonsági teljesítményteszten alapulnak. Szinte minden javaslatnak van egy mögöttes szabályzata, amely a teljesítményteszt egyik követelménye alapján van kivezetve.

Egy javaslat részleteinek áttekintésekor gyakran hasznos lehet a mögöttes szabályzat megtekintése. A szabályzat által támogatott összes  javaslathoz a Javaslat részletei lap Szabályzatdefiníciók megtekintése hivatkozásával közvetlenül a megfelelő szabályzat Azure Policy használhatja:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Hivatkozás Azure Policy javaslatot támogató adott szabályzat lapjára":::

Ezen a hivatkozáson megtekintheti a szabályzatdefiníciót, és áttekinti a kiértékelési logikát. 

Ha áttekinti a biztonsági javaslatok referencia-útmutatójában található javaslatok [listáját,](recommendations-reference.md)a szabályzatdefiníciós oldalakra mutató hivatkozásokat is látni fog:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Egy adott szabályzat Azure Policy oldalának elérése közvetlenül a Azure Security Center javaslatok referenciaoldalán":::


## <a name="next-steps"></a>Következő lépések

Ez az oldal magas szinten ismertette a szabályzatok, kezdeményezések és javaslatok alapvető fogalmait és kapcsolatait. A kapcsolódó információkért lásd:

- [Egyéni kezdeményezések létrehozása](custom-security-policies.md)
- [Biztonsági szabályzatok letiltása javaslatok letiltásához](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Megtudhatja, hogyan szerkeszthet biztonsági szabályzatokat a Azure Policy](../governance/policy/tutorials/create-and-manage.md)