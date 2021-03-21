---
title: Az Azure AD-alkalmazáshoz való engedélyezési tapasztalatok
titleSuffix: Microsoft identity platform
description: 'További információ az Azure AD-beli beleegyező élményekről: az Azure AD-alkalmazások kezelésekor és fejlesztésekor használható'
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c4a4af81c6a216119ae2e1b0221c06ddc349452f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92478133"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése

További információ az Azure Active Directory (Azure AD) alkalmazás-engedélyezési felhasználói élményről. Így intelligens módon kezelheti a szervezete alkalmazásait és/vagy fejlesztheti az alkalmazásokat, és így zökkenőmentesen megadhatja a felhasználói élményt.

## <a name="consent-and-permissions"></a>Hozzájárulás és engedélyek

A hozzájárulás egy olyan felhasználó, aki engedélyt ad egy alkalmazásnak a védett erőforrások elérésére a nevükben. A rendszergazda vagy a felhasználó megkérheti, hogy engedélyezze a hozzáférést a szervezethez vagy az egyes adatszolgáltatásokhoz.

A jogosultság megadásának tényleges felhasználói élménye a felhasználó bérlője, a felhasználó jogosultsági köre (vagy szerepkör), valamint az ügyfélalkalmazás által kért [engedélyek](../azuread-dev/v1-permissions-consent.md) típusa szerint változhat. Ez azt jelenti, hogy az alkalmazás-fejlesztőknek és a bérlői rendszergazdáknak van némi hozzáférésük a beleegyező felhasználói élményhez A rendszergazdák rugalmasan állíthatják be és letilthatják a szabályzatokat a bérlőn vagy az alkalmazáson a bérlőn való részvételi élmény szabályozása érdekében. Az alkalmazások fejlesztői megadhatják, hogy milyen típusú engedélyekre van szükség, és ha a felhasználókat a felhasználói beleegyező folyamaton vagy a rendszergazdai beleegyező folyamaton keresztül szeretnék irányítani.

- A **felhasználói hozzájárulás folyamata** az, amikor egy alkalmazás fejlesztője a felhasználókat az engedélyezési végpontra irányítja, hogy csak az aktuális felhasználó belefoglalását jegyezze fel.
- A rendszergazdai belefoglalási **folyamat** az, amikor egy alkalmazás fejlesztője a felhasználókat a rendszergazdai belefoglalási végpontra irányítja azzal a szándékkal, hogy rögzítse a teljes bérlőhöz való hozzájárulásukat. Annak biztosítása érdekében, hogy a rendszergazdai jogosultságok folyamata megfelelően működjön, az alkalmazás-fejlesztőknek az alkalmazás jegyzékfájljában szereplő összes engedélyt listáznia kell `RequiredResourceAccess` . További információ: [Application manifest](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>A hozzájárulási kérés építőelemei

A beleegyezés kérése úgy van kialakítva, hogy a felhasználók elegendő információval rendelkezzenek annak megállapításához, hogy megbízhatók-e az ügyfélalkalmazás a védett erőforrások nevében való eléréséhez. Az építőelemek megismerése segíti a felhasználókat abban, hogy jobban tájékozott döntéseket hozhassanak, így a fejlesztők jobb felhasználói élményt hozhatnak létre.

A következő diagram és táblázat a hozzájárulási kérés építőelemeit ismerteti.

![A hozzájárulási kérés építőelemei](./media/application-consent-experience/consent_prompt.png)

| # | Összetevő | Cél |
| ----- | ----- | ----- |
| 1 | Felhasználói azonosító | Ez az azonosító azt a felhasználót jelöli, akit az ügyfélalkalmazás a következő nevében a védett erőforrásokhoz való hozzáférésre kéri. |
| 2 | Cím | A cím változása attól függően változik, hogy a felhasználók a felhasználói vagy a rendszergazdai beleegyező folyamaton keresztül mennek keresztül. A felhasználói beleegyező folyamatokban a cím "engedély kérése", míg a rendszergazda beleegyező folyamatában a cím egy további, "elfogadás a szervezet számára" sort fog tartalmazni. |
| 3 | Alkalmazás emblémája | Ennek a képnek a segítségével a felhasználóknak vizuálisan kell megjelenniük, hogy az alkalmazás az elérni kívánt alkalmazás-e. Ezt a rendszerképet az alkalmazások fejlesztői biztosítják, és a rendszerkép tulajdonjoga nincs érvényesítve. |
| 4 | Alkalmazás neve | Ennek az értéknek tájékoztatnia kell a felhasználókat arról, hogy az alkalmazásnak hozzáférést kell kérnie az adatkezeléshez. Megjegyzés: ezt a nevet a fejlesztők biztosítják, és ennek az alkalmazásnak a tulajdonjoga nincs érvényesítve. |
| 5 | Közzétevő tartomány | Ennek az értéknek biztosítania kell a felhasználók számára, hogy a megbízhatóságot ki tudják értékelni. Ezt a tartományt a fejlesztők biztosítják, és a közzétevő tartomány tulajdonjoga érvényesítve van. |
| 6 | Engedélyek | Ez a lista tartalmazza az ügyfélalkalmazás által kért engedélyeket. A felhasználóknak mindig értékelniük kell, hogy milyen típusú engedélyekre van szükség ahhoz, hogy megértsük, milyen az ügyfélalkalmazás jogosult a nevében való hozzáférésre. Alkalmazás-fejlesztőként a legkevesebb jogosultsággal rendelkező engedélyekhez ajánlott a hozzáférés kérése. |
| 7 | Engedély leírása | Ezt az értéket az a szolgáltatás kapja meg, amely kiteszi az engedélyeket. Az engedélyek leírásának megtekintéséhez az engedély mellett be kell váltania a francia idézőjelet. |
| 8 | Alkalmazási feltételek | A jelen feltételek a szolgáltatás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat tartalmaznak. A kiadó feladata a szabályok megjelölése a szolgáltatási feltételekben. Emellett a közzétevő feladata a felhasználói adatok saját adatvédelmi nyilatkozatban való használatának és megosztásának a kiosztása. Ha a közzétevő nem ad meg ezekre az értékekre mutató hivatkozásokat a több-bérlős alkalmazások számára, a jóváhagyásra figyelmeztető üzenet jelenik meg. |
| 9 | https://myapps.microsoft.com | Ez a hivatkozás, ahol a felhasználók áttekinthetik és eltávolíthatják azokat a nem Microsoft-alkalmazásokat, amelyek jelenleg hozzáférnek az adataihoz. |

## <a name="common-consent-scenarios"></a>Közös engedélyezési forgatókönyvek

A felhasználók a közös engedélyezési forgatókönyvekben láthatják a beleegyező felhasználói élményeket:

1. Azok a felhasználók, akik egy olyan alkalmazást érnek el, amely a felhasználó beleegyezési folyamatához irányítja őket, miközben a szolgáltatói hatókörén belül egy engedélyezési készletre van szükségük.
    
    1. A rendszergazdák további szabályozást fognak látni a hagyományos engedélyezési kérdésben, amely lehetővé teszi számukra a teljes bérlő nevében való hozzájárulást. A vezérlő alapértelmezés szerint ki lesz kapcsolva, így csak akkor, ha a rendszergazdának explicit módon be kell állítani a jóváhagyást a teljes bérlő nevében. A mai naptól kezdve ez a jelölőnégyzet csak a globális rendszergazdai szerepkörre mutat, így a Felhőbeli rendszergazda és az alkalmazás rendszergazdája nem fogja látni ezt a jelölőnégyzetet.

        ![Beleegyezés kérése az 1a forgatókönyvhöz](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. A felhasználók láthatják a hagyományos beleegyezés kérését.

        ![A hagyományos beleegyezés kérdését bemutató képernyőkép.](./media/application-consent-experience/consent_prompt_1b.png)

2. Olyan alkalmazásokhoz hozzáférő személyek, amelyek legalább egy olyan engedélyt igényelnek, amely a szolgáltatói hatókörén kívül esik.
    1. A rendszergazdák ugyanazt a kérést fogják látni, mint 1. a fentebb látható.
    2. A felhasználók nem kapnak engedélyt az alkalmazásnak, és megkérik a rendszergazdát, hogy hozzáférjenek az alkalmazáshoz. 
                
        ![Képernyőkép a jóváhagyásról, amely arra utasítja a felhasználót, hogy kérje meg a rendszergazdát, hogy hozzáférjen az alkalmazáshoz.](./media/application-consent-experience/consent_prompt_2b.png)

3. Azok a személyek, akik navigálnak a rendszergazdai beleegyező folyamathoz, vagy átirányítják őket.
    1. A rendszergazda felhasználók láthatják a rendszergazdai jóváhagyást kérő üzenetet. Ebben a kérésben a cím és az engedély leírása módosult, a módosítások kiemelik azt a tényt, hogy a kérés elfogadásával az alkalmazás a teljes bérlő nevében hozzáférést biztosít a kért adathoz.
        
        ![Beleegyezés kérése 1b forgatókönyv esetén](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. A nem rendszergazda felhasználók ugyanazt a képernyőt fogják látni, mint 2. II.

## <a name="next-steps"></a>Következő lépések
- Részletes áttekintést kaphat [arról, hogy az Azure ad-beli engedélyezési keretrendszer hogyan valósítja meg a hozzájárulásukat](./quickstart-register-app.md).
- További részletekért tekintse meg, [Hogyan használhatják a több-bérlős alkalmazások a](./howto-convert-app-to-be-multi-tenant.md) "felhasználó" és a "rendszergazda" jogosultságot a "felhasználói" és "rendszergazdai" engedély megvalósításához, és támogatják a fejlettebb többrétegű alkalmazás-mintákat.
- Ismerje meg [, hogyan konfigurálhatja az alkalmazás közzétevő tartományát](howto-configure-publisher-domain.md).