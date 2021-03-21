---
title: A biztonsági szabályzatok, a kezdeményezések és a javaslatok megismerése Azure Security Center
description: A biztonsági szabályzatok, kezdeményezések és javaslatok megismerése Azure Security Centerban.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176433"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Mik azok a biztonsági szabályzatok, kezdeményezések és javaslatok?

Security Center az előfizetések biztonsági kezdeményezéseit alkalmazza. Ezek a kezdeményezések egy vagy több biztonsági házirendet tartalmaznak. Ezen szabályzatok mindegyike biztonsági javaslatot eredményez a biztonsági helyzet javítására. Ez az oldal részletesen ismerteti ezeket az ötleteket.


## <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzat?

Az Azure Policy-ben létrehozott Azure Policy-definíció egy szabály az ellenőrzött biztonsági feltételekről. A beépített definíciók olyan dolgok, mint például annak szabályozása, hogy milyen típusú erőforrásokat lehet telepíteni, vagy érvényesíteni a címkék használatát az összes erőforráson. Létrehozhat saját egyéni házirend-definíciókat is.

A szabályzat-definíciók (beépített vagy egyéni) megvalósításához hozzá kell rendelnie őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

A Azure Policy különböző típusú házirendekkel rendelkezik. Security Center főleg olyan "naplózási" házirendeket használ, amelyek bizonyos feltételeket és konfigurációkat ellenőriznek, majd jelentést tesznek a megfelelőségről. Léteznek olyan "kényszerítő" szabályzatok is, amelyek a biztonságos beállítások alkalmazására használhatók.

## <a name="what-is-a-security-initiative"></a>Mi az a biztonsági kezdeményezés?

Az Azure-kezdeményezés olyan Azure-házirend-definíciók vagy-szabályok gyűjteménye, amelyek egy adott célhoz vagy célhoz vannak csoportosítva. Az Azure-kezdeményezések leegyszerűsítik a házirendek felügyeletét azáltal, hogy a szabályzatok egy készletét együtt, logikusan, egyetlen elemként csoportosítják.

A biztonsági kezdeményezés meghatározza a számítási feladatok kívánt konfigurációját, és segít biztosítani, hogy megfeleljen a vállalat vagy a szabályozók biztonsági követelményeinek.

A biztonsági szabályzatokhoz hasonlóan Security Center kezdeményezések is létrejönnek a Azure Policy. A [Azure Policy](../governance/policy/overview.md) a házirendek kezeléséhez, a kezdeményezések létrehozásához, valamint a több előfizetéshez vagy a teljes felügyeleti csoportokhoz tartozó kezdeményezések hozzárendeléséhez használható.

Az alapértelmezett kezdeményezés automatikusan hozzá van rendelve a Azure Security Center minden előfizetéséhez az Azure biztonsági Teljesítményteszte. Ez a teljesítményteszt a Microsoft által létrehozott, az Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít. További tudnivalók az [Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

Security Center a következő lehetőségeket kínálja a biztonsági kezdeményezésekkel és házirendekkel való munkavégzéshez:

- **A beépített alapértelmezett kezdeményezés megtekintése és szerkesztése** – a Security Center engedélyezésekor az "Azure biztonsági teljesítményteszt" nevű kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez. A kezdeményezés testreszabásához a szabályzat paramétereinek szerkesztésével engedélyezheti vagy letilthatja az egyéni házirendeket. Tekintse meg a [beépített biztonsági szabályzatok](./policy-reference.md) listáját, amelyből megismerheti a rendelkezésre álló lehetőségeket.

- **Saját egyéni kezdeményezések hozzáadása** – Ha testre szeretné szabni az előfizetésre alkalmazott biztonsági kezdeményezéseket, akkor a Security Centeron belül megteheti. Ezután javaslatokat fog kapni, ha a gépek nem követik a létrehozott házirendeket. Az egyéni szabályzatok létrehozásával és hozzárendelésével kapcsolatos utasításokért lásd: [egyéni biztonsági kezdeményezések és házirendek használata](custom-security-policies.md).

- **Szabályozási megfelelőségi szabványok hozzáadása kezdeményezésként** – Security Center szabályozási megfelelőségi irányítópultja egy adott szabvány vagy szabályozás (például Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020) kontextusában jeleníti meg a környezet összes értékelésének állapotát. További információ: [a szabályozás megfelelőségének javítása](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslat?

A szabályzatok használatával Security Center rendszeresen elemzi az erőforrások megfelelőségi állapotát, hogy azonosítsa a lehetséges biztonsági hibás konfigurációkat és gyengeségeket. Ezután javaslatokat ad a problémák megoldására. A javaslatok az erőforrások a vonatkozó házirendekkel való értékelésének, valamint a meghatározott követelményeknek nem megfelelő erőforrások azonosításának eredményei.

A Security Center a választott kezdeményezéseken alapuló biztonsági javaslatokat tesz. Ha a kezdeményezés szabályzata össze van hasonlítva az erőforrásokkal, és egy vagy több nem megfelelőnek minősül, akkor a Security Center.

A javaslatok olyan műveletek, amelyekkel biztonságossá teheti és megerősítheti az erőforrásait. Minden javaslat a következő információkat tartalmazza:

- A probléma rövid leírása
- A javaslat megvalósításának végrehajtásához szükséges szervizelési lépések
- Az érintett erőforrások

A gyakorlatban a következőképpen működik:

1. Az Azure biztonsági teljesítményteszt egy ***kezdeményezés***
1. Olyan ***szabályzatot*** tartalmaz, amely megköveteli az összes Azure Storage-fiók számára a hálózati hozzáférés korlátozását, így csökkentheti a támadási felületet. Ezt a házirendet "Storage-fiókoknak kell korlátoznia a virtuális hálózati szabályok használatával", és letiltható vagy engedélyezhető Azure Policy
1. Ha Azure Security Center megtalál egy Azure Storage-fiókot a védett előfizetések bármelyikén, a rendszer kivizsgálja ezeket a fiókokat, hogy megtudja, vannak-e védve virtuális hálózati szabályokkal. Ha nem, a rendszer egy ***javaslatot*** jelenít meg a helyzet kijavításához és az erőforrások biztonságának megerősítéséhez. 

Ezért egy kezdeményezés (1) olyan szabályzatokat (2) tartalmaz, amelyek megfelelő (3) javaslatokat hoznak. 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Javaslat és szabályzat közötti kapcsolat megtekintése

Ahogy fent említettük, Security Center beépített javaslatai az Azure biztonsági Teljesítményteszten alapulnak. Szinte minden javaslat rendelkezik egy alapul szolgáló házirenddel, amely a teljesítményteszt követelményének megfelelően van származtatva.

A javaslatok részleteinek áttekintéséhez gyakran hasznos, ha látni szeretné a mögöttes szabályzatot. A szabályzat által támogatott minden javaslat esetében a javaslat részletei lapon található **házirend-definíció megtekintése** hivatkozásra kattintva lépjen közvetlenül a megfelelő szabályzat Azure Policy bejegyzésére:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Hivatkozás Azure Policy lapra a javaslatot támogató konkrét szabályzathoz":::

Ezzel a hivatkozással megtekintheti a szabályzat definícióját, és áttekintheti az értékelési logikát. 

Ha áttekinti a javaslatok listáját a [biztonsági javaslatok hivatkozási útmutatójában](recommendations-reference.md), akkor a szabályzat-definíciós lapokra mutató hivatkozásokat is láthatja:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Egy adott szabályzat Azure Policy oldalának elérése közvetlenül a Azure Security Center javaslatok hivatkozási oldaláról":::


## <a name="next-steps"></a>Következő lépések

Ezen az oldalon részletesen ismertetjük a házirendek, a kezdeményezések és a javaslatok közötti alapvető fogalmakat és kapcsolatokat. A kapcsolódó információk a következő témakörben találhatók:

- [Egyéni kezdeményezések létrehozása](custom-security-policies.md)
- [A javaslatok letiltására vonatkozó biztonsági szabályzatok letiltása](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Megtudhatja, hogyan szerkesztheti a biztonsági házirendeket Azure Policy](../governance/policy/tutorials/create-and-manage.md)