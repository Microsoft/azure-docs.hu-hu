---
title: Migrálás a Microsoft hitelesítési tárba (MSAL)
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library (MSAL) és az Azure AD Authentication Library (ADAL) közötti különbséget, valamint a MSAL-re való áttelepítést.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f32c81d4d247eb6c819adf5015cf36c846f4e83b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218230"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Alkalmazások migrálása a Microsoft hitelesítési tárba (MSAL)

Számos fejlesztő a Azure Active Directory Authentication Library (ADAL) használatával épített és telepített alkalmazásokat. A Microsoft Authentication Library (MSAL) használatát javasoljuk az Azure AD-entitások hitelesítéséhez és engedélyezéséhez.

A ADAL helyett a MSAL használatával:

- Az identitások szélesebb körét is hitelesítheti:
  - Azure AD-identitások
  - Microsoft-fiókok
  - Közösségi és helyi fiókok Azure AD B2C használatával
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- Az alkalmazás engedélyezheti a növekményes hozzájárulásukat.
- A feltételes hozzáférés támogatása egyszerűbb.
- Az innováció előnyeit élvezheti. Mivel az összes Microsoft fejlesztési erőfeszítés most már a MSAL összpontosít, a ADAL-ben nem lesznek új funkciók implementálva.

A **MSAL mostantól az ajánlott hitelesítési függvénytár a Microsoft Identity platformmal való használatra**.

## <a name="migration-guidance"></a>Útmutató a migráláshoz

A következő cikkek segítséget nyújtanak a MSAL való Migrálás során:

- [Migrálás MSAL.Androidra](migrate-android-adal-msal.md)
- [Migrálás MSAL.iOS vagy macOS rendszerre](migrate-objc-adal-msal.md)
- [Migrálás MSAL Javába](migrate-adal-msal-java.md)
- [Migrálás MSAL.js-re](msal-compare-msal-js-and-adal-js.md)
- [Migrálás MSAL.NET-re](msal-net-migration.md)
- [Migrálás MSAL Pythonba](migrate-python-adal-msal.md)
- [Xamarin-alkalmazások migrálása közvetítők használatával MSAL.NET rendszerre](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

__K: a ADAL elavult?__  
V: Igen. 2020. június 30-ig a továbbiakban nem fogunk új funkciókat hozzáadni a ADAL-hez. A kritikus biztonsági javításokat továbbra is a ADAL, 2022. június 30-ig fogjuk hozzáadni. Ezen időpont után a ADAL-t használó alkalmazások továbbra is működőképesek maradnak, de javasoljuk, hogy a legújabb funkciók kihasználásához és a biztonság fenntartása érdekében ajánlott a MSAL-re való frissítés.

__K: a meglévő ADAL-alkalmazások működése leáll?__  
V: Nem. A meglévő alkalmazásai módosítás nélkül továbbra is működőképesek maradnak. Ha azt tervezi, hogy 2022 június 30-ig tart, érdemes frissítenie az alkalmazásokat a MSAL, hogy azok biztonságosak maradjanak, de a MSAL való Migrálás nem szükséges a meglévő funkciók fenntartásához.

__K: Hogyan tudni, hogy mely alkalmazások használják a ADAL-t?__  
A: Ha az alkalmazás forráskódját használja, a fenti áttelepítési útmutatók alapján meghatározhatja, hogy az alkalmazás melyik függvénytárat és hogyan telepítse át az MSAL-re. Ha egy ISV-t társít, javasoljuk, hogy a MSAL való áttelepítésének megismeréséhez közvetlenül érje el azokat.

__K: Miért érdemes befektetni a MSAL-re?__  
A: a MSAL olyan új szolgáltatásokat tartalmaz, amelyek nem a ADAL, beleértve a növekményes belefoglalást, az egyszeri bejelentkezést és a jogkivonat-gyorsítótárazási kezelést. A ADAL-től eltérően a MSAL a 2022. június 30-ig továbbra is a biztonsági javításokat fogja kapni. [További információ](msal-overview.md).

__K: a Microsoft frissíteni fogja a saját alkalmazásait a MSAL?__  
Igen. A Microsoft folyamatban van az alkalmazások MSAL általi áttelepítésének folyamata, így biztosítva, hogy a MSAL folyamatos biztonsági és szolgáltatás-fejlesztései hasznosak legyenek.

__K: olyan eszközt fog kibocsátani, amely segít áthelyezni az alkalmazásokat a ADAL-ből a MSAL-be?__  
V: Nem. A kódtárak közötti különbségek szükségessé teszik az erőforrások kiépítését az eszköz fejlesztésére és karbantartására, amelyeket egyébként a MSAL javításával kellene elkölteni. Azonban biztosítjuk az előző áttelepítési útmutatókat, amelyekkel elvégezheti a szükséges módosításokat az alkalmazásban.

__K: Hogyan működik a MSAL a AD FS?__  
A: a MSAL.NET bizonyos forgatókönyveket támogat a AD FS 2019-es hitelesítéshez. Ha az alkalmazásnak a jogkivonatokat közvetlenül a AD FS korábbi verziójából kell beszerezni, akkor továbbra is a ADAL kell maradnia. [További információ](msal-net-adfs-support.md).

__K: Hogyan segítséget nyújt az alkalmazás áttelepítéséhez?__  
Válasz: Tekintse meg a jelen cikk [áttelepítési útmutató](#migration-guidance) című szakaszát. Ha az alkalmazás platformjának elolvasása után további kérdései vannak, akkor tegye közzé a [Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) a címkével, `[azure-ad-adal-deprecation]` vagy nyisson meg egy problémát a könyvtár GitHub-tárházában. Az egyes könyvtárak tárházára mutató hivatkozásokat az MSAL áttekintését ismertető cikk [nyelvek és keretrendszerek](msal-overview.md#languages-and-frameworks) című szakaszában találja.

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások frissítése a Microsoft Authentication Library és a Microsoft Graph API használatára](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [A Microsoft Identity platform áttekintése](v2-overview.md)
- [Tekintse át a MSAL-kód mintáit](sample-v2-code.md)
