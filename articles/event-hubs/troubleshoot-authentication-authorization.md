---
title: Hitelesítési és engedélyezési problémák elhárítása – Azure Event Hubs
description: Ez a cikk tájékoztatást nyújt az Azure Event Hubs-vel kapcsolatos hitelesítési és engedélyezési hibák elhárításáról.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329610"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Hitelesítési és engedélyezési problémák elhárítása – Azure Event Hubs
A [kapcsolódási problémák elhárítása](troubleshooting-guide.md) című cikk tippekkel szolgál az Azure Event Hubs kapcsolódási problémáinak elhárításához. Ez a cikk tippeket és javaslatokat tartalmaz az Azure Event Hubs-vel kapcsolatos hitelesítési és engedélyezési problémák elhárításához. 

## <a name="if-you-are-using-azure-active-directory"></a>Ha Azure Active Directory használ
Ha Azure Active Directory (Azure AD) szolgáltatást használ az Azure Event Hubs hitelesítéséhez és engedélyezéséhez, ellenőrizze, hogy az Event hub-hoz hozzáférő identitás tagja-e **a megfelelő** **erőforrás-hatókörnek** (fogyasztói csoport, Event hub, névtér, erőforráscsoport vagy előfizetés).

### <a name="azure-roles"></a>Azure-szerepkörök
- Az [Azure Event Hubs adattulajdonos](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) Event Hubs erőforrásokhoz való teljes hozzáféréshez.
- Az [Azure Event Hubs adatfeladót](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) a küldési hozzáféréshez.
- [Azure Event Hubs adatfogadó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) a fogadási hozzáféréshez.

A séma-beállításjegyzék beépített szerepköreivel kapcsolatban lásd: [séma beállításjegyzékbeli szerepkörei](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Erőforrás-hatókörök
- **Fogyasztói csoport**: ezen a hatókörön a szerepkör-hozzárendelés csak erre az entitásra vonatkozik. A Azure Portal jelenleg nem támogatja az Azure-szerepkörök egy rendszerbiztonsági tag számára való hozzárendelését ezen a szinten. 
- **Event hub**: a szerepkör-hozzárendelés az Event hub-entitásra és az alatta lévő fogyasztói csoportra vonatkozik.
- **Névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes topológiáját Event Hubs.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Event Hubs erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Event Hubs erőforrására vonatkozik.

További információért tekintse át a következő cikkeket:

- [Alkalmazás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-application.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Ha megosztott hozzáférési aláírásokat (SAS) használ
Ha [sas](authenticate-shared-access-signature.md)-t használ, kövesse az alábbi lépéseket: 

- Győződjön meg arról, hogy az Ön által használt SAS-kulcs helyes. Ha nem, használja a megfelelő SAS-kulcsot.
- Győződjön meg arról, hogy a kulcs rendelkezik a megfelelő engedélyekkel (küldés, fogadás vagy kezelés). Ha nem, olyan kulcsot használjon, amely rendelkezik a szükséges engedélyekkel. 
- Ellenőrizze, hogy a kulcs lejárt-e. Javasoljuk, hogy a lejárata előtt is újítsa meg a SAS-t. Ha az ügyfél és a Event Hubs szolgáltatási csomópontok között óra van elferdítve, előfordulhat, hogy a hitelesítési jogkivonat lejár, mielőtt az ügyfél felismeri. Az aktuális implementációs fiókok órája akár 5 percet is igénybe vehet, azaz a token 5 perccel a lejárata előtt megújítja az ügyfelet. Ezért ha az óra ferde értéke 5 percnél nagyobb, akkor az ügyfél megfigyelheti az időszakos hitelesítési hibákat.
- Ha a **sas kezdési ideje** **most** értékre van állítva, akkor az első néhány perc időszakos meghibásodása miatt időnként eldöntheti, hogy az aktuális idő a különböző gépeken milyen eltéréseket mutat. Állítsa be a kezdési időpontot legalább 15 percre a múltban, vagy ne állítsa be egyáltalán. Ugyanez általában a lejárati időre is vonatkozik. 

További információért tekintse át a következő cikkeket: 

- [Hitelesítés közös hozzáférésű aláírások (SAS) használatával](authenticate-shared-access-signature.md). 
- [A Event Hubs-erőforrásokhoz való hozzáférés engedélyezése közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

* [Kapcsolati problémák hibaelhárítása](troubleshooting-guide.md)
