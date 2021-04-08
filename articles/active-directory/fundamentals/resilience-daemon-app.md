---
title: Növelje a hitelesítés és engedélyezés rugalmasságát a fejlesztés alatt álló Daemon-alkalmazásokban
titleSuffix: Microsoft identity platform
description: Útmutató a hitelesítés és engedélyezés rugalmasságának növeléséhez a Daemon alkalmazásban a Microsoft Identity platform használatával
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724841"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Növelje a hitelesítés és engedélyezés rugalmasságát a fejlesztés alatt álló Daemon-alkalmazásokban

Ez a cikk útmutatást nyújt arról, hogy a fejlesztők hogyan használhatják a Microsoft Identity platformot és Azure Active Directory a démoni alkalmazások rugalmasságának növelésére. Ide tartoznak a háttérben futó folyamatok, a szolgáltatások, a kiszolgálók és a kiszolgálói alkalmazások, valamint a felhasználók nélküli alkalmazások.

![Egy démon-alkalmazás, amely meghívja a Microsoft Identity-t](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Felügyelt identitások használata az Azure-erőforrásokhoz

A Microsoft Azureon futó Daemon-alkalmazásokat használó fejlesztők [felügyelt identitásokat használhatnak az Azure-erőforrásokhoz](../managed-identities-azure-resources/overview.md). A felügyelt identitások nem szükségesek a fejlesztők számára a titkok és a hitelesítő adatok kezeléséhez. A szolgáltatás javítja a rugalmasságot azáltal, hogy elkerüli a tanúsítvány lejárati hibáit, a rotációs hibákat vagy a megbízhatóságot. Emellett számos beépített funkcióval rendelkezik, amelyek kifejezetten a rugalmasság növelését szolgálják.

A felügyelt identitások hosszú élettartamú hozzáférési jogkivonatokat és információkat használnak a Microsoft Identity szolgáltatástól, hogy proaktív módon szerezzenek be új jogkivonatokat egy nagy időkereten belül, mielőtt a meglévő token lejár. Az alkalmazás továbbra is futtatható egy új jogkivonat beszerzésére tett kísérlet során.

A felügyelt identitások regionális végpontokkal is javítják a teljesítményt és a rugalmasságot a régión kívüli hibákkal szemben. A regionális végpontok segítségével minden forgalmat megtarthat egy földrajzi területen belül. Ha például az Azure-erőforrás WestUS2 van, az összes forgalom, beleértve a Microsoft Identity által generált forgalmat, WestUS2 marad. Ezzel kiküszöbölheti a lehetséges meghibásodási pontokat a szolgáltatás függőségeinek összevonásával.

## <a name="use-the-microsoft-authentication-library"></a>A Microsoft hitelesítési függvénytárának használata

A felügyelt identitásokat nem használó Daemon-alkalmazások fejlesztői használhatják a [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md)szolgáltatást, amely egyszerűvé teszi a hitelesítés és az engedélyezés megvalósítását, és automatikusan alkalmazza a rugalmasságra vonatkozó ajánlott eljárásokat. A MSAL egyszerűbbé teszi a szükséges ügyfél-hitelesítő adatok megadását. Előfordulhat például, hogy az alkalmazásnak nem kell megvalósítania JSON Web Token-érvényesítéseket tanúsítvány alapú hitelesítő adatok használatakor.

### <a name="use-microsoftidentityweb-for-net-developers"></a>A Microsoft. Identity. Web használata .NET-fejlesztőknek

A ASP.NET Core-alapú Daemon-alkalmazásokat fejlesztő fejlesztők használhatják a [Microsoft. Identity. Web](../develop/microsoft-identity-web.md) könyvtárat. Ez a könyvtár a MSAL-re épül, hogy a ASP.NET Core alkalmazások számára még egyszerűbb legyen az engedélyezés. Több [elosztott jogkivonat-gyorsítótárazási](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) stratégiát is tartalmaz a több régióban futtatható elosztott alkalmazások esetében.

## <a name="cache-and-store-tokens"></a>Gyorsítótár-és tárolási tokenek

Ha nem használja a MSAL-t a hitelesítés és az engedélyezés megvalósításához, végrehajthat néhány ajánlott eljárást a tokenek gyorsítótárazásához és tárolásához. A MSAL automatikusan végrehajtja és követi ezeket az ajánlott eljárásokat.

Egy alkalmazás jogkivonatokat vásárol az identitás-szolgáltatótól, hogy engedélyezze az alkalmazásnak a védett API-k meghívását. Ha az alkalmazás jogkivonatokat kap, a jogkivonatokat tartalmazó válasz "lejár \_ " tulajdonságot is tartalmaz, amely megadja az alkalmazásnak, hogy mennyi ideig kell gyorsítótárazni, és újra kell használni a tokent. Fontos, hogy az alkalmazások a \_ jogkivonat élettartamának megállapításához a "lejárat" tulajdonságot használják. Az alkalmazásnak soha nem kell megkísérelni egy API-hozzáférési jogkivonat dekódolását. A gyorsítótárazott jogkivonat használatával megelőzhető az alkalmazás és a Microsoft-identitás közötti szükségtelen forgalom. A felhasználó bejelentkezhet az alkalmazásba a jogkivonat élettartamának hosszára.

## <a name="properly-handle-service-responses"></a>Szolgáltatási válaszok megfelelő kezelése

Végül, míg az alkalmazásoknak az összes hibaüzenetet kezelnie kell, vannak olyan válaszok, amelyek hatással lehetnek a rugalmasságra. Ha az alkalmazás HTTP 429-hibakódot kap, túl sok kérést, a Microsoft Identity szabályozza a kérelmeket. Ha az alkalmazás továbbra is túl sok kérést hajt végre, továbbra is érvényben marad, hogy megakadályozza az alkalmazásnak a jogkivonatok fogadását. Az alkalmazásnak nem kell újra megadnia a tokent, amíg az idő (másodpercben) nem lesz az "újrapróbálkozás" válasz mezőjében. Az 429-es válasz fogadása gyakran arra utal, hogy az alkalmazás nem gyorsítótárazza és nem használja helyesen a jogkivonatokat. A fejlesztőknek át kell tekinteniük a tokenek gyorsítótárazásának és az alkalmazásban való újrafelhasználásának módját.

Ha egy alkalmazás HTTP-5xx választ kap, akkor az alkalmazás nem adhat meg gyors újrapróbálkozási ciklust. Ha van ilyen, az alkalmazásnak ugyanazt az "újrapróbálkozást" kell tennie, mint egy 429-válaszhoz. Ha a válasz nem "újrapróbálkozás utáni" fejlécet ad meg, javasoljuk, hogy az első újrapróbálkozás után legalább 5 másodperccel próbálkozzon újra.

Ha egy kérelem időtúllépése esetén az alkalmazások nem tudnak azonnal újrapróbálkozni. Hozzon létre egy exponenciális visszalépési újrapróbálkozást az első újrapróbálkozás után legalább 5 másodperccel a válasz után.

## <a name="next-steps"></a>Következő lépések

- [Rugalmasság kiépítése a bejelentkezési felhasználókat használó alkalmazásokba](resilience-client-app.md)
- [Hozzon létre rugalmasságot az identitás-és hozzáférés-kezelési infrastruktúrában](resilience-in-infrastructure.md)
- [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)