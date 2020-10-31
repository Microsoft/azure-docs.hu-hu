---
title: Technikai részletek hozzáadása SaaS-ajánlathoz a Microsoft partner Centerben
description: Megtudhatja, hogyan biztosíthatja a Microsoft kereskedelmi Marketplace szolgáltatásként nyújtott szoftver-(SaaS-) ajánlat technikai részleteit.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: d84e1508dec4d4a5802f7a2ac8c0e6ada5a8044c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128691"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Technikai részletek hozzáadása SaaS-ajánlathoz

Ez a cikk a Microsoft kereskedelmi piactérről a megoldáshoz való kapcsolódást segítő technikai részletek megadását ismerteti. Ez a kapcsolat lehetővé teszi, hogy az Ön ajánlatát kiépítse az ügyfélnek, ha úgy dönt, hogy beszerezze és felügyelje. További információk ezekről a beállításokról: [technikai információk](plan-saas-offer.md#technical-information).

> [!NOTE]
> Ha úgy dönt, hogy egymástól függetlenül dolgozza fel a tranzakciókat, akkor ez a beállítás nem jelenik meg. Ehelyett ugorjon az [SaaS-ajánlat értékesítésének módjára](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Technikai konfiguráció

A **technikai konfiguráció** lapon megadhatja azokat a műszaki adatokat, amelyeket a kereskedelmi piactér az SaaS-alkalmazással vagy-megoldással való kommunikációra használ. 

- Kezdőlap **URL-címe** (kötelező) – határozza meg az SaaS-webhely URL-címét (például: `https://contoso.com/signup` ), amelyet az ügyfelek a kereskedelmi piactéren való beszerzése után és a konfigurációs folyamat az újonnan létrehozott SaaS-előfizetésből való aktiválásával fognak leképezni.

  > [!IMPORTANT]
  > A kezdőlapjának a 24/7-es verzióját kell használnia. Ez az egyetlen módja, ha értesítést kap a SaaS-ajánlatok kereskedelmi piactéren végzett új beszerzéséről, vagy egy ajánlat aktív előfizetésének konfigurációs kéréseiről.

- **Kapcsolat webhook** (kötelező) – minden olyan aszinkron esemény esetében, amelyet a Microsoftnak el kell küldenie Önnek (például az SaaS-előfizetés meg lett szakítva), meg kell adnia egy kapcsolat webhook URL-címét. Meghívjuk ezt az URL-címet, hogy értesítse Önt az eseményről.

  > [!IMPORTANT]
  > A webhooknak a 24/7-as verzióját kell futtatnia, mivel ez az egyetlen módja, ha értesítést szeretne kapni az ügyfelek SaaS-előfizetésével kapcsolatban, amelyek a kereskedelmi Piactéren keresztül vásároltak.

- **Azure Active Directory bérlői azonosító** (kötelező) – a Azure Active Directory (Azure ad) alkalmazás BÉRLŐi azonosítójának megkereséséhez nyissa meg Azure Active Directory [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) paneljét. A **megjelenítendő név** oszlopban válassza ki az alkalmazást. Ezután keresse meg a felsorolt **címtár (bérlő) azonosítószámát** (például `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory alkalmazás-azonosító** (kötelező) – az [alkalmazás azonosítójának](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)megkereséséhez nyissa meg a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) panelt Azure Active Directory. A **megjelenítendő név** oszlopban válassza ki az alkalmazást. Ezután keresse meg a felsorolt alkalmazás (ügyfél) AZONOSÍTÓSZÁMÁT (például: `50c464d3-4930-494c-963c-1e951d15360e` ).

A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: terv áttekintése.

## <a name="next-steps"></a>Következő lépések

- [Csomagok létrehozása SaaS-ajánlathoz](create-new-saas-offer-plans.md).