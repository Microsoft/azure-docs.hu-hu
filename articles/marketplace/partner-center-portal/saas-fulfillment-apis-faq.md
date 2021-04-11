---
title: SaaS-teljesítési API – GYIK – Microsoft kereskedelmi piactér
description: Ismerje meg a Microsoft kereskedelmi piactér integrációs követelményeit, amelyek lehetővé teszik az Azure-ügyfelek számára az SaaS-ajánlatokra való előfizetést.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/19/2021
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 760e7210d054e44dfec6d6a6e480baecd04d6807
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044124"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>SaaS-megvalósítási API-kkal kapcsolatos gyakori kérdések

Ez a cikk a Microsoft kereskedelmi piactérhez kapcsolódó számos integrációs követelményt ismerteti, amely lehetővé teszi az Azure-ügyfelek számára az SaaS-ajánlatok előfizetését.

## <a name="discovery-experience"></a>Felderítési élmény

Az SaaS-ajánlat közzététele után az Azure-felhasználók felfedezhetik az Azure Marketplace-en. Az ügyfelek a terméktípus (SaaS) alapján szűrhetik az ajánlatokat, és felfedezhetik azokat az SaaS-szolgáltatásokat, amelyekre kíváncsiak.

## <a name="purchase-experience"></a>Vásárlási élmény

Ha egy felhasználót érdekli egy adott SaaS-szolgáltatás, a felhasználó előfizethet az Azure Marketplace-ről.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Mit jelent az, ha egy Azure-felhasználó előfizet egy SaaS-ajánlatra az Azure Marketplace-en?

Ez azt jelenti, hogy a felhasználók megtekinthetik a SaaS szolgáltatáshoz társított használati feltételeket és adatvédelmi nyilatkozatot, és elfogadják, hogy az Ön által beállított számlázási feltételekkel megfizetik, az SaaS-ajánlat közzétevője, a Microsoft számláján. A felhasználók az Azure-ban meglévő fizetési profiljuk használatával fizethetnek az SaaS-szolgáltatás felhasználására.

Ez a képesség többféleképpen is hasznos lehet. Az ügyfelek mostantól egy helyen felfedezhetik és előfizethetik Microsoft Cloud platformot megbízható forrásként, anélkül, hogy minden egyes ISV-szoftvert használni kellene. Emellett az ügyfelek a meglévő fizetési profilt is használhatják anélkül, hogy az egyes ISV-szoftvereket külön kell fizetniük.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>A felhasználó automatikusan felszámítja az ajánlat előfizetését?

A SaaS-ajánlatra való feliratkozáskor a felhasználó beleegyezett, hogy a Microsoft platformon keresztül fizet az SaaS-szolgáltatás fogyasztásának. A díjak azonban csak az ajánlat felhasználása után indulnak el. A felhasználónak meg kell adnia SaaS-ajánlatát, és meg kell erősítenie a fiók létrehozását az ajánlat megkezdése érdekében. Ezután értesíti a Microsoftot az ügyfél SaaS-előfizetés számlázásának megkezdéséről.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hogyan történik az értesítés, amikor egy felhasználó előfizet az SaaS-ajánlatra?

Az ajánlatra való feliratkozás után az Azure-felhasználó felderítheti és kezelheti az összes ajánlatát az Azure-ban. Alapértelmezés szerint az újonnan előfizetett SaaS-ajánlatok állapota **kiépítés, beteljesülés függőben**. Ebben az állapotban az Azure-felhasználót a rendszer a **fiók konfigurálására** szolgáló művelettel fogja kérni, hogy megkeresse a Azure Portal a SaaS-előfizetés kezelési felületét.

Amikor a felhasználó kiválasztja a **fiók konfigurálása** lehetőséget, a rendszer átirányítja az SaaS szolgáltatás webhelyére. A közzétevő az ajánlat közzétételének időpontjában konfigurálta az URL-címet. Ezt a lapot a közzétevő kezdőlapjának nevezzük. Az Azure-felhasználók az Azure-beli meglévő Azure Active Directory (Azure AD) hitelesítő adataik alapján jelentkeznek be az SaaS-kezdőlapra.

> [!IMPORTANT]
> Be kell jelentkeznie a vásárlás felhasználójának Azure Active Directory, egyszeri bejelentkezés (Azure AD SSO) használatával a [szabályzat](/legal/marketplace/certification-policies?context=/azure/marketplace/context/context)utasítása alapján. A `mail` Microsoft Graph API-ból beolvasott felhasználói erőforrás tulajdonsága az Azure ad-hez és a MSA tartozó kapcsolattartási adatokat adja meg `userPrincipalName` . Előfordulhat, hogy a "mail" mező üres az Azure AD-ben, és a felhasználó nem rendelkezik rögzített e-mail-címmel. Ha ez a helyzet, javasoljuk, hogy érzékelje, és kérjen meg egy kapcsolattartási e-mailt. Ez az egyetlen esélye annak, hogy kapcsolattartási e-mail-címet kapjon az ügyfélnek a beléptetési folyamat során vagy azt követően.

Ha az Azure-felhasználót átirányítják a kezdőlapra, a rendszer tokent ad hozzá a lekérdezési URL-címhez. Ez a jogkivonat rövid életű, és 24 óra időtartamra érvényes. Ezt követően észlelhető a jogkivonat jelenléte, és meghívhatja a Microsoft API-ját, hogy a tokenhez több környezet legyen társítva.

![Ügyfél-előfizetési folyamat](media/saas-metering-service-integration-flow-a.png)

Az SaaS-ajánlat életciklusában a Transact-forgatókönyvek kezelésére szolgáló API-szerződéssel kapcsolatos további információkért lásd: [SaaS-teljesítési API](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Honnan tudhatja meg, hogy a felhasználó milyen SaaS-előfizetést kínál az Azure-ban?

Az API-ra adott válasz az `Resolve` SaaS-előfizetéshez kapcsolódó ajánlatot és megtervezési információkat tartalmazza.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hogyan változtatható meg az Azure-felhasználó az ehhez az Azure-előfizetéshez társított terv?

* Az Azure-felhasználó a SaaS-előfizetéshez kapcsolódó tervet közvetlenül a SaaS-élményben vagy a Microsoft platformon módosíthatja.

* A konverziókat a számlázási ciklusban bármikor megteheti. A rendszer felszólítja, hogy fogadja el az összes konverziót, amely a nyugtát követően hatályba lép.

* Az előre fizetett csomag (**havi** vagy **éves**) díjszabása Pro-rated. A konverzió időpontjában kibocsátott bármely túllépés a következő számlán lesz felszámítva. Az új túllépések az új csomag alapján lesznek kibocsátva.

>[!Note]
>Ha nem szeretné bizonyos konverziós útvonalakat támogatni, tiltsa le a visszalépéseket.

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél egy csomagot módosít a SaaS-élményben:

![Ügyfél-díjcsomag változási folyamata](media/saas-metering-service-integration-flow-b.png)

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél egy tervet módosít a Microsoft online áruházban:

![Az ügyfél online áruházi tervének változási folyamata](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hogyan lehet az Azure-felhasználó lemondani az Azure-előfizetéshez társított csomagról?

Az Azure-felhasználók közvetlenül a SaaS-élményben vagy a Microsoft platformon keresztül leiratkozhat a megvásárolt SaaS-ajánlatból. A felhasználó lemondása után a következő számlázási időszak után már nem számítunk fel díjat.

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél leiratkozik az SaaS-ajánlatra a SaaS-élményben:

![Az ügyfél lemond a SaaS-élményben](media/saas-metering-service-integration-flow-d.png)

Az alábbi kódrészlet rögzíti a folyamatot, amikor az Azure-felhasználó leiratkozik a Microsoft Online áruházában:

![Az ügyfél lemond a Microsoft Online áruházában](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Következő lépések

[Marketplace metering service API-k](./marketplace-metering-service-apis.md)
