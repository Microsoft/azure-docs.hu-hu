---
title: Felkészülés a bejövő IP-címek változására
description: Ha a bejövő IP-cím megváltozik, Ismerje meg, mi a teendő, hogy az alkalmazás továbbra is működni fog a módosítás után.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672417"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Bejövő IP-cím módosításának előkészítése

Ha értesítést kapott arról, hogy a Azure App Service alkalmazás bejövő IP-címe változik, kövesse a jelen cikk utasításait.

## <a name="determine-if-you-have-to-do-anything"></a>Annak megállapítása, hogy bármit kell-e tennie

* 1. lehetőség: Ha a App Service alkalmazás nem rendelkezik egyéni tartománnyal, nincs szükség beavatkozásra.

* 2. lehetőség: Ha csak egy CNAME rekord (URI-ra mutató DNS-rekord) van konfigurálva a tartományi regisztrációs portálon (harmadik fél DNS-szolgáltatója vagy Azure DNS), nincs szükség beavatkozásra.

* 3. lehetőség: Ha egy rekord (a közvetlenül az IP-címére mutató DNS-rekord) be van állítva a tartományi regisztrációs portálon (harmadik fél DNS-szolgáltatója vagy Azure DNS), cserélje le a meglévő IP-címet az újat. Az új IP-címet a következő szakaszban található utasítások alapján érheti el.

* 4. lehetőség: Ha az alkalmazás egy terheléselosztó, IP-szűrő vagy bármely más, az alkalmazás IP-címét igénylő IP-mechanizmus mögött van, cserélje le a meglévő IP-címet az újat. Az új IP-címet a következő szakaszban található utasítások alapján érheti el.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Az új bejövő IP-cím megkeresése a Azure Portal

Az alkalmazáshoz megadott új bejövő IP-cím a **virtuális IP-cím** mezőben lévő portálon található. Ez az új IP-cím és a régi is csatlakoztatva van az alkalmazáshoz, és később a régi le lesz választva.

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza a **app Services** lehetőséget.

3.  Válassza ki a App Service alkalmazást a listából.

1.  Ha az alkalmazás egy függvény alkalmazás, tekintse meg a [Function app bejövő IP-címe](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)című témakört.

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs menüben a **Tulajdonságok** elemre, és keresse meg a **virtuális IP-cím** feliratú szakaszt.

5. Másolja ki az IP-címet, és konfigurálja újra a tartományi rekordot vagy az IP-mechanizmust.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan lehet előkészíteni az Azure által kezdeményezett IP-címek változásait. A Azure App Service IP-címeivel kapcsolatos további információkért lásd: [bejövő és kimenő IP-címek a Azure app Serviceban](overview-inbound-outbound-ips.md).
