---
title: SSL-IP-cím módosítása – az Azure App Service előkészítése
description: Ha módosítani kell az SSL-IP-címe, megtudhatja, mit kell tennie, hogy az alkalmazása továbbra is működik, a módosítás után.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6c8c86ff6212acc31e961d6ae62836ca2b7b7380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268906"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Egy SSL-IP-cím módosítása előkészítése

Ha kapott egy értesítés, hogy az Azure App Service-alkalmazás SSL IP-címe módosul, kövesse a cikkben meglévő SSL IP-cím felszabadítása, és hozzárendelhet egy újat.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>SSL-IP-címek és új

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza ki a **App Services**.

3.  Válassza ki az App Service-alkalmazást a listából.

4.  Alatt a **beállítások** fejléc, kattintson a **SSL-beállítások** a bal oldali navigációs.

1. Az SSL-kötések szakaszban jelölje ki a gazdagép neve bejegyzést. Megnyílik a szerkesztőben válassza **SNI SSL** a a **SSL-típus** legördülő menüben, majd kattintson **kötésének hozzáadása**. A művelet a sikert jelző üzenet megjelenésekor azt a meglévő IP-cím kiadása.

6.  Az a **SSL-kötések** területén válassza ismét a tanúsítványt az ugyanazon gazdagép neve rekord. A szerkesztőben megjelenő, ezúttal válassza **IP-alapú SSL** a a **SSL-típus** legördülő menüben, majd kattintson **kötésének hozzáadása**. Amikor megjelenik a művelet a sikert jelző üzenet, melyeket beszerezett új IP-címet.

7.  Ha egy A rekordot (közvetlenül a IP-címre mutató DNS-rekord) a tartomány regisztrációs portálon (harmadik féltől származó DNS-szolgáltató vagy az Azure DNS-ben) van konfigurálva, az újonnan létrehozott egy cserélje le a meglévő IP-cím. Az új IP-cím a következő utasításokat a következő szakaszban találja.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Az Azure Portalon keresse meg az új SSL-IP-cím

1.  Várjon néhány percet, és nyissa meg a [az Azure portal](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza ki a **App Services**.

3.  Válassza ki az App Service-alkalmazást a listából.

4.  Alatt a **beállítások** fejléc, kattintson a **tulajdonságok** a bal oldali navigációs és a keresés feliratú szakasz **virtuális IP-cím**.

5. Másolja ki az IP-címet, és konfigurálja újra a tartományi rekord vagy IP-mechanizmust.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen egy IP-cím módosítása az Azure által kezdeményezett előkészítése. IP-címek az Azure App Service kapcsolatos további információkért lásd: [SSL és az SSL IP-címek az Azure App Service](overview-inbound-outbound-ips.md).
