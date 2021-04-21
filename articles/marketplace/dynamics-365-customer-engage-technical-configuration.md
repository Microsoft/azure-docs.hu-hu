---
title: A Dynamics 365 for Customer Engagement & PowerApps-ajánlat technikai konfigurációjának beállítása Microsoft AppSource (Azure Marketplace)
description: A Dynamics 365 for Customer Engagement & PowerApps technikai konfigurációt kínál a Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820396"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>A Dynamics 365 for Customer Engagement & Power Apps ajánlat technikai konfigurációjának beállítása

Ez az oldal határozza meg az ajánlathoz való csatlakozáshoz használt technikai részleteket. Ez a kapcsolat lehetővé teszi, hogy kiépítsük az ajánlatot a végfelhasználó számára, ha úgy dönt, hogy megvásárolja.

## <a name="offer-information"></a>Ajánlat adatai

**Az alaplicenc-modell** határozza meg, hogy az ügyfelek hogyan vannak hozzárendelve az alkalmazáshoz a CRM felügyeleti központban. Válassza **az Erőforrás** lehetőséget a példányalapú licenceléshez, vagy a **Felhasználó** lehetőséget, ha a licencek bérlőnként egy licencet rendelnek hozzá.

Az **S2S kimenő** és CRM biztonságos tároló-hozzáférés szükséges jelölőnégyzet lehetővé teszi a CRM biztonságos tároló vagy a kiszolgálók között (S2S) kimenő hozzáférés konfigurálást. Ehhez a funkcióhoz a Dynamics 365 Teamnek speciális megfontolásra van szüksége a minősítési fázisban. A Microsoft kapcsolatba lép Önnel a funkció támogatásához szükséges további lépések érdekében.

Hagyja **üresen az Alkalmazáskonfiguráció URL-címe** mezőt; A későbbi használatra.

## <a name="crm-package"></a>CRM-csomag

A **csomag helyének URL-címeként** adja meg egy Azure Blob Storage CRM-csomag .zip-fájlját tartalmazó fiók URL-címét. Az URL-címbe foglalhat egy csak olvasható SAS-kulcsot, hogy a Microsoft átveheti az Ön csomagját ellenőrzés céljából.

> [!IMPORTANT]
> A közzétételi blokkok elkerülése érdekében győződjön meg arról, hogy a Blob Storage URL-címének lejárati dátuma nem járt le. A dátumot a szabályzat eléréséhez módosíthatja. **Javasoljuk, hogy a lejárati** idő legyen legalább egy hónap.

Jelölje be **a Több CRM-csomag a** csomagfájlban jelölőnégyzetet, ha van ilyen. Ha igen, mindenképpen foglalja bele az összes csomagot a .zip-fájlba.

A csomag felépítésével és szerkezetének frissítésével kapcsolatos részletes információkért [lásd: Step 3: Create an AppSource package for your app (3. lépés: AppSource-csomag létrehozása az alkalmazáshoz).](/powerapps/developer/common-data-service/create-package-app-appsource)

## <a name="crm-package-availability"></a>CRM-csomagok rendelkezésre állása

Válassza **a + Régió hozzáadása lehetőséget** azon földrajzi régiók megadásához, amelyekben a CRM-csomag elérhető lesz az ügyfelek számára. A következő szuverén régiókban való üzembe helyezéshez különleges engedélyre és érvényesítésre van szükség a tanúsítási folyamat során: [Németország,](../germany/index.yml) [AZ USA kormányzati](../azure-government/documentation-government-welcome.md)felhőszolgáltatása és a TIP.

Alapértelmezés szerint a rendszer a fent megadott alkalmazáskonfigurációs **URL-címet** használja az egyes régiókhoz. Ha szeretné, megadhatja egy külön alkalmazáskonfigurációs URL-címet egy vagy több adott régióhoz.

Válassza **a Piszkozat** mentése lehetőséget, mielőtt továbblép a bal oldali navigációs menüBen található **Co-sell with Microsoft (Közös** értékesítés a Microsofttal) következő lapra. További információ a Microsofttal való közös értékesítés beállításával kapcsolatban (nem kötelező), lásd: [Együttműködési partnerek bevonása.](marketplace-co-sell.md) Ha nem együtt értékesít, vagy ha végzett, folytassa az alábbi **Következő lépésekkel.**

## <a name="next-steps"></a>Következő lépések

- [Kiegészítő tartalom konfigurálása](dynamics-365-customer-engage-supplemental-content.md)
