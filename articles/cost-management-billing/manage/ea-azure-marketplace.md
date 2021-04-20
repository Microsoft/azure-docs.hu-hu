---
title: Azure Piactér
description: Ismerteti, hogyan használhatják az Azure Marketplace-t az EA-ügyfelek.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726825"
---
# <a name="azure-marketplace"></a>Azure Piactér

Ez a cikk azt ismerteti, hogy az EA-ügyfelek és a partnerek hogyan tekinthetik meg a Marketplace díjait, és hogyan engedélyezhetik az Azure Marketplace-en történő vásárlásokat.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace EA-ügyfelek számára

A közvetlen ügyfelek esetében az Azure Marketplace-díjak az Azure Enterprise Portalon láthatók. Az Azure Marketplace-vásárlások és a használat számlázása az Azure-előrefizetési kereten kívül, negyedévente, havonta és utólagosan történik.

A közvetett ügyfelek megtalálhatják Azure Marketplace-előfizetéseiket az Azure Enterprise Portal **Előfizetések kezelése** lapján, de a díjszabás rejtve marad. Az Azure Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz (LSP) kell fordulniuk.

Az új havi vagy évente ismétlődő Azure Marketplace-vásárlások esetében a teljes összeg lesz kiszámlázva arra az időszakra, amelyben az Azure Marketplace-tételeket megvásárolták. Ezek a tételek a következő időszakban automatikusan megújulnak az eredeti vásárlás napján.

A havonta ismétlődő díjak megújítása továbbra is az egyes naptári hónapok első napján történik. Az éves díjak megújítása a vásárlási dátum évfordulóján történik.

Az Azure Marketplace-en elérhető külső viszonteladói szolgáltatások némelyike mostantól a Nagyvállalati Szerződés (EA) Azure-előrefizetését használja. Ezeknek a szolgáltatásoknak a számlázása korábban az EA Azure-előrefizetési keretén kívül, külön számlákkal történt. A szolgáltatásokra az Azure Marketplace-en érvényes EA Azure-előrefizetési keret hozzájárul az ügyfelek vásárlás- és fizetéskezelésének egyszerűsítéséhez. Az Azure-előrefizetést használó szolgáltatások teljes listája az [Azure-webhely 2018. március 6-i frissítésében](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) érhető el.

### <a name="partners"></a>Partnerek

A licencmegoldás-szolgáltatók letölthetik az Azure Marketplace-árlistát az Azure Enterprise Portal Árlistájáról. Válassza a jobb felső sarokban található **Marketplace-árlista** lehetőséget. Az Azure Marketplace-árlistában az összes rendelkezésre álló szolgáltatás és azok ára is megjelenik.

Az árlista letöltéséhez:

1. Az Azure Enterprise Portalon lépjen a **Jelentések** > **Árlista** lapra.
1. A jobb felső sarokban, a felhasználóneve alatt keresse meg az Azure Marketplace-árlistára mutató hivatkozást.
1. Kattintson a jobb egérgombbal a hivatkozásra, majd válassza a **Cél mentése másként** lehetőséget.
1. A **Mentés** ablakban módosítsa a dokumentum címét a következőre: `AzureMarketplacePricelist.zip`. Ezáltal a fájl formátuma .xlsx helyett .zip lesz.
1. Ha a letöltés befejeződött, az országspecifikus árlisták egy zip-fájlban lesznek elérhetők.
1. A licencmegoldás-szolgáltatóknak az egyes országok fájljaira kell hivatkozniuk az országspecifikus díjszabás megállapításához. A licencmegoldás-szolgáltatók az **Értesítések** lapon találják azokat a termékváltozatokat, amelyek teljesen újak, illetve amelyek el lettek távolítva.
1. Ritkán fordul elő árváltozás. Ha mégis megtörténik, a licencmegoldás-szolgáltatók 30 nappal korábban e-mailben kapnak értesítést az áremelésekről és a valutaárfolyamok módosulásáról.
1. A licencmegoldás-szolgáltatók regisztrációnként, független szoftvergyártók szerint, illetve negyedévente egy számlát kapnak.

### <a name="enabling-azure-marketplace-purchases"></a>Az Azure Marketplace-vásárlások engedélyezése

A vállalati rendszergazdák engedélyezhetik vagy letilthatják az Azure Marketplace-vásárlásokat a regisztrációjukhoz tartozó összes Azure-előfizetésre vonatkozóan. Ha a vállalati rendszergazda letiltja a vásárlásokat, és vannak olyan Azure-előfizetések, amelyek már rendelkeznek Azure Marketplace-előfizetésekkel, az intézkedés ezeket az Azure Marketplace-előfizetéseket nem érinti.

Bár az ügyfelek átalakíthatják közvetlen Azure-előfizetéseiket Azure EA-előfizetéssé, ha az Azure Enterprise Portalon lévő regisztrációjukhoz társítják őket, a művelet nem konvertálja automatikusan az alárendelt gyermek előfizetéseket.

Az Azure Marketplace-vásárlások engedélyezése:

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Lépjen a **Kezelés** menüpontra.
1. A **Regisztráció részletei** szakaszban válassza az **Azure Marketplace** vonalelem mellett található ceruzaikont.
1. Válassza igény szerint az **Engedélyezett/Letiltott** vagy az ingyenes **Csak BYOL-termékváltozat** lehetőséget.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> A BYOL (saját licenc használata) és a Csak ingyenes lehetőség az Azure Marketplace-vásárlásokat és -beszerzéseket a BYOL- és az ingyenes termékváltozatokra korlátozza.

### <a name="services-billed-hourly-for-azure-ea"></a>Az Azure EA esetében óradíj alapján számlázott szolgáltatások

Az alábbi szolgáltatások számlázása a nagyvállalati szerződés esetében óradíj alapján történik, és nem havi díjszabás szerint, mint a Microsoft Online Előfizetési Program (MOSP) esetében:

- Alkalmazásszolgáltatási hálózat
- Web Application Firewall (Webalkalmazási tűzfal)

### <a name="azure-remoteapp"></a>Azure RemoteApp

Ha nagyvállalati szerződéssel rendelkezik, akkor az Azure RemoteAppért a nagyvállalati szerződésben megállapított árszint alapján fizet. Nem számítunk fel további díjakat. A standard ár a kezdeti 40 órát tartalmazza. A korlátlan díjszabás a kezdeti 80 órát fedezi. A RemoteApp a 80 órán túl nem bocsátja ki a használatot.

## <a name="next-steps"></a>További lépések

- Itt további információt találhat a [díjszabásról](ea-pricing-overview.md).
- A gyakori [Cost Management + Billing tekintse](../cost-management-billing-faq.yml) meg a szolgáltatásokra és az Azure EA Azure Marketplace-előrefizetésre vonatkozó kérdések és válaszok listáját.