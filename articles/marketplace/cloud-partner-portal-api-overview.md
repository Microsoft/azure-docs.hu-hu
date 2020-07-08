---
title: Cloud Partner Portal API-hivatkozás – Microsoft kereskedelmi piactér
description: A, a használatának előfeltételei és a piactér API-műveleteinek listája.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 2f79dea4baaa2589333af4e15aece06c1d86b9c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516255"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-referencia

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a jelen dokumentumban felsorolt [CPP API-k módosításait](#changes-to-cpp-apis-after-the-migration-to-partner-center) annak biztosítására, hogy a kód továbbra is működjön a partneri központba való Migrálás után.

A Cloud Partner Portal REST API-k lehetővé teszik a számítási feladatok, ajánlatok és kiadói profilok programozott lekérését és kezelését. Az API-k szerepköralapú hozzáférés-vezérlés (RBAC) használatával kényszerítik ki a megfelelő engedélyeket a feldolgozási idő során.

Ez a hivatkozás a Cloud Partner Portal REST API-k technikai részleteit ismerteti. A dokumentumban szereplő hasznos adatok csak referenciául szolgálnak, és új funkciók hozzáadásakor változhatnak.

## <a name="prerequisites-and-considerations"></a>Előfeltételek és megfontolások

Az API-k használata előtt tekintse át a következőket:

- Az [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) című cikkből megtudhatja, hogyan adhat hozzá egy egyszerű szolgáltatásnevet a fiókjához, és hogyan szerezhet be Azure Active Directory (Azure ad) hozzáférési tokent a hitelesítéshez.
- Az API-k meghívásához elérhető két [Egyidejűség-vezérlési](./cloud-partner-portal-api-concurrency-control.md) stratégia.
- További API- [megfontolások](./cloud-partner-portal-api-considerations.md), például verziószámozás és hibakezelés.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>A CPP API-k módosításai a partneri központba való Migrálás után

| **API** | **Módosítások ismertetése** | **Hatás** |
| ------- | ---------------------- | ---------- |
| Közzététel utáni, GoLive, Mégse | Az áttelepített ajánlatok esetében a válasz fejlécének formátuma eltérő lesz, de továbbra is ugyanúgy működik, mint a művelet állapotának lekéréséhez szükséges relatív elérési út. | Az ajánlathoz tartozó BEJEGYZÉSi kérelmek bármelyikének elküldésekor a hely fejléce az ajánlat áttelepítési állapotának függvényében két formátummal fog rendelkezni:<ul><li>Nem áttelepített ajánlatok<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Áttelepített ajánlatok<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Művelet beolvasása | A válaszban korábban támogatott "Notification-mail" mezőre vonatkozó ajánlati típusok esetében ez a mező elavult, és az áttelepített ajánlatok esetében már nem lesz visszaadva. | Az áttelepített ajánlatok esetében a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API szolgáltatás egyezteti az e-mailek küldését a partner Centerben az értesítő e-mail folyamattal. Az értesítéseket a rendszer a partner Center fiók beállításainak eladó kapcsolattartási adatok szakaszában megadott e-mail-címre küldi el, hogy értesítse a művelet előrehaladásáról.<br><br>Kérjük, tekintse át az e-mail-címet a fiókpartner [postafiók-beállítások](https://partner.microsoft.com/dashboard/account/management) területén, és ellenőrizze, hogy a megfelelő e-mailt adta-e meg az értesítésekhez.  |

## <a name="common-tasks"></a>Gyakori feladatok

Ez a hivatkozás a következő gyakori feladatok végrehajtására szolgáló API-kat ismerteti.

### <a name="offers"></a>Ajánlatok

- [Az összes ajánlat beolvasása](./cloud-partner-portal-api-retrieve-offers.md)
- [Adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Ajánlat állapotának lekérése](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Ajánlat létrehozása](./cloud-partner-portal-api-creating-offer.md)
- [Ajánlat közzététele](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Műveletek

- [Műveletek lekérése](./cloud-partner-portal-api-retrieve-operations.md)
- [Műveletek megszakítása](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Alkalmazás közzététele

- [Élesítés](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Egyéb feladatok

- [A virtuális gépekre vonatkozó ajánlatok díjszabásának beállítása](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Hibaelhárítás

- [Hitelesítési hibák hibaelhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
