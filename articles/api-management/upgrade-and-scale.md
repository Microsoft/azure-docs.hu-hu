---
title: Azure API Management-példány frissítése és méretezése | Microsoft Docs
description: Ez a témakör az Azure API Management-példányok frissítését és méretezését ismerteti.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127787"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management-példány frissítése és méretezése  

Az Azure API Management-példányok az egységek hozzáadásával és eltávolításával méretezhetők. Az **egységek** dedikált Azure-erőforrásokból állnak, és egy bizonyos terhelési kapacitással rendelkeznek, amely havonta több API-hívással van kifejezve. Ez a szám nem jelent hívási korlátot, hanem a maximális átviteli sebességet, amely lehetővé teszi a durva kapacitás megtervezését. A tényleges átviteli sebesség és a késés a különböző tényezőktől függ, például az egyidejű kapcsolatok száma és sebessége, a konfigurált szabályzatok típusa és száma, a kérelmek és a válaszok mérete, valamint a háttérbeli késés.

Az egyes egységek kapacitása és ára attól függ, hogy **az egység** milyen szinten található. Négy szint közül választhat: **fejlesztői**, **alapszintű**, **standard**, **prémium**. Ha a kapacitást egy adott szinten szeretné bővíteni, vegyen fel egy egységet. Ha a API Management-példányban jelenleg kiválasztott réteg nem teszi lehetővé több egység hozzáadását, akkor magasabb szintű szintre kell frissítenie.

Az egyes egységek és a rendelkezésre álló funkciók (például a többrégiós telepítés) díja az API Management-példányhoz választott szinttől függ. A [díjszabás részleteit ismertető](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk az egyes csomagokban kapott egységenkénti és-funkciók árát ismerteti. 

>[!NOTE]
>A [díjszabási részletekről](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) szóló cikk az egység kapacitásainak hozzávetőleges számát mutatja az egyes csomagokban. A pontosabb számok megszerzéséhez az API-k reális forgatókönyvét kell megvizsgálnia. Tekintse meg az [Azure API Management-példány kapacitását](api-management-capacity.md) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetés

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ API Management példánnyal rendelkezik. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

+ Ismerje meg az [Azure API Management-példány kapacitásának](api-management-capacity.md)koncepcióját.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Frissítés és skálázás  

Négy szint közül választhat: **fejlesztői**, **alapszintű**,  **standard** és **prémium**. A **fejlesztői** szintet a szolgáltatás kiértékeléséhez kell használni; nem használható éles környezetben. A **fejlesztői** szint nem rendelkezik SLA-val, és nem méretezheti ezt a szintet (egység hozzáadása/eltávolítása). 

Az **alapszintű**, a **standard** és a **prémium** a SLA-val rendelkező, és méretezhető. Az alapszintű **csomag** egy SLA-val rendelkező legolcsóbb szint, amely akár két egységig is méretezhető, a **standard** szint pedig akár négy egységig is méretezhető. A **prémium** szinthez tetszőleges számú egységet adhat hozzá.

A **prémium** szint lehetővé teszi egyetlen Azure API Management-példány terjesztését tetszőleges számú, a kívánt Azure-régióban. Amikor először hoz létre egy Azure API Management-szolgáltatást, a példány csak egy egységet tartalmaz, és egyetlen Azure-régióban helyezkedik el. A kezdeti régió **elsődleges** régióként van kijelölve. A további régiók egyszerűen hozzáadhatók. Régió hozzáadásakor meg kell adnia, hogy hány egységet szeretne lefoglalni. Rendelkezhet például egy egységgel az **elsődleges** régióban és öt egységben egy másik régióban. Az egységek számát az egyes régiókban található forgalomhoz igazíthatja. További információ: [azure API Management Service-példány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md).

Bármely szintre frissítheti és visszaléphet. A verziófrissítés vagy a visszalépési művelettel eltávolíthat bizonyos funkciókat – például a virtuális hálózatok vagy a többrégiós telepítést, ha a standard vagy az alapszintű, prémium szintű csomagra vált.

> [!NOTE]
> A frissítés vagy a skálázási folyamat 15 – 45 percet is igénybe vehet. Értesítést kap, ha elkészült.

> [!NOTE]
> API Management **szolgáltatás a használati** szinten automatikusan méretezi a forgalmat a forgalom alapján.

## <a name="scale-your-api-management-service"></a>A API Management szolgáltatás méretezése

![API Management szolgáltatás méretezése Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. A [Azure Portalban](https://portal.azure.com/)navigáljon a API Management szolgáltatáshoz.
2. Válassza ki a kívánt **helyeket** a menüből.
3. Kattintson a méretezni kívánt helyet tartalmazó sorra.
4. Adja meg az **egységek** új számát – vagy használja a csúszkát, vagy írja be a számot.
5. Kattintson az **Alkalmaz** gombra.

## <a name="change-your-api-management-service-tier"></a>A API Management szolgáltatási szintjeinek módosítása

1. A [Azure Portalban](https://portal.azure.com/)navigáljon a API Management szolgáltatáshoz.
2. Kattintson a menü **díjszabási szintjére** .
3. Válassza ki a kívánt szolgáltatási szintet a legördülő listából. A csúszka segítségével megadhatja a API Management szolgáltatás méretezését a módosítás után.
4. Kattintson a **Mentés** gombra.

## <a name="downtime-during-scaling-up-and-down"></a>Leállás a felfelé és lefelé skálázás során
Ha a fejlesztői szinten vagy a-ból végez méretezést, a rendszer leállást eredményez. Ellenkező esetben nincs leállás. 

## <a name="compute-isolation"></a>Számítási elkülönítés
Ha a biztonsági követelmények között szerepel a [számítási elkülönítés](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), az **elkülönített** díjszabási szintet használhatja. Ez a szint biztosítja, hogy egy API Management szolgáltatási példány számítási erőforrásai a teljes fizikai gazdagépet használják, és megadják a szükséges elkülönítési szintet, például az Egyesült Államok védelmi Minisztériuma 5 (IL5) számítási feladatait. Ha az elkülönített szintet szeretné elérni, [hozzon létre egy támogatási jegyet](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Következő lépések

- [Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
- [Azure API Management Service-példány automatikus méretezése](api-management-howto-autoscale.md)
- [A API Management költségeinek megtervezése és kezelése](plan-manage-costs.md)
- [API Management korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)