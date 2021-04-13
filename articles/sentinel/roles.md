---
title: Engedélyek az Azure Sentinelben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy az Azure Sentinel hogyan használja az Azure-beli szerepköralapú hozzáférés-vezérlést a felhasználók engedélyeinek kiosztásához és az egyes szerepkörökhöz tartozó engedélyezett műveletek azonosításához.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.openlocfilehash: b64adbb63efaa4ce4781474f732bc9509d51029e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310325"
---
# <a name="permissions-in-azure-sentinel"></a>Az Azure Sentinel engedélyei

Az Azure Sentinel az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) használatával biztosítja az Azure-beli felhasználókhoz, csoportokhoz és szolgáltatásokhoz hozzárendelhető [beépített szerepköröket](../role-based-access-control/built-in-roles.md) .

Az Azure RBAC segítségével szerepköröket hozhat létre és rendelhet hozzá a biztonsági operatív csapatához az Azure Sentinel megfelelő hozzáférésének biztosításához. A különböző szerepkörök részletesen szabályozzák, hogy az Azure Sentinel mely felhasználóinak láthatják és tudják megtekinteni. Az Azure-szerepkörök közvetlenül az Azure Sentinel-munkaterülethez rendelhetők (lásd az alábbi megjegyzést), vagy egy olyan előfizetésben vagy erőforráscsoporthoz, amelyhez a munkaterület tartozik, amelyet az Azure Sentinel örököl.

## <a name="roles-for-working-in-azure-sentinel"></a>Az Azure Sentinel használatának szerepkörei

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel-specifikus szerepkörök

**Az Azure Sentinel beépített szerepkörei olvasási hozzáférést biztosítanak az Azure Sentinel-munkaterület adataihoz.**

- Az [Azure Sentinel Olvasó](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) szerepkörhöz tartozó felhasználók megtekintheti az adatokat, incidenseket, munkafüzeteket és más Azure Sentinel-erőforrásokat.

- Az [Azure Sentinel Válaszadó](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) szerepkörhöz tartozó felhasználók a fentieken kívül kezelhetik (hozzárendelhetik, elutasíthatják stb.) is az incidenseket.

- Az [Azure Sentinel Közreműködő](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) szerepkörhöz tartozó felhasználók a fentieken kívül létrehozhatnak és szerkeszthetnek munkafüzeteket, elemzési szabályokat és más Azure Sentinel-erőforrásokat is.

- Az [Azure Sentinel Automation közreműködője](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) lehetővé teszi az Azure Sentinel számára az automatizálási szabályokhoz való hozzáadását. Ez a felhasználói fiókok esetében nem jelent meg.

> [!NOTE]
>
> - A legjobb eredmények érdekében ezeket a szerepköröket az Azure Sentinel munkaterületet tartalmazó **erőforráscsoporthoz** kell rendelni. Így a szerepkörök az Azure Sentinel támogatásához központilag telepített összes erőforrásra érvényesek lesznek, mivel ezeket az erőforrásokat is ugyanabba az erőforráscsoporthoz kell helyezni.
>
> - Egy másik lehetőség, hogy közvetlenül az Azure Sentinel **munkaterületen** rendeli hozzá a szerepköröket. Ha ezt teszi, ugyanezeket a szerepköröket is hozzá kell rendelnie a SecurityInsights- **megoldás erőforráshoz** a munkaterületen. Előfordulhat, hogy más erőforrásokhoz is hozzá kell rendelnie őket, és az erőforrásokon folyamatosan kell felügyelni a szerepkör-hozzárendeléseket.

### <a name="additional-roles-and-permissions"></a>További szerepkörök és engedélyek

Előfordulhat, hogy az adott feladatra vonatkozó követelményekkel rendelkező felhasználóknak további szerepköröket vagy konkrét engedélyeket kell rendelniük a feladatok elvégzéséhez.

- **A fenyegetésekre adott válaszok automatizálására szolgáló forgatókönyvek használata**

    **Az Azure** Sentinel olyan forgatókönyveket használ, amelyekkel automatizált fenyegetésekre reagálhat. A forgatókönyvek **Azure Logic Appsre** épülnek, és külön Azure-erőforrások. Előfordulhat, hogy a biztonsági műveletek csapatának meghatározott tagjaihoz szeretne hozzárendelni Logic Apps biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) műveletekhez. A [Logic app közreműködő](../role-based-access-control/built-in-roles.md#logic-app-contributor) szerepkör használatával explicit engedélyeket rendelhet a forgatókönyvek használatához.

- **Adatforrások összekapcsolása az Azure Sentinel szolgáltatással**

    Ahhoz, hogy egy felhasználó **adatösszekötőket** vegyen fel, hozzá kell rendelnie a felhasználó írási engedélyeit az Azure Sentinel munkaterületen. Emellett jegyezze fel a szükséges további engedélyeket az egyes összekötők számára, amint az a megfelelő összekötő lapon szerepel.

- **Vendég felhasználói incidensek kiosztása**

    Ha a vendégnek incidenseket kell tudnia hozzárendelni, az Azure Sentinel válaszadó szerepkörön kívül a felhasználónak is hozzá kell rendelnie a [címtár-olvasó](../active-directory/roles/permissions-reference.md#directory-readers)szerepkört. Vegye figyelembe, hogy ez a szerepkör *nem* Azure-szerepkör, hanem egy **Azure Active Directory** szerepkör, és a normál (nem vendég) felhasználók számára ez a szerepkör alapértelmezés szerint hozzá van rendelve.

- **Munkafüzetek létrehozása és törlése**

    Ahhoz, hogy egy felhasználó Azure Sentinel-munkafüzetet hozzon létre és töröljön, a felhasználót a [figyelési közreműködő](../role-based-access-control/built-in-roles.md#monitoring-contributor)Azure monitor szerepkörével is hozzá kell rendelni. Ez a szerepkör nem szükséges a munkafüzetek *használatához* , de csak a létrehozásához és törléséhez.

### <a name="other-roles-you-might-see-assigned"></a>A hozzárendelt egyéb szerepkörök

Az Azure Sentinel-specifikus Azure-szerepkörök hozzárendelésével más Azure-beli és Log Analytics Azure-szerepköröket is megtalálhat, amelyek más célra is hozzárendelhetők a felhasználókhoz. Vegye figyelembe, hogy ezek a szerepkörök szélesebb körű engedélyeket biztosítanak, amelyek az Azure Sentinel-munkaterülethez és egyéb erőforrásokhoz is hozzáférést biztosítanak:

- **Azure-szerepkörök:** [tulajdonos](../role-based-access-control/built-in-roles.md#owner), [közreműködő](../role-based-access-control/built-in-roles.md#contributor)és [olvasó](../role-based-access-control/built-in-roles.md#reader). Az Azure-szerepkörök hozzáférést biztosítanak az összes Azure-erőforráshoz, beleértve a Log Analytics munkaterületeket és az Azure Sentinel-erőforrásokat.

- **Log Analytics szerepkörök:** [Log Analytics közreműködő](../role-based-access-control/built-in-roles.md#log-analytics-contributor) és [log Analytics olvasó](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics szerepkörök hozzáférést biztosítanak a Log Analytics-munkaterületekhez.

Ha például az Azure Sentinel **olvasó** szerepkörhöz hozzárendelt felhasználó, de az **Azure Sentinel közreműködő** szerepkört nem, akkor továbbra is szerkesztheti az elemeket az Azure sentinelben, ha az Azure-szintű **közreműködő** szerepkörhöz van rendelve. Ezért ha csak az Azure Sentinelben szeretne engedélyeket adni egy felhasználónak, gondosan távolítsa el a felhasználó korábbi engedélyeit, és ügyeljen arra, hogy ne szüntesse meg a szükséges hozzáférést egy másik erőforráshoz.

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Azure Sentinel-szerepkörök és engedélyezett műveletek

Az alábbi táblázat összefoglalja az Azure Sentinel-szerepköröket és azok engedélyezett műveleteit az Azure Sentinelben.

| Szerepkör | Forgatókönyvek létrehozása és futtatása| Analitikus szabályok és egyéb Azure Sentinel-erőforrások létrehozása és szerkesztése [*](#workbooks) | Incidensek kezelése (elutasítás, hozzárendelés stb.) | Információk, incidensek, munkafüzetek és egyéb Azure Sentinel-erőforrások megtekintése |
|---|---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | &#10003; |
| Azure Sentinel Responder | -- | -- | &#10003; | &#10003; |
| Azure Sentinel Contributor | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel közreműködő + Logic app közreműködő | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* A munkafüzetek létrehozásához és törléséhez a további [figyelési közreműködő](../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkörre van szükség. További információ: [további szerepkörök és engedélyek](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Egyéni szerepkörök és speciális Azure RBAC

- **Egyéni szerepkörök**. Az Azure beépített szerepköreinek használatával vagy ahelyett, hogy az Azure Sentinelhez egyéni Azure-szerepköröket is létrehozhat. Az Azure Sentinel Azure-beli egyéni szerepkörei ugyanúgy jönnek létre, mint más [Egyéni Azure-szerepköröket](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)az [Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) és az [Azure log Analytics erőforrásaira](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)adott engedélyek alapján.

- **Log Analytics RBAC**. A Log Analytics Advanced Azure szerepköralapú hozzáférés-vezérlést az Azure Sentinel-munkaterület összes adatához használhatja. Ebbe beletartozik az adattípuson alapuló Azure-RBAC és az erőforrás-környezet Azure-RBAC is. További információkért lásd:

    - [A naplózási és munkaterületek kezelése Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Az Azure Sentinel erőforrás-környezeti RBAC](resource-context-rbac.md)
    - [Táblaszintű RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Az erőforrás-környezet és a tábla szintű RBAC kétféleképpen biztosít hozzáférést az Azure Sentinel-munkaterületen lévő konkrét információhoz anélkül, hogy hozzáférést kellene biztosítani a teljes Azure Sentinel-élményhez.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan dolgozhat az Azure Sentinel-felhasználók szerepköreivel, és hogy az egyes szerepkörök hogyan teszik lehetővé a felhasználók számára.

Az Azure-beli biztonsági és megfelelőségi blogbejegyzések az [Azure Sentinel blogban](https://aka.ms/azuresentinelblog)találhatók.
