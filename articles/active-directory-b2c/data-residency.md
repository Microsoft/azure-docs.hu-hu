---
title: Regionális elérhetőség és adattárolási hely
titleSuffix: Azure AD B2C
description: A régió rendelkezésre állása, az adatok tartózkodási helye és a Azure Active Directory B2C előzetes verziójú Bérlővel kapcsolatos információk.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92309684"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: régió rendelkezésre állása & adattárolás

A régió rendelkezésre állása és az adattárolás két nagyon különböző fogalom, amelyek eltérő módon vonatkoznak az Azure többi részétől való Azure AD B2Cra. Ez a cikk ismerteti a két fogalom közötti különbségeket, és összehasonlítja, hogyan vonatkoznak az Azure-ra és a Azure AD B2Cra.

A Azure AD B2C **általánosan elérhető világszerte** az **Egyesült Államok, Európa vagy Ázsia és a csendes-óceáni térség** **adattárolási** lehetőségével.

A [régió elérhetősége](#region-availability) arra utal, hogy a szolgáltatás hol használható.

Az [adatrezidens](#data-residency) a felhasználói adattárolási helyekre utal.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Azure AD B2C világszerte elérhető az Azure-beli nyilvános felhőben.

Ez eltér a modelltől, amelyet a legtöbb egyéb Azure-szolgáltatás követ, ami általában a *rendelkezésre állást* és az *adattárolást* is igénybe vette. Ebből az Azure- [termékek régió](https://azure.microsoft.com/regions/services/) oldalán és a [Active Directory B2C árképzési kalkulátorban](https://azure.microsoft.com/pricing/details/active-directory-b2c/)is láthat példát.

## <a name="data-residency"></a>Adattárolási hely

A Azure AD B2C Egyesült Államok, Európa vagy a Ázsia és a Csendes-óceáni térség régióban tárolja a felhasználói adattípusokat.

Az adattárolást a [Azure ad B2C bérlő létrehozásakor](tutorial-create-tenant.md)kiválasztott ország/régió határozza meg:

![Képernyőfelvétel a bérlő létrehozása űrlapról, az ország vagy régió kiválasztása.](./media/data-residency/data-residency-b2c-tenant.png)

A következő országok/régiók adatai találhatók a **Egyesült Államokban** :

> Egyesült Államok (USA), Kanada (CA), Costa Rica (CR), Dominikai Köztársaság (DO), Salvador (SV), Guatemala (GT), Mexikó (MX), Panama (PA), Puerto Rico (PR) és Trinidad & Tobago (TT)

**Európában** az alábbi országok és régiók adatai találhatók:

> Algéria (DZ), Ausztria (AT), Azerbajdzsán (AZ), Bahrein (BH), Fehéroroszország (BY), Belgium (BE), Bulgária (BG), Horvátország (HR), Ciprus (CY), Cseh Köztársaság (CZ), Dánia (Dánia), Egyiptom (EG), Észtország (EE), Finnország (FT), Franciaország (FR), Németország (DE), Görögország (GR), Magyarország (HU), Izland (IS), Írország (IE), Izrael (IL), Olaszország (IT), Jordánia (JO), Kazahsztán (KZ), Kenya (KE), Kuvait (KW), Lettország (LV), Libanon (LB), Liechtenstein (LI), Litvánia (LT) , Luxembourg (LU), Észak-Macedónia (ML), Málta (MT), Montenegró (i), Marokkó (MA), Hollandia (NL), Nigéria (NG), Norvégia (NO), Omán (OM), Pakisztán (PK), Lengyelország (com), Portugália (PT), Katar (QA), Románia (RO), Oroszország (RU), Szaúd-Arábia (SA), Szerbia (RS), Szlovákia (SK), Szlovénia (ST), Dél-Afrika (ZA), Spanyolország (ES), Svédország (SE), Svájc (CH), Tunézia (TN), Törökország (TR), Ukrajna (UA), Egyesült Arab Emírségek (AE) és Egyesült Királyság (GB)

A következő országok/régiók esetében **Ázsia és a csendes-óceáni térség** az adat:

> Afganisztán (AF), Hongkong KKT (HK), India (IN), Indonézia (ID), Japán (JP), Korea (KR), Malajzia (MY), Fülöp-szigetek (PH), Szingapúr (SG), Srí Lanka (LK), Tajvan (TW) és Thaiföld (TH).

A következő országok/régiók a listához való felvételük folyamatban van. Egyelőre továbbra is használhatja a Azure AD B2Ct a fenti országok/régiók bármelyikének kiválasztásával.

> Argentína, Ausztrália, Brazília, Chile, Kolumbia, Ecuador, Irak, Új-Zéland, Paraguay, Peru, Uruguay és Venezuela.

## <a name="remote-profile-solution"></a>Távoli profil megoldása

Azure AD B2C [Egyéni szabályzatokkal](custom-policy-overview.md)integrálható a [REST API-szolgáltatásokkal](custom-policy-rest-api-intro.md), amelyek lehetővé teszik a felhasználói profilok tárolását és olvasását egy távoli adatbázisból (például egy marketing-ADATBÁZISból, egy CRM-rendszerből vagy bármely üzletági alkalmazásból).  
- A regisztrálási és a profil-szerkesztési folyamatok során Azure AD B2C meghívja az egyéni REST API, hogy megmaradjon a felhasználói profil a távoli adatforráshoz. A felhasználó hitelesítő adatait a rendszer Azure AD B2C könyvtárban tárolja. 
- Bejelentkezéskor a hitelesítő adatok helyi vagy közösségi fiókkal való ellenőrzése után Azure AD B2C meghívja a REST API, amely felhasználói elsődleges kulcsként (e-mail cím vagy felhasználói objectId) küldi el a felhasználó egyedi azonosítóját. A REST API beolvassa az adatokat a távoli adatbázisból, és visszaadja a felhasználói profilt.  

A regisztráció, a profil szerkesztése vagy a bejelentkezés befejezése után Azure AD B2C tartalmazza az alkalmazáshoz visszaadott hozzáférési jogkivonat felhasználói profilját. További információ: [Azure ad B2C távoli profil megoldás](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) a githubban.

## <a name="preview-tenant"></a>Bérlő előzetes verziója

Ha a B2C-bérlőt az Azure AD B2C's előzetes verziójának ideje alatt hozta létre, akkor valószínű, hogy a **bérlő típusa** az **előzetes verziójú bérlőt** mondja.

Ebben az esetben a bérlőt csak fejlesztési és tesztelési célokra kell használnia. Ne használjon előzetes verziójú bérlőt éles alkalmazásokhoz.

Egy előnézeti B2C-bérlőtől **nem érhető el áttelepítési útvonal** egy éles méretű B2C-bérlőre. Létre kell hoznia egy új B2C-bérlőt az éles alkalmazásokhoz.

Ismert problémák merültek fel, ha törli a B2C-bérlőt, és létrehoz egy éles méretű B2C-bérlőt ugyanazzal a tartománynévvel. *Egy másik tartománynévvel rendelkező, éles méretű B2C-bérlőt kell létrehoznia*.

![Képernyőkép a bérlői típusról, mint az előzetes verzió bérlője.](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy Azure ad B2C bérlőt](tutorial-create-tenant.md).
