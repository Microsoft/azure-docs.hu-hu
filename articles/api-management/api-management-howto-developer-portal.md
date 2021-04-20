---
title: Az Azure-beli fejlesztői portál áttekintése API Management
titleSuffix: Azure API Management
description: Ismerje meg a fejlesztői portált a API Management egy testreszabható webhelyen, ahol az API-felhasználók felfedezhetik az API-kat.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: aaf02affce924232deb56bf7694771c838b0b323
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739601"
---
# <a name="overview-of-the-developer-portal"></a>A fejlesztői portál áttekintése

A fejlesztői portál egy automatikusan létrehozott, teljes mértékben testreszabható webhely, amely az API-k dokumentációját tartalmazza. Az API-felhasználók itt fedezhetik fel az API-kat, itt tanulhatják meg azok használatát, kérhetnek hozzáférést, és kipróbálhatják őket.

A cikkben bemutatottak szerint testreszabhatja és kiterjesztheti a fejlesztői portált az adott forgatókönyvekhez. 

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrálás az örökölt portálról

> [!IMPORTANT]
> Az örökölt fejlesztői portál elavult, és csak biztonsági frissítéseket kap. Továbbra is használhatja a szokásos módon, a 2023. októberi kiesőgáig, amikor a rendszer eltávolítja az összes API Management szolgáltatásból.

Az új fejlesztői portálra való migrálásról a dedikált dokumentációs cikkben [olvashat.](developer-portal-deprecated-migration.md)

## <a name="customization-and-styling"></a>Testreszabás és stílus

A fejlesztői portál a beépített, áthúzható vizualizációszerkesztővel testre szabható és stílusos is lehet. További [részleteket ebben az](api-management-howto-developer-portal-customize.md) oktatóanyagban talál.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Bővíthetőség

A API Management szolgáltatás tartalmaz egy beépített, mindig naprakész, felügyelt **fejlesztői** portált. Ezt a webes felületen Azure Portal el.

Ha egyéni logikával kell kiterjesztenie, amely nem azonnal használható, módosíthatja annak kódbázisát. A portál kódbázisa egy [GitHub-adattárban érhető el.](https://github.com/Azure/api-management-developer-portal) Megvalósíthat például egy új widgetet, amely integrálható egy külső támogatási rendszerrel. Új funkciók megvalósításakor az alábbi lehetőségek közül választhat:

- **Az eredményül** kapott portált a saját szolgáltatásán kívül API Management saját maga. Amikor ön a portált saját maga tartja fenn, ön lesz a karbantartó, és Ön felelős a frissítéséért. Azure ügyfélszolgálata csak a saját üzemeltetett portálok alapszintű beállítását [nyújtjuk.](developer-portal-self-host.md)
- Nyisson meg egy lekéréses kérelmet a API Management, hogy új funkciókat egyesítsen a **felügyelt** portál kódbázisával.

A részletekért és utasításokért tekintse meg a GitHub-adattárat és a widget [implementálását ismertető oktatóanyagot.](developer-portal-implement-widgets.md) [](https://github.com/Azure/api-management-developer-portal) A felügyelt portál [testreszabását](api-management-howto-developer-portal-customize.md) ismertető oktatóanyag végigvezeti a portál  felügyeleti panelján, amely gyakori a felügyelt és a saját maga által üzemeltetett **verziókban.**


## <a name="next-steps"></a>Következő lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetett verziójának beállítása](developer-portal-self-host.md)
- [Saját widget implementálja](developer-portal-implement-widgets.md)

Egyéb erőforrások tallózása:

- [GitHub-adattár a forráskódmal](https://github.com/Azure/api-management-developer-portal)
- [Gyakori kérdések a fejlesztői portálról](developer-portal-faq.md)
