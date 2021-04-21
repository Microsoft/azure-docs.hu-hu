---
title: ISV Partner gallery for Azure AD B2C
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan integrálhatja az ISV-partnereivel, hogy a végfelhasználói élményt az igényeinek megfelelően szabja. Partnerhálózatunk kibővíti a megoldási képességeket; MFA, biztonságos ügyfél-hitelesítés, szerepköralapú hozzáférés-vezérlés engedélyezése; csalás elleni, személyazonosság-ellenőrzési igazolással.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b555c8651c6e1608193a6ae06c39b20f633e4ea9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813657"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV-partnerek

IsV-partnerhálózatunk kibővíti a megoldási képességeket, így zökkenőmentes végfelhasználói élményt építhet ki. Az Azure AD B2C lehetővé teszi az ISV-partnerekkel való integrációt a többtényezős hitelesítés (MFA) módszereinek engedélyezéséhez, a szerepköralapú hozzáférés-vezérléshez, a személyazonosság ellenőrzéséhez és ellenőrzéséhez, a robotészlelési és csalás elleni védelem javításához, valamint a Payment Services 2(PSD2) Secure Customer Authentication (SCA) követelményeinek való megfeleléshez. Az alkalmazások ISV-partnerekkel való integrálásának elsajátítása részletes mintaúton keresztül érhető el.

>[!NOTE]
>A [Azure Active Directory B2C GitHubon található](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) közösségi webhely a közösségtől származó egyéni mintaházi szabályzatokat is biztosít.

## <a name="identity-verification-and-proofing"></a>Személyazonosság ellenőrzése és igazolása

A Microsoft a következő isV-ket partneri kapcsolatban állja a személyazonosság ellenőrzéséhez és igazolásához.

| ISV-partner | Leírások és integrációs bemutatók |
|:-------------------------|:--------------|
|![Experian embléma képernyőképe.](./media/partner-gallery/experian-logo.png) | [Az experian](./partner-experian.md) egy személyazonosság-ellenőrzési és igazolási szolgáltató, amely felhasználói attribútumok alapján végez kockázatfelmérést a csalások megelőzése érdekében. |
|![Az IDology embléma képernyőképe.](./media/partner-gallery/idology-logo.png) | [Az IDology](./partner-idology.md) egy identitás-ellenőrzési és igazolási szolgáltató azonosító-ellenőrzési megoldásokkal, csalás elleni megelőzési megoldásokkal, megfelelőségi megoldásokkal és más megoldásokkal.|
|![Jumio embléma képernyőképe.](./media/partner-gallery/jumio-logo.png) | [A Jumio](./partner-jumio.md) egy azonosító-ellenőrzési szolgáltatás, amely valós idejű automatizált azonosító-ellenőrzést tesz lehetővé, és védi az ügyféladatokat. |
| ![A LexisNexis embléma képernyőképe.](./media/partner-gallery/lexisnexis-logo.png) | [A LexisNexis](./partner-lexisnexis.md) egy profilkészítési és identitás-ellenőrzési szolgáltató, amely ellenőrzi a felhasználók azonosítását, és átfogó kockázatértékelést biztosít a felhasználó eszközén. |
| ![Képernyőkép az Onfido emblémáról](./media/partner-gallery/onfido-logo.png) | [Az Onfido](./partner-onfido.md) egy dokumentumazonosító és arc-biometrika-ellenőrzési megoldás, amely lehetővé teszi a vállalatok számára, hogy valós időben teljesítik az Ügyfél és a személyazonosság követelményeit.   |

## <a name="mfa-and-passwordless-authentication"></a>MFA és jelszó nélküli hitelesítés

A Microsoft az alábbi isV-ket használja MFA- és jelszó nélküli hitelesítéshez.

| ISV-partner | Leírások és integrációs bemutatók |
|:-------------------------|:--------------|
| ![Képernyőkép a hypr emblémáról](./media/partner-gallery/hypr-logo.png) | [A Hypr](./partner-hypr.md) egy jelszó nélküli hitelesítésszolgáltató, amely a jelszavakat nyilvános kulcsú titkosításra cseréli, így kiküszöbölve a csalásokat, az adathalászatot és a hitelesítő adatok újbóli használatát. |
| ![Képernyőkép egy embléma emblémáról](./media/partner-gallery/itsme-logo.png) | [Az itsme](./partner-itsme.md) egy elektronikus azonosítási, hitelesítési és megbízhatósági szolgáltatásoknak (eiDAS) megfelelő digitális azonosító megoldás, amely lehetővé teszi a felhasználók számára, hogy biztonságosan jelentkezzenek be kártyaolvasók, jelszavak, kéttényezős hitelesítés és több PIN-kód nélkül. |
|![Kulcs nélküli embléma képernyőképe.](./media/partner-gallery/keyless-logo.png) | [A kulcs nélküli](./partner-keyless.md) hitelesítésszolgáltató olyan jelszó nélküli hitelesítésszolgáltató, amely arc-biometrikus vizsgálat formájában biztosítja a hitelesítést, és kiküszöböli a csalást, adathalászatot és a hitelesítő adatok újbóli használatát.
| ![Nevis embléma képernyőképe](./media/partner-gallery/nevis-logo.png) | [A Nevis](./partner-nevis.md) lehetővé teszi a jelszó nélküli hitelesítést, és mobil, teljes körű végfelhasználói élményt nyújt a Nevis Access alkalmazással az erős ügyfél-hitelesítés és a PSD2 tranzakciós követelmények teljesítéséhez. |
| ![A trusona emblémájának képernyőképe](./media/partner-gallery/trusona-logo.png) | [A Trusona-integráció](./partner-trusona.md) segít a biztonságos bejelentkezésben, és lehetővé teszi a jelszó nélküli hitelesítést, az MFA-t és a digitális licencek vizsgálatát. |
| ![A twilio emblémájának képernyőképe.](./media/partner-gallery/twilio-logo.png) | A [Twilio Verify](./partner-twilio.md) alkalmazás több megoldást is kínál az MFA egyszeri SMS-jelszó (OTP), az időalapú egyszeri jelszó (TOTP) és a leküldéses értesítések engedélyezéséhez, valamint a PSD2 SCA-követelményeinek való megfeleléshez. |
| ![A gépelésiDNA embléma képernyőképe](./media/partner-gallery/typingdna-logo.png) | [A GépelésDNA](./partner-typingdna.md) lehetővé teszi az erős ügyfél-hitelesítést a felhasználó gépelési mintáját elemezve. Segít a vállalatoknak a csendes MFA engedélyezésében és a PSD2 SCA-követelményeinek való megfelelésben. |
| ![Képernyőkép egy képembléma emblémáról](./media/partner-gallery/whoiam-logo.png) | [A WhoIAM](./partner-whoiam.md) egy védjegyes identitáskezelő rendszer (BRIMS) alkalmazás, amellyel a szervezetek hangalapú, SMS-ben és e-mailben ellenőrizhetik a felhasználói bázisukat. |

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés 
 
A Microsoft a következő isV-ket partneri partnerként mutatja be a szerepköralapú hozzáférés-vezérléshez.

| ISV-partner | Leírás és integráció – bemutatók |
|:-------------------------|:--------------|
| ![Az n8identity emblémájának képernyőképe](./media/partner-gallery/n8identity-logo.png) | [Az N8Identity](./partner-n8identity.md) egy szolgáltatásként való identitásirányítási platform, amely megoldást kínál az ügyfélfiókok migráltatására és az ügyfélszolgálati kérelmek (CSR) felügyeletére a Microsoft Azure. |
| ![Képernyőkép a Saviynt emblémáról](./media/partner-gallery/saviynt-logo.png) | [A nagy felhőbeli](./partner-Saviynt.md) natív platform intelligens elemzésekkel és alkalmazásközi integrációval támogatja a jobb biztonságot, megfelelőséget és irányítást az it-modernizáláshoz. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Biztonságos hibrid hozzáférés a helyszíni alkalmazásokhoz

A Microsoft az alábbi isV-ket használja a helyszíni alkalmazások biztonságos hibrid elérésének biztosítása érdekében. 

| ISV-partner | Leírás és integráció – bemutatók |
|:-------------------------|:--------------|
| ![A Ping embléma képernyőképe](./media/partner-gallery/ping-logo.png) | [A Ping Identity](./partner-ping-identity.md) biztonságos hibrid hozzáférést biztosít a helyszíni örökölt alkalmazásokhoz több felhőben. |
| ![Képernyőkép egy rétegemblémáról](./media/partner-gallery/strata-logo.png) | A [rétegek](./partner-strata.md) biztonságos hibrid hozzáférést biztosítanak a helyszíni alkalmazásokhoz konzisztens hozzáférési szabályzatok betartatásával, az identitások szinkronban való tartásával, valamint az alkalmazásoknak az örökölt identitásrendszerről a Azure AD B2C által biztosított szabványalapú hitelesítésre és hozzáférés-vezérlésre való áttérésének Azure AD B2C. |
| ![Zscaler embléma képernyőképe](./media/partner-gallery/zscaler-logo.png) | [A Zscaler](./partner-zscaler.md) szabályzatalapú, biztonságos hozzáférést biztosít a magánalkalmazások és eszközök számára a VPN költség-, probléma- és biztonsági kockázatai nélkül. |

## <a name="fraud-protection"></a>Csalás elleni védelem

A Microsoft a következő isV-ket partneri partnerként mutatja be a csalások észlelése és megelőzése érdekében. 

| ISV-partner | Leírások és integrációs bemutatók |
|:-------------------------|:--------------|
| ![Képernyőkép egy Arkose Lab emblémáról](./media/partner-gallery/arkose-logo.png) | [Az Arkose Labs](./partner-arkose-labs.md) egy csalás elleni megelőzési megoldásszolgáltató, amely segít a szervezeteknek a robotok, a fiókátvételi támadások és a fiókok rosszindulatú megnyitásai elleni védelemben. |
| ![A BioCatch emblémájának képernyőképe](./media/partner-gallery/biocatch-logo.png) | [A BioCatch](./partner-biocatch.md) egy csalás-megelőzési megoldásszolgáltató, amely elemzi a felhasználók fizikai és kognitív digitális viselkedését, hogy olyan elemzéseket hozzon létre, amelyek megkülönböztetik a megbízható ügyfeleket és a számítógépes fenyegetéseket. |
| ![A Microsoft Dynamics 365 emblémájának képernyőképe](./media/partner-gallery/microsoft-dynamics365-logo.png) | [A Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md) egy olyan megoldás, amely segít a szervezeteknek az eszköz ujjlenyomatával való rosszindulatú fióknyitásokkal szembeni védelemben. |


## <a name="additional-information"></a>További információ

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Az egyéni szabályzatok első lépések a Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Következő lépések

Válasszon ki egy partnert a fenti táblázatokban, hogy megtudja, hogyan integrálhatja a megoldását a Azure AD B2C.
