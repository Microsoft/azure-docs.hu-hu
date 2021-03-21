---
title: Az Azure AD B2C API-összekötői
description: A Azure Active Directory (Azure AD) API-összekötők használatával testreszabhatja és kiterjesztheti a regisztrációs felhasználói folyamatokat webes API-k használatával.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611521"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>API-összekötők használata a regisztrációs felhasználói folyamatok testreszabásához és kiterjesztéséhez

> [!IMPORTANT]
> A regisztrációhoz használható API-összekötők a Azure AD B2C nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Áttekintés 
Fejlesztőként vagy rendszergazdaként az API-összekötők használatával integrálhatja a regisztrációs felhasználói folyamatokat a webes API-kkal a regisztrációs élmény testreszabásához és a külső rendszerekkel való integrációhoz. Az API-összekötők esetében például a következőket teheti:

- **Felhasználói bemeneti adatok érvényesítése**. A helytelenül formázott vagy érvénytelen felhasználói adatértékek ellenőrzése. Ellenőrizheti például, hogy a felhasználó által megadott adatok egy külső adattáron vagy a megengedett értékek listáján lévő meglévő adatokon keresztül érvényesíthetők-e. Ha érvénytelen, megkérheti a felhasználót, hogy adjon meg érvényes adatmennyiséget, vagy tiltsa le a felhasználót a regisztrációs folyamat folytatásához.
- **Integrálás egyéni jóváhagyási munkafolyamattal**. Kapcsolódás egyéni jóváhagyási rendszerhez a fiókok létrehozásának kezeléséhez és korlátozásához.
- **Felhasználói attribútumok felülírása**. Formázza vagy rendeljen hozzá egy értéket a felhasználótól gyűjtött attribútumhoz. Ha például egy felhasználó az első nevet adja meg az összes kisbetűs vagy az összes nagybetűvel, a nevet csak az első nagybetűvel formázhatja. 
- **Személyazonosság-ellenőrzés végrehajtása**. A személyazonosság-ellenőrző szolgáltatással további biztonsági szintet adhat hozzá a fiókok létrehozásával kapcsolatos döntésekhez.
- **Egyéni üzleti logikát futtathat**. Leküldéses értesítések küldéséhez, a vállalati adatbázisok frissítéséhez, az engedélyek kezeléséhez, a naplózási adatbázisokhoz és egyéb egyéni műveletek elvégzéséhez aktiválhatja az alárendelt eseményeket a felhőalapú rendszerekben.

Az API-összekötők a HTTP-végpont URL-címének és az API-hívás hitelesítésének definiálásával Azure Active Directory biztosítanak az API-végpont meghívásához szükséges információkkal. Az API-összekötők konfigurálása után engedélyezheti azt egy adott lépéshez a felhasználói folyamatokban. Ha a felhasználó eléri ezt a lépést a regisztrációs folyamat során, az API-összekötőt meghívja, és HTTP POST-kérésként megvalósul az API-hoz, a felhasználói adatokat ("jogcímeket") pedig egy JSON-törzsben kulcs-érték párokként küldi el. Az API-válasz hatással lehet a felhasználói folyamat végrehajtására. Az API-válasz például blokkolhatja egy felhasználó regisztrációját, megkérheti a felhasználót, hogy írja be újra az adatokat, vagy írja felül a felhasználói attribútumok felülírását és hozzáfűzését.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Az API-összekötők felhasználói folyamatokban való engedélyezése

A felhasználói folyamatoknak két helye van, ahol engedélyezheti az API-összekötőt:

- Az identitás-szolgáltatóval való bejelentkezés után
- A felhasználó létrehozása előtt

> [!IMPORTANT]
> Mindkét esetben az API-összekötőket a rendszer a felhasználói **regisztráció** során hívja meg, nem pedig bejelentkezik.

### <a name="after-signing-in-with-an-identity-provider"></a>Az identitás-szolgáltatóval való bejelentkezés után

A regisztrálási folyamat ezen lépésében szereplő API-összekötőt azonnal meghívja a rendszer, miután a felhasználó hitelesítve lett az identitás-szolgáltatónál (például Google, Facebook, & Azure AD). Ez a lépés megelőzi az ***attribútum-gyűjtemény lapot***, amely a felhasználó számára a felhasználói attribútumok összegyűjtésére bemutatott űrlap. Ez a lépés nem kerül meghívásra, ha egy felhasználó helyi fiókkal van regisztrálva. Az alábbi példák az API-összekötők olyan forgatókönyveit ismertetik, amelyeket a következő lépésben engedélyezhet:

- A felhasználó által megadott e-mail-vagy összevont identitás használatával kereshet jogcímeket egy meglévő rendszeren. Adja vissza ezeket a jogcímeket a meglévő rendszerből, töltse ki előre az attribútum-gyűjtemény lapot, és tegye elérhetővé őket a tokenben való visszatéréshez.
- A közösségi identitáson alapuló engedélyezési vagy letiltási lista implementálása.

### <a name="before-creating-the-user"></a>A felhasználó létrehozása előtt

A regisztrációs folyamat ezen lépésében található API-összekötőt a rendszer az attribútum-gyűjtemény lap után hívja meg, ha van ilyen. Ezt a lépést mindig a rendszer a felhasználói fiók létrehozása előtt hívja meg. A következő példák olyan forgatókönyveket mutatnak be, amelyeket a regisztráció során lehet engedélyezni:

- Érvényesítse a felhasználói bemeneti adatokat, és kérje meg a felhasználót, hogy küldje el újra az adatokat.
- Felhasználói regisztráció letiltása a felhasználó által megadott adatbevitel alapján.
- Személyazonosság-ellenőrzés végrehajtása.
- Külső rendszerek lekérdezése a felhasználóról az alkalmazási jogkivonatban visszaadott vagy az Azure AD-ben tárolt meglévő információkhoz.


## <a name="next-steps"></a>Következő lépések
- Útmutató [API-összekötők felhasználói folyamathoz való hozzáadásához](add-api-connector.md)
- Ismerkedjen meg a [mintákkal](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->