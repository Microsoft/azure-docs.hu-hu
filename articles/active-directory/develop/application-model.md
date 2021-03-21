---
title: Alkalmazás modellje | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795656"
---
# <a name="application-model"></a>Alkalmazásmodell

Az alkalmazások maguk is bejelentkezhetnek a felhasználókba, vagy delegálhatja a bejelentkezést egy identitás-szolgáltatóhoz. Ez a cikk az alkalmazások Microsoft Identity platformmal való regisztrálásához szükséges lépéseket ismerteti.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Ahhoz, hogy egy identitás-szolgáltató tudja, hogy egy felhasználó hozzáfér egy adott alkalmazáshoz, a felhasználónak és az alkalmazásnak is regisztrálva kell lennie az identitás-szolgáltatónál. Ha az alkalmazást Azure Active Directory (Azure AD) regisztrálja, az alkalmazáshoz olyan identitás-konfigurációt biztosít, amely lehetővé teszi, hogy integrálható legyen a Microsoft Identity platformmal. Az alkalmazás regisztrálása a következőket is lehetővé teszi:

* Szabja testre az alkalmazás arculatát a Bejelentkezés párbeszédpanelen. Ez a arculat azért fontos, mert a bejelentkezés az első olyan felhasználói élmény, amellyel a felhasználó az alkalmazással fog rendelkezni.
* Döntse el, hogy engedélyezni szeretné-e a felhasználók számára, hogy csak akkor jelentkezzenek be, ha a szervezethez tartoznak. Ezt az architektúrát egyetlen bérlős alkalmazásnak nevezzük. Vagy lehetővé teheti a felhasználók számára, hogy bármilyen munkahelyi vagy iskolai fiókkal jelentkezzenek be, amelyet több-bérlős alkalmazásnak nevezünk. Személyes Microsoft-fiókokat vagy közösségi fiókot is engedélyezhet a LinkedIn, a Google és egyebek között.
* Hatókör-engedélyek kérése. Kérheti például a "user. Read" hatókört, amely engedélyt ad a bejelentkezett felhasználó profiljának olvasásához.
* Adja meg a webes API-hoz való hozzáférést meghatározó hatóköröket. Általában, amikor egy alkalmazás szeretne hozzáférni az API-hoz, engedélyt kell kérnie az Ön által meghatározott hatókörökre.
* Ossza meg a titkos kódot a Microsoft Identity platformmal, amely igazolja az alkalmazás identitását. A titkos kulcs használata abban az esetben fontos, ha az alkalmazás bizalmas ügyfélalkalmazás. A bizalmas ügyfélalkalmazások olyan alkalmazások, amelyek biztonságosan tárolhatják a hitelesítő adatokat. A hitelesítő adatok tárolásához megbízható háttér-kiszolgáló szükséges.

Az alkalmazás regisztrálása után a rendszer egyedi azonosítót kap, amelyet a Microsoft Identity platformmal oszt meg, amikor jogkivonatokat kér. Ha az alkalmazás egy [bizalmas ügyfélalkalmazás](developer-glossary.md#client-application), akkor a titkos kulcsot vagy a nyilvános kulcsot is megoszthatja attól függően, hogy a rendszer a tanúsítványokat és a titkokat használta-e.

A Microsoft Identity platform az alkalmazásokat olyan modell használatával jelöli, amely két fő funkciót teljesít:

* Azonosítsa az alkalmazást az általa támogatott hitelesítési protokollok alapján.
* Adja meg a hitelesítéshez szükséges összes azonosítót, URL-címet, titkot és kapcsolódó információt.

A Microsoft Identity platform:

* A futtatáskor a hitelesítés támogatásához szükséges összes adattal rendelkezik.
* Az összes adat megtartásával döntheti el, hogy az alkalmazás milyen erőforrásokhoz férhet hozzá, és milyen esetekben kell teljesítenie az adott kérést.
* Infrastruktúrát biztosít az alkalmazások kiépítésének megvalósításához az alkalmazás fejlesztői bérlője és bármely más Azure AD-bérlő között.
* Kezeli a felhasználói hozzájárulásukat a jogkivonat-kérelmek ideje alatt, és megkönnyíti a bérlők közötti alkalmazások dinamikus üzembe helyezését.

A *jóváhagyás* az a folyamat, amelynek során az erőforrás-tulajdonos engedélyt ad egy ügyfélalkalmazás számára a védett erőforrások eléréséhez, az adott engedélyek alatt az erőforrás tulajdonosának nevében. A Microsoft Identity platform a következőket teszi lehetővé:

* A felhasználók és a rendszergazdák a nevükben lévő erőforrások eléréséhez az alkalmazáshoz való hozzáférést dinamikusan biztosítanak vagy megtagadják.
* A rendszergazdák végső soron eldönthetik, hogy mely alkalmazások engedélyezettek, és mely felhasználók használhatnak bizonyos alkalmazásokat, és hogyan férhetnek hozzá a címtár erőforrásaihoz.

## <a name="multi-tenant-apps"></a>Több-bérlős alkalmazások

A Microsoft Identity platformon az [Application Object](developer-glossary.md#application-object) egy alkalmazást ír le. A központi telepítés ideje alatt a Microsoft Identity platform az Application objektumot használja tervrajzként egy [egyszerű szolgáltatásnév](developer-glossary.md#service-principal-object)létrehozásához, amely egy adott alkalmazás konkrét példányát jelöli a címtárban vagy a bérlőn belül. Az egyszerű szolgáltatásnév azt határozza meg, hogy az alkalmazás mit tud valójában egy adott címtárban, ki használhatja azt, milyen erőforrásokhoz férhet hozzá, és így tovább. A Microsoft Identity platform egy egyszerű szolgáltatásnevet hoz létre egy alkalmazás-objektumból a [beleegyező](developer-glossary.md#consent)módon.

Az alábbi ábrán egy egyszerűsített Microsoft Identity platform kiépítési folyamata látható. Két bérlőt mutat be: *A* és *B*.

* A *bérlő* az alkalmazás tulajdonosa.
* A *B bérlő* az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.

![Diagram, amely egy egyszerűsített kiépítési folyamatot mutat be beleegyezett.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba. Az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. A Microsoft Identity platform az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben.
1. A felhasználó megkapja a kért jogkivonatot.

Ezt a folyamatot több bérlő esetében is megismételheti. Az A bérlő megőrzi az alkalmazás tervét (Application Object). Az összes többi bérlő felhasználói és rendszergazdái, akikkel az alkalmazás beleegyezik, folyamatosan szabályozhatja, hogy az alkalmazás milyen műveleteket végezhet el az egyes bérlők megfelelő egyszerű szolgáltatásán keresztül. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Következő lépések

A Microsoft Identity platform hitelesítésével és engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* A hitelesítés és az engedélyezés alapvető fogalmait a [hitelesítés és engedélyezés](authentication-vs-authorization.md)című témakör ismerteti.
* A következő témakörből megtudhatja, hogyan használhatók a hozzáférési tokenek, a frissítési tokenek és az azonosító tokenek a hitelesítés és az engedélyezés során: [biztonsági jogkivonatok](security-tokens.md).
* A webes, asztali és mobil alkalmazások bejelentkezési folyamatával kapcsolatos információkért lásd: [az alkalmazás bejelentkezési folyamata](app-sign-in-flow.md).

Az alkalmazás modelljével kapcsolatos további információkért tekintse meg a következő cikkeket:

* A Microsoft Identity platformon futó alkalmazás-és egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [how és Why alkalmazások hozzáadása az Azure ad](active-directory-how-applications-are-added.md)-hez.
* Az egybérlős alkalmazásokkal és a több-bérlős alkalmazásokkal kapcsolatos további információkért tekintse meg a [bérleti Azure Active Directory](single-and-multi-tenant-apps.md)című témakört.
* További információ arról, hogy az Azure AD hogyan nyújt Azure Active Directory B2Ct, hogy a szervezetek a felhasználók, jellemzően az ügyfelek számára a Google-fiókkal való bejelentkezést, a [Azure Active Directory B2C dokumentációját](../../active-directory-b2c/index.yml)használják.