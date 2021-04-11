---
title: ISV-partneri katalógus a Azure AD B2Choz
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható az ISV-partnereinkkel a végfelhasználói élmény igényei szerint. A partneri hálózat kibővíti a megoldási képességeiket; MFA engedélyezése, biztonságos ügyfél-hitelesítés, szerepköralapú hozzáférés-vezérlés; csalás elleni küzdelem a személyazonosság-ellenőrzés igazolásával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0629606f602b975b399855a681570ecdac9ec832
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256820"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>ISV-partnerek Azure Active Directory B2C

Az ISV-partneri hálózat kibővíti a megoldási képességeiket a zökkenőmentes végfelhasználói élmények kiépítésének megkönnyítésére. A Azure AD B2C lehetővé teszi az ISV-partnerekkel való integrációt a többtényezős hitelesítés (MFA) módszerei, a szerepköralapú hozzáférés-vezérlés, az identitás-ellenőrzés és a próbanyomatok engedélyezése, a biztonság növelése a bot-észlelés és a csalások elleni védelem terén, valamint a fizetési szolgáltatások irányelv 2 (PSD2) biztonságos ügyfél-hitelesítési (SCA) követelményeinek teljesítése. A részletes példákból megismerheti, hogyan integrálhatja az alkalmazásokat az ISV-partnerekkel.

>[!NOTE]
>A [githubon található Azure Active Directory B2C közösségi webhely](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) a Közösségtől származó egyéni szabályzatokat is biztosít.

## <a name="identity-verification-and-proofing"></a>Személyazonosság ellenőrzése és ellenőrzés

A Microsoft partnerei a következő ISV-ket biztosítják a személyazonosság ellenőrzéséhez és ellenőrzéséhez.

| ISV-partner | Leírás és integrációs útmutató |
|:-------------------------|:--------------|
|![Képernyőkép egy Experian-emblémáról.](./media/partner-gallery/experian-logo.png) | A [Experian](./partner-experian.md) egy személyazonosság-ellenőrző és ellenőrző szolgáltató, amely a felhasználói attribútumok alapján kockázatértékeléseket végez a csalások megelőzése érdekében. |
|![Képernyőkép egy IDology-emblémáról.](./media/partner-gallery/idology-logo.png) | A [IDology](./partner-idology.md) egy azonosító-ellenőrzési megoldásokkal, csalás-megelőzési megoldásokkal, megfelelőségi megoldásokkal és egyebekkel rendelkező személyazonosság-ellenőrzési és-ellenőrző szolgáltató.|
|![Képernyőkép egy Jumio-emblémáról.](./media/partner-gallery/jumio-logo.png) | A [Jumio](./partner-jumio.md) egy azonosító-ellenőrzési szolgáltatás, amely lehetővé teszi a valós idejű automatizált azonosítók ellenőrzését, így biztosítva az ügyféladatok védelmét. |
| ![Képernyőkép egy LexisNexis-emblémáról.](./media/partner-gallery/lexisnexis-logo.png) | A [LexisNexis](./partner-lexisnexis.md) egy profilkészítési és identitás-ellenőrzési szolgáltató, amely ellenőrzi a felhasználó azonosítását, és átfogó kockázatértékelést biztosít a felhasználó eszköze alapján. |
| ![Képernyőkép egy Onfido-emblémáról](./media/partner-gallery/onfido-logo.png) | A [Onfido](./partner-onfido.md) egy dokumentum-azonosító és egy arc biometria-ellenőrzési megoldás, amely lehetővé teszi a vállalatok számára, hogy valós időben tudják kielégíteni *az ügyfél* és az identitás követelményeit.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA-és jelszó-alapú hitelesítés

Microsoft-partnerek a következő ISV-val az MFA-és jelszó-alapú hitelesítéshez.

| ISV-partner | Leírás és integrációs útmutató |
|:-------------------------|:--------------|
| ![Képernyőkép egy hypr-emblémáról](./media/partner-gallery/hypr-logo.png) | A [Hypr](./partner-hypr.md) egy jelszóval nem kompatibilis hitelesítésszolgáltató, amely a nyilvános kulcsú titkosítással rendelkező jelszavakat a csalások, az adathalászat és a hitelesítő adatok újrafelhasználásának megszüntetésével váltja fel. |
| ![Képernyőkép egy itsme-emblémáról](./media/partner-gallery/itsme-logo.png) | a [itsme](./partner-itsme.md) egy elektronikus azonosítási, hitelesítési és megbízhatósági szolgáltatás (eiDAS) által kompatibilis digitális azonosító megoldás, amely lehetővé teszi a felhasználók számára, hogy a kártyák olvasói, jelszavas, kétfaktoros hitelesítés és több PIN-kód nélkül is biztonságosan bejelentkezzenek. |
|![Képernyőfelvétel egy kulcsnélküli emblémáról.](./media/partner-gallery/keyless-logo.png) | A [kulcsnélküli](./partner-keyless.md) szolgáltatás olyan jelszó nélküli hitelesítési szolgáltató, amely egy arc biometrikus vizsgálat formájában biztosítja a hitelesítést, kiküszöbölve a csalások, az adathalászat és a hitelesítő adatok újbóli használatát.
| ![A Nevis emblémájának képernyőképe](./media/partner-gallery/nevis-logo.png) | A [Nevis](./partner-nevis.md) lehetővé teszi a jelszó-alapú hitelesítést, és mobil-első, teljesen márkás végfelhasználói élményt nyújt a Nevis hozzáférési alkalmazással az erős ügyfél-hitelesítéshez és a PSD2 tranzakciós követelményeinek teljesítéséhez. |
| ![Képernyőkép egy trusona-emblémáról](./media/partner-gallery/trusona-logo.png) | A [Trusona](./partner-trusona.md) -integráció segítségével biztonságosan bejelentkezhet, és engedélyezheti a jelszó nélküli hitelesítést, az MFA-t és a digitális licencek vizsgálatát. |
| ![Képernyőkép egy twilio-emblémáról.](./media/partner-gallery/twilio-logo.png) | A [Twilio ellenőrzi](./partner-twilio.md) , hogy az alkalmazás több megoldással teszi lehetővé az MFA SMS egyszeri jelszavas (OTP), az időalapú egyszeri jelszó (TOTP) és a leküldéses értesítések használatát, valamint a PSD2 vonatkozó SCA-követelmények betartását. |
| ![Képernyőkép egy typingDNA-emblémáról](./media/partner-gallery/typingdna-logo.png) | A [TypingDNA](./partner-typingdna.md) lehetővé teszi, hogy a felhasználó gépelési mintáját elemezze erős ügyfél-hitelesítésre. Lehetővé teszi a vállalatok számára a csendes MFA használatát, és megfelel a PSD2-re vonatkozó SCA-követelményeknek. |
| ![Képernyőkép egy whoiam-emblémáról](./media/partner-gallery/whoiam-logo.png) | A [WhoIAM](./partner-whoiam.md) egy márkás identitáskezelésrendszer-(BRIMS-) alkalmazás, amely lehetővé teszi, hogy a szervezetek hang-, SMS-és e-mail-cím alapján ellenőrizzék a felhasználói bázist. |

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés 
 
Microsoft-partnerek a következő ISV-val a szerepköralapú hozzáférés-vezérléshez.

| ISV-partner | Leírás és integrációs útmutató |
|:-------------------------|:--------------|
| ![Képernyőkép egy n8identity-emblémáról](./media/partner-gallery/n8identity-logo.png) | A [N8Identity](./partner-n8identity.md) egy szolgáltatás-felügyeleti platform, amely megoldást nyújt a Microsoft Azureon futó ügyfél-fiókok áttelepítésére és az ügyfélszolgálati kérelmekre (CSR) való felügyeletre. |
| ![Képernyőkép egy Saviynt-emblémáról](./media/partner-gallery/saviynt-logo.png) | A [Saviynt](./partner-Saviynt.md) Cloud-Native platform a jobb biztonságot, megfelelőséget és irányítást segíti az intelligens elemzések és az alkalmazások integrálásával az informatikai modernizáció egyszerűsítése érdekében. |

## <a name="security"></a>Biztonság

Microsoft-partnerek a következő biztonsági ISV-val.

| ISV-partner | Leírás és integrációs útmutató |
|:-------------------------|:--------------|
| ![Képernyőkép a Arkóz labor emblémáról](./media/partner-gallery/arkose-logo.png) | A [Arkóz Labs](./partner-arkose-labs.md) egy csalások elleni megelőzési megoldás szolgáltatója, amely segítséget nyújt a szervezeteknek a robotok elleni támadásokkal, a fiókok átvételi támadásokkal és a csalárd fiókok megnyitásával |
| ![Képernyőkép a Microsoft Dynamics 365 emblémáról](./media/partner-gallery/microsoft-dynamics365-logo.png) | A [Microsoft Dynamics 365 csalások elleni védelem](./partner-dynamics-365-fraud-protection.md) olyan megoldás, amely segít a szervezeteknek a jogosulatlan fiókok elleni védelemben az eszközök ujjlenyomatának használatával. |
| ![Képernyőkép a ping logóról](./media/partner-gallery/ping-logo.png) | A [ping Identity](./partner-ping-identity.md) lehetővé teszi, hogy a biztonságos hibrid hozzáférést biztosítson a helyszíni örökölt alkalmazásokhoz több felhőben. |
| ![Képernyőfelvétel a rétegek emblémáról](./media/partner-gallery/strata-logo.png) | A [rétegek](./partner-strata.md) biztonságos hibrid hozzáférést biztosítanak a helyszíni alkalmazásokhoz a konzisztens hozzáférési házirendek betartatásával, az identitások szinkronban tartásával, valamint egyszerűvé teszik a régi identitású rendszerekből származó alkalmazások átváltását a szabvány alapú hitelesítésre és a Azure ad B2C által biztosított hozzáférés-vezérlésre. |
| ![Képernyőkép egy zscaler-emblémáról](./media/partner-gallery/zscaler-logo.png) | A [Zscaler](./partner-zscaler.md) házirend-alapú, biztonságos hozzáférést biztosít a privát alkalmazásokhoz és az eszközökhöz a VPN-t érintő többletköltségek, szóváltás vagy biztonsági kockázatok nélkül. |

## <a name="additional-information"></a>További információ

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Következő lépések

Válasszon ki egy partnert az említett táblázatokban, és Ismerje meg, hogyan integrálhatja megoldását Azure AD B2C használatával.
