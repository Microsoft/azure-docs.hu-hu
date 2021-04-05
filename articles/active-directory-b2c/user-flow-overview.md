---
title: Felhasználói folyamatok Azure Active Directory B2Cban | Microsoft Docs
titleSuffix: Azure AD B2C
description: További információ a Azure Active Directory B2C bővíthető házirend-keretrendszeréről és a különböző felhasználói folyamatok létrehozásáról.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840191"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Felhasználói folyamatok az Azure Active Directory B2C-ben

Hogy könnyebb legyen beállítani az alkalmazások leggyakoribb identitáskezelési feladatait, az Azure AD B2C Portal több előre meghatározott, konfigurálható szabályzatot tartalmaz; ezek az úgynevezett **felhasználói folyamatok**. A felhasználói folyamattal meghatározhatja, hogy a felhasználók hogyan használják az alkalmazást, amikor olyan műveleteket végeznek, mint például a bejelentkezés, a regisztráció, a profil szerkesztése vagy a jelszó alaphelyzetbe állítása. A felhasználói folyamatok esetében a következő képességeket szabályozhatja:

- Bejelentkezéshez használt fióktípus, például Facebook-vagy helyi fiókokhoz hasonló közösségi fiókok
- A fogyasztótól begyűjtött attribútumok, például Utónév, irányítószám és a cipő mérete
- Azure AD többtényezős hitelesítés
- A felhasználói felület testreszabása
- Információ arról, hogy az alkalmazás jogcímeket fogad jogkivonatként

A bérlőben számos különböző típusú felhasználói folyamatot hozhat létre, és igény szerint használhatja azokat az alkalmazásokban. A felhasználói folyamatok az alkalmazások között újra felhasználhatók. Ez a rugalmasság lehetővé teszi az identitások megadását és módosítását a kód minimális vagy semmilyen módosításával. Az alkalmazás egy szabványos HTTP-hitelesítési kérelem használatával indítja el a felhasználói folyamatot, amely tartalmazza a felhasználói folyamat paraméterét. Egy testreszabott [jogkivonat](tokens-overview.md) érkezik válaszként.

Az alábbi példák a "p" lekérdezési karakterlánc paramétert mutatják be, amely meghatározza a használni kívánt felhasználói folyamatot:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Felhasználói folyamatok verziói

Azure AD B2C több típusú felhasználói folyamatot tartalmaz:

- **Regisztráció és bejelentkezés** – kezeli a regisztrációs és a bejelentkezési élményeket egyetlen konfigurációval. A felhasználók a környezettől függően a megfelelő útvonalat vezetik le. Emellett külön **regisztrációs** vagy **bejelentkezési** felhasználói folyamatokat is tartalmaz. A kombinált regisztráció és a bejelentkezési felhasználói folyamat azonban általában ajánlott.
- **Profil szerkesztése** – lehetővé teszi a felhasználók számára a profil adatainak szerkesztését.
- **Jelszó alaphelyzetbe állítása** – beállíthatja, hogy a felhasználók hogyan állíthatják alaphelyzetbe a jelszavukat.

A legtöbb felhasználói folyamat esetében a **javasolt** verzió és a **standard** verzió is szerepel. Részletekért lásd: [felhasználói folyamatok verziói](user-flow-versions.md).

> [!IMPORTANT]
> Ha korábban már használta a felhasználói folyamatokat Azure AD B2C előtt, megfigyelheti, hogy módosítottuk a felhasználói folyamatok verzióinak hivatkozását. Korábban V1 (az éles környezetben való használatra kész) verziókat, valamint V1.1 és V2 (előzetes) verziókat kínáltunk. Most összevontuk a felhasználói folyamatokat két verzióra:
>
>- Az **ajánlott** felhasználói folyamatok a felhasználói folyamatok új előzetes verzióit jelentik. Alaposan tesztelték és egyesítik az örökölt **v2** és a **v 1.1** verzió összes funkcióját. Az új javasolt felhasználói folyamatok továbbra is megmaradnak és frissülnek. Ha áthelyezi ezeket az új ajánlott felhasználói folyamatokat, hozzáférhet a kiadott új funkciókhoz.
>- A korábban **v1**-ként ismert **általános** felhasználói folyamatok általánosan elérhetők, a termelésre kész felhasználói folyamatok. Ha a felhasználói folyamatok kritikus fontosságúak, és a nagymértékben stabil verzióktól függenek, továbbra is használhatja a normál felhasználói folyamatokat, és felismerheti, hogy ezek a verziók nem lesznek karbantartva és frissítve.
>
>Az összes korábbi előzetes verziójú felhasználói folyamat (V 1.1 és v2) a 2021-es **augusztus 1-től** az elavult elérési úton van. Ahol csak lehetséges, javasoljuk, hogy a lehető leghamarabb [váltson az új **javasolt** felhasználói folyamatokra](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) , így mindig kihasználhatja a legújabb funkciókat és frissítéseket.

## <a name="linking-user-flows"></a>Felhasználói folyamatok összekapcsolása

A helyi fiókkal rendelkező **regisztrációs vagy bejelentkezési** felhasználói folyamat tartalmaz egy **elfelejtett jelszót?** hivatkozást a felület első oldalán. Ha erre a hivatkozásra kattint, nem indítja el automatikusan a jelszó-visszaállítás felhasználói folyamatát.

Ehelyett a hibakódot a `AADB2C90118` rendszer visszaadja az alkalmazásnak. Az alkalmazásnak ezt a hibakódot egy adott felhasználói folyamat futtatásával kell kezelnie, amely alaphelyzetbe állítja a jelszót. Ha példát szeretne látni, tekintse meg az [egyszerű ASP.net mintát](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , amely bemutatja a felhasználói folyamatok összekapcsolását.

## <a name="email-address-storage"></a>E-mail-cím tárterülete

Egy e-mail-cím a felhasználói folyamat részeként is megkövetelhető. Ha a felhasználó egy közösségi identitás-szolgáltatóval végzi a hitelesítést, az e-mail-cím a **otherMails** tulajdonságban tárolódik. Ha egy helyi fiók egy Felhasználónév alapján van kiválasztva, akkor az e-mail-cím egy erős hitelesítési részlet tulajdonságban tárolódik. Ha egy helyi fiók egy e-mail-címen alapul, akkor az e-mail-cím a **signInNames** tulajdonságban tárolódik.

Az e-mail-cím nem garantálható az ilyen esetekben. A bérlői rendszergazda letilthatja az e-mailek ellenőrzését a helyi fiókok alapszintű házirendjeiben. Még ha az e-mail cím ellenőrzése is engedélyezve van, a rendszer nem ellenőrzi a címeket, ha azok egy közösségi identitás-szolgáltatótól származnak, és nem változtak.

A Microsoft Graph API-n keresztül csak a **otherMails** és a **signInNames** tulajdonságok jelennek meg. Az erős hitelesítés részletei tulajdonságban szereplő e-mail-cím nem érhető el.

## <a name="next-steps"></a>Következő lépések

Az ajánlott felhasználói folyamatok létrehozásához kövesse az [oktatóanyag: felhasználói folyamat létrehozása](tutorial-create-user-flows.md)című témakör utasításait.
