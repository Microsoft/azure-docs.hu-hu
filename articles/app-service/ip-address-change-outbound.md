---
title: Felkészülés a kimenő IP-címek változására
description: Ha a kimenő IP-cím módosítva lesz, Ismerje meg, mi a teendő, hogy az alkalmazás továbbra is működjön a változás után.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74671670"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>A kimenő IP-címek módosításának előkészítése

Ha értesítést kapott arról, hogy a Azure App Service alkalmazás kimenő IP-címei változnak, kövesse a cikk utasításait.

## <a name="determine-if-you-have-to-do-anything"></a>Annak megállapítása, hogy bármit kell-e tennie

* 1. lehetőség: Ha a App Service alkalmazás nem használ IP-szűrést, explicit befoglalási listát vagy a kimenő forgalom (például az Útválasztás vagy a tűzfal) speciális kezelését, nincs szükség beavatkozásra.

* 2. lehetőség: Ha az alkalmazás speciális módon kezeli a kimenő IP-címeket (lásd az alábbi példákat), adja hozzá az új kimenő IP-címeket, ahol a meglévők megjelennek. Ne cserélje le a meglévő IP-címeket. Az új kimenő IP-címeket a következő szakaszban található utasítások alapján érheti el.

  Előfordulhat például, hogy egy kimenő IP-cím explicit módon szerepel az alkalmazáson kívüli tűzfalban, vagy egy külső fizetési szolgáltatás rendelkezhet olyan engedélyezési listával, amely tartalmazza az alkalmazás kimenő IP-címét. Ha a kimenő címe az alkalmazáson kívüli listában van konfigurálva, akkor módosítani kell.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>A kimenő IP-címek megkeresése a Azure Portal

Az új kimenő IP-címek a portálon jelennek meg, mielőtt érvénybe lépnek. Amikor az Azure megkezdi az újak használatát, a régieket már nem fogja használni. A rendszer egyszerre csak egy készletet használ, ezért a befoglalási listán szereplő bejegyzéseknek a régi és az új IP-címmel kell rendelkezniük ahhoz, hogy a kapcsoló meggátolja a kimaradást. 

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza a **app Services** lehetőséget.

3.  Válassza ki a App Service alkalmazást a listából.

1.  Ha az alkalmazás egy függvény alkalmazás, tekintse meg a [Function app kimenő IP-címei](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)című témakört.

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs menüben a **Tulajdonságok** elemre, és keresse meg a **kimenő IP-címek** feliratú szakaszt.

5. Másolja ki az IP-címeket, és vegye fel őket a kimenő forgalom speciális kezelésére, például egy szűrőre vagy egy engedélyezett listára. Ne törölje a listában szereplő meglévő IP-címeket.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan lehet előkészíteni az Azure által kezdeményezett IP-címek változásait. A Azure App Service IP-címeivel kapcsolatos további információkért lásd: [bejövő és kimenő IP-címek a Azure app Serviceban](overview-inbound-outbound-ips.md).
