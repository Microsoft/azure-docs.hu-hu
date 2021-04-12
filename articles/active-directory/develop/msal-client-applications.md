---
title: Nyilvános és bizalmas ügyfélalkalmazások (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a nyilvános ügyfél és a bizalmas ügyfélalkalmazások használatát a Microsoft Authentication Library (MSAL) alkalmazásban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063756"
---
# <a name="public-client-and-confidential-client-applications"></a>Nyilvános ügyfél és bizalmas ügyfélalkalmazások
A Microsoft Authentication Library (MSAL) két típusú ügyfelet határoz meg: a nyilvános ügyfeleket és a bizalmas ügyfeleket. A két ügyfél típusát úgy különböztetik meg, hogy az engedélyezési kiszolgálóval biztonságosan hitelesítik magukat, és megőrzik az ügyfél hitelesítő adataik titkosságát. Ezzel szemben az Azure AD Authentication Library (ADAL) az úgynevezett *hitelesítési környezetet* használja (amely az Azure ad-vel való kapcsolódás).

- A **bizalmas ügyfélalkalmazások** olyan alkalmazások, amelyek kiszolgálókon futnak (webalkalmazások, web API-alkalmazások vagy akár Service/Daemon-alkalmazások). A rendszer nehéznek tekinti a hozzáférést, és emiatt képes megtartani az alkalmazás titkos kulcsát. A bizalmas ügyfelek a konfigurációs idő titkát is tárolhatják. Az ügyfél minden példánya külön konfigurációval rendelkezik (beleértve az ügyfél-azonosítót és az ügyfél titkos kulcsát). Ezek az értékek nehézek a végfelhasználók kinyeréséhez. A webalkalmazás a leggyakoribb bizalmas ügyfél. Az ügyfél-azonosító a webböngészőn keresztül érhető el, de a titkos kulcsot csak a hátsó csatornán át kell adni, és soha nem kerül közvetlenül elérhetővé.

    Bizalmas ügyfélalkalmazások: <BR>
    ![Web App ](media/msal-client-applications/web-app.png) ![ web API ](media/msal-client-applications/web-api.png) ![ démon/szolgáltatás](media/msal-client-applications/daemon-service.png)

- A **nyilvános ügyfélalkalmazások** olyan alkalmazások, amelyek az eszközökön vagy asztali számítógépeken, vagy egy böngészőben futnak. Nem megbízhatók az alkalmazás titkainak biztonságos megőrzése érdekében, így csak a felhasználó nevében férnek hozzá a webes API-khoz. (Csak a nyilvános ügyfelek folyamatait támogatják.) A nyilvános ügyfelek nem rendelkezhetnek a konfigurációs idő titkával, így nem rendelkeznek az ügyfél titkos kulcsaival.

    Nyilvános ügyfélalkalmazások: <BR>
    ![Asztali alkalmazás ](media/msal-client-applications/desktop-app.png) ![ böngészővel nem rendelkező API ](media/msal-client-applications/browserless-app.png) ![ Mobile-alkalmazás](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js a nyilvános és a bizalmas ügyfélalkalmazások elkülönítése nem történik meg.  A MSAL.js a felhasználói ügynökön alapuló alkalmazásokként, a nyilvános ügyfeleknél, amelyekben az ügyfél kódját hajtja végre egy felhasználói ügynökben, például egy böngészőben. Ezek az ügyfelek nem tárolják a titkos kulcsokat, mert a böngésző környezete nyíltan elérhető.

## <a name="comparing-the-client-types"></a>Az ügyfél típusának összehasonlítása
Íme néhány hasonlóság és különbség a nyilvános ügyfél és a bizalmas ügyfélalkalmazások számára:

- Mindkét fajta alkalmazás egy felhasználói jogkivonat-gyorsítótárat tart fenn, és a tokent csendesen is beszerezheti (ha a jogkivonat már szerepel a jogkivonat-gyorsítótárban). A bizalmas ügyfélalkalmazások az alkalmazáshoz tartozó jogkivonatok esetében is rendelkeznek alkalmazás-jogkivonat-gyorsítótárral.
- Mindkét típusú alkalmazás kezeli a felhasználói fiókokat, és lekérhet egy fiókot a felhasználói jogkivonat gyorsítótárából, beszerezhet egy fiókot az azonosítóból, vagy eltávolíthatja a fiókot.
- A nyilvános ügyfélalkalmazások négyféle módon szerzik be a tokeneket (négy hitelesítési folyamat). A bizalmas ügyfélalkalmazások háromféle módon szerzik be a jogkivonatot (és az identitás-szolgáltatói végpont URL-címének kiszámításának egyik módját). További információ: [tokenek beszerzése](msal-acquire-cache-tokens.md).

Ha már használta a ADAL-t, észreveheti, hogy a ADAL hitelesítési környezetével ellentétben a MSAL (más néven az alkalmazás- *azonosító* vagy az alkalmazás- *azonosító*) az alkalmazás felépítésekor a rendszer egyszer átadja az ügyfél-azonosítót. Nem kell újra átadni, ha az alkalmazás jogkivonatot vásárol. Ez mind a nyilvános, mind a bizalmas ügyfélalkalmazások esetében igaz. A bizalmas ügyfélalkalmazások konstruktorai az ügyfél hitelesítő adatait is megkapják: az identitás-szolgáltatóval megosztott titkos kulcsot.

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg a következőkkel:
- [Ügyfélalkalmazás konfigurációs beállításai](msal-client-application-configuration.md)
- [Ügyfélalkalmazások példányainak MSAL.NET használatával](msal-net-initializing-client-applications.md)
- [Ügyfélalkalmazások példányainak MSAL.jshasználatával történő példánya ](msal-js-initializing-client-applications.md)
