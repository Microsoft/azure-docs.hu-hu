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
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105413"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése

További információ az Azure Active Directory (Azure AD) alkalmazás-engedélyezési felhasználói élményről. Így intelligens módon kezelheti a szervezete alkalmazásait és/vagy fejlesztheti az alkalmazásokat, és így zökkenőmentesen megadhatja a felhasználói élményt.

## <a name="consent-and-permissions"></a>Hozzájárulás és engedélyek

A hozzájárulás egy olyan felhasználó, aki engedélyt ad egy alkalmazásnak a védett erőforrások elérésére a nevükben. A rendszergazda vagy a felhasználó megkérheti, hogy engedélyezze a hozzáférést a szervezethez vagy az egyes adatszolgáltatásokhoz.

A jogosultság megadásának tényleges felhasználói élménye a felhasználó bérlője, a felhasználó jogosultsági köre (vagy szerepkör), valamint az ügyfélalkalmazás által kért [engedélyek](v2-permissions-and-consent.md) típusa szerint változhat. Ez azt jelenti, hogy az alkalmazás-fejlesztőknek és a bérlői rendszergazdáknak van némi hozzáférésük a beleegyező felhasználói élményhez A rendszergazdák rugalmasan állíthatják be és letilthatják a szabályzatokat a bérlőn vagy az alkalmazáson a bérlőn való részvételi élmény szabályozása érdekében. Az alkalmazások fejlesztői megadhatják, hogy milyen típusú engedélyekre van szükség, és ha a felhasználókat a felhasználói beleegyező folyamaton vagy a rendszergazdai beleegyező folyamaton keresztül szeretnék irányítani.

- A **felhasználói hozzájárulás folyamata** az, amikor egy alkalmazás fejlesztője a felhasználókat az engedélyezési végpontra irányítja, hogy csak az aktuális felhasználó belefoglalását jegyezze fel.
- A rendszergazdai belefoglalási **folyamat** az, amikor egy alkalmazás fejlesztője a felhasználókat a rendszergazdai belefoglalási végpontra irányítja azzal a szándékkal, hogy rögzítse a teljes bérlőhöz való hozzájárulásukat. Annak biztosítása érdekében, hogy a rendszergazdai jogosultságok folyamata megfelelően működjön, az alkalmazás-fejlesztőknek az alkalmazás jegyzékfájljában szereplő összes engedélyt listáznia kell `RequiredResourceAccess` . További információ: [Application manifest](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>A hozzájárulási kérés építőelemei

A beleegyezés kérése úgy van kialakítva, hogy a felhasználók elegendő információval rendelkezzenek annak megállapításához, hogy megbízhatók-e az ügyfélalkalmazás a védett erőforrások nevében való eléréséhez. Az építőelemek megismerése segíti a felhasználókat abban, hogy jobban tájékozott döntéseket hozhassanak, így a fejlesztők jobb felhasználói élményt hozhatnak létre.

A következő diagram és táblázat a hozzájárulási kérés építőelemeit ismerteti.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="A hozzájárulási kérés építőelemei":::

| # | Összetevő | Cél |
| ----- | ----- | ----- |
| 1 | Felhasználói azonosító | Ez az azonosító azt a felhasználót jelöli, akit az ügyfélalkalmazás a következő nevében a védett erőforrásokhoz való hozzáférésre kéri. |
| 2 | Cím | A cím változása attól függően változik, hogy a felhasználók a felhasználói vagy a rendszergazdai beleegyező folyamaton keresztül mennek keresztül. A felhasználói beleegyező folyamatokban a cím "engedély kérése", míg a rendszergazda beleegyező folyamatában a cím egy további, "elfogadás a szervezet számára" sort fog tartalmazni. |
| 3 | Alkalmazás emblémája | Ennek a képnek a segítségével a felhasználóknak vizuálisan kell megjelenniük, hogy az alkalmazás az elérni kívánt alkalmazás-e. Ezt a rendszerképet az alkalmazások fejlesztői biztosítják, és a rendszerkép tulajdonjoga nincs érvényesítve. |
| 4 | Alkalmazás neve | Ennek az értéknek tájékoztatnia kell a felhasználókat arról, hogy az alkalmazásnak hozzáférést kell kérnie az adatkezeléshez. Megjegyzés: ezt a nevet a fejlesztők biztosítják, és ennek az alkalmazásnak a tulajdonjoga nincs érvényesítve. |
| 5 | Közzétevő tartomány | Ennek az értéknek biztosítania kell a felhasználók számára, hogy a megbízhatóságot ki tudják értékelni. Ezt a tartományt a fejlesztők biztosítják, és a közzétevő tartomány tulajdonjoga érvényesítve van. |
| 6 | Közzétevő ellenőrizve | A kék "ellenőrzött" jelvény azt jelenti, hogy az alkalmazás közzétevője ellenőrizte az identitását egy Microsoft Partner Network fiók használatával, és befejezte az ellenőrzési folyamatot.|
| 7 | Kibocsátó adatai  | Megjeleníti, hogy a Microsoft vagy a szervezete közzétette-e az alkalmazást. |
| 8 | Engedélyek | Ez a lista tartalmazza az ügyfélalkalmazás által kért engedélyeket. A felhasználóknak mindig értékelniük kell, hogy milyen típusú engedélyekre van szükség ahhoz, hogy megértsük, milyen az ügyfélalkalmazás jogosult a nevében való hozzáférésre. Alkalmazás-fejlesztőként a legkevesebb jogosultsággal rendelkező engedélyekhez ajánlott a hozzáférés kérése. |
| 9 | Engedély leírása | Ezt az értéket az a szolgáltatás kapja meg, amely kiteszi az engedélyeket. Az engedélyek leírásának megtekintéséhez az engedély mellett be kell váltania a francia idézőjelet. |
| 10| Alkalmazási feltételek | A jelen feltételek a szolgáltatás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat tartalmaznak. A kiadó feladata a szabályok megjelölése a szolgáltatási feltételekben. Emellett a közzétevő feladata a felhasználói adatok saját adatvédelmi nyilatkozatban való használatának és megosztásának a kiosztása. Ha a közzétevő nem ad meg ezekre az értékekre mutató hivatkozásokat a több-bérlős alkalmazások számára, a jóváhagyásra figyelmeztető üzenet jelenik meg. |
| 11 | https://myapps.microsoft.com | Ez a hivatkozás, ahol a felhasználók áttekinthetik és eltávolíthatják azokat a nem Microsoft-alkalmazásokat, amelyek jelenleg hozzáférnek az adataihoz. |
| 12 | Itt jelentheti be | Ez a hivatkozás egy gyanús alkalmazás jelentésére szolgál, ha nem bízik meg az alkalmazásban, ha úgy véli, hogy az alkalmazás megszemélyesít egy másik alkalmazást, ha úgy gondolja, hogy az alkalmazás nem fogja tudni használni az adatait, vagy valamilyen más okból. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>Az alkalmazásnak engedélyre van szüksége a felhasználó hatókörén belül

Általános beleegyezési forgatókönyv szerint a felhasználó hozzáfér egy olyan alkalmazáshoz, amelyhez a felhasználó hatókörén belüli engedély szükséges. A felhasználó a felhasználói beleegyező folyamatra van irányítva.

A rendszergazdák további szabályozást fognak látni a hagyományos engedélyezési kérdésben, amely lehetővé teszi számukra a teljes bérlő nevében való hozzájárulást. A vezérlő alapértelmezés szerint ki lesz kapcsolva, így csak akkor, ha a rendszergazdának explicit módon be kell állítani a jóváhagyást a teljes bérlő nevében. A mai naptól kezdve ez a jelölőnégyzet csak a globális rendszergazdai szerepkörre mutat, így a Felhőbeli rendszergazda és az alkalmazás rendszergazdája nem fogja látni ezt a jelölőnégyzetet.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Beleegyezés kérése az 1a forgatókönyvhöz":::

A felhasználók láthatják a hagyományos beleegyezés kérését.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="A hagyományos beleegyezés kérdését bemutató képernyőkép.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>Az alkalmazásnak engedélyre van szüksége a felhasználó hatókörén kívül

Egy másik gyakori beleegyezési forgatókönyv szerint a felhasználó olyan alkalmazáshoz fér hozzá, amelyhez legalább egy engedély szükséges, amely a felhasználó hatókörén kívül esik.

A rendszergazdák további szabályozást fognak látni a hagyományos engedélyezési kérdésben, amely lehetővé teszi számukra a teljes bérlő nevében való hozzájárulást.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Beleegyezés kérése az 1a forgatókönyvhöz":::

A nem rendszergazda felhasználók nem kapnak jóváhagyást az alkalmazáshoz, és azt is megkérik, hogy kérjenek a rendszergazdát, hogy hozzáférjenek az alkalmazáshoz.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Képernyőkép a jóváhagyásról, amely arra utasítja a felhasználót, hogy kérje meg a rendszergazdát, hogy hozzáférjen az alkalmazáshoz.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>A felhasználó a rendszergazdai beleegyező folyamatra van irányítva

Egy másik gyakori eset az, amikor a felhasználó navigál a rendszergazdai beleegyező folyamathoz, vagy átirányítja őket.

A rendszergazda felhasználók láthatják a rendszergazdai jóváhagyást kérő üzenetet. Ebben a kérésben a cím és az engedély leírása módosult, a módosítások kiemelik azt a tényt, hogy a kérés elfogadásával az alkalmazás a teljes bérlő nevében hozzáférést biztosít a kért adathoz.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Beleegyezés kérése – 3a. forgatókönyv":::

A nem rendszergazda felhasználók nem kapnak jóváhagyást az alkalmazáshoz, és azt is megkérik, hogy kérjenek a rendszergazdát, hogy hozzáférjenek az alkalmazáshoz.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Képernyőkép a jóváhagyásról, amely arra utasítja a felhasználót, hogy kérje meg a rendszergazdát, hogy hozzáférjen az alkalmazáshoz.":::

## <a name="next-steps"></a>Következő lépések

- Részletes áttekintést kaphat [arról, hogy az Azure ad-beli engedélyezési keretrendszer hogyan valósítja meg a hozzájárulásukat](./quickstart-register-app.md).
- További részletekért tekintse meg, [Hogyan használhatják a több-bérlős alkalmazások a](./howto-convert-app-to-be-multi-tenant.md) "felhasználó" és a "rendszergazda" jogosultságot a "felhasználói" és "rendszergazdai" engedély megvalósításához, és támogatják a fejlettebb többrétegű alkalmazás-mintákat.
- Ismerje meg [, hogyan konfigurálhatja az alkalmazás közzétevő tartományát](howto-configure-publisher-domain.md).