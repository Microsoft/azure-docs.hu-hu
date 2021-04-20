---
title: Feltételes hozzáférés B2B együttműködési felhasználók számára – Azure AD
description: Ismerje meg, hogyan kényszeríthetőek a többtényezős hitelesítési szabályzatok Azure Active Directory B2B-felhasználók számára.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739547"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Feltételes hozzáférés B2B együttműködési felhasználók számára

Ez a cikk azt ismerteti, hogy a szervezetek hogyan terjedhet ki a feltételes hozzáférési (CA) szabályzatok hatókörére b2B vendégfelhasználók számára az erőforrásaik eléréséhez.
>[!NOTE]
>Ez a hitelesítési vagy engedélyezési folyamat kissé eltér a vendégfelhasználóktól, mint az identitásszolgáltató (IdP) meglévő felhasználóitól.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Hitelesítési folyamat B2B vendégfelhasználók számára külső címtárból

A folyamatot az alábbi ábra szemlélteti: a képen a B2B vendégfelhasználók külső címtárból történő ![ hitelesítési folyamata látható](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Lépés | Description |
|--------------|-----------------------|
| 1. | A B2B vendégfelhasználó hozzáférést kér egy erőforráshoz. Az erőforrás átirányítja a felhasználót az erőforrás-bérlőhöz, egy megbízható idP-hez.|
| 2. | Az erőforrás-bérlő külsőként azonosítja a felhasználót, és átirányítja a felhasználót a B2B-vendégfelhasználó azonosítójára. A felhasználó elsődleges hitelesítést végez az internetszolgáltatóban.
| 3. | A B2B-vendégfelhasználó idP-ja egy jogkivonatot ad ki a felhasználónak. A felhasználó vissza lesz irányítva az erőforrás-bérlőre a jogkivonattal. Az erőforrás-bérlő ellenőrzi a jogkivonatot, majd kiértékeli a felhasználót a CA-szabályzatai szerint. Az erőforrás-bérlő megkövetelheti például, hogy a felhasználó többtényezős Azure Active Directory (AD) végezzen.
| 4. | Ha az összes erőforrás-bérlői CA-szabályzat teljesült, az erőforrás-bérlő kijaja a saját jogkivonatát, és átirányítja a felhasználót az erőforrásához.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Hitelesítési folyamat B2B-vendégfelhasználók számára egy alkalommal használt PIN-kóddal

A folyamatot az alábbi ábra szemlélteti: a képen a B2B-vendégfelhasználók egy alkalommal használt ![ PIN-kódjával való hitelesítési folyamat látható](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Lépés | Description |
|--------------|-----------------------|
| 1. |A felhasználó hozzáférést kér egy másik bérlő egyik erőforrása számára. Az erőforrás átirányítja a felhasználót az erőforrás-bérlőhöz, egy megbízható idP-hez.|
| 2. | Az erőforrás-bérlő külső [e-mail-jelszóval (OTP)](./one-time-passcode.md) azonosítja a felhasználót, és e-mailt küld az egyszeri jelszavas jelszóval a felhasználónak.|
| 3. | A felhasználó lekéri az otP-t, és elküldi a kódot. Az erőforrás-bérlő kiértékeli a felhasználót a CA-szabályzatai szerint.
| 4. | Ha az összes CA-szabályzat teljesült, az erőforrás-bérlő ki ad egy jogkivonatot, és átirányítja a felhasználót az erőforrásához. |

>[!NOTE]
>Ha a felhasználó egy külső erőforrás-bérlőből érkezett, nem lehetséges, hogy a B2B vendégfelhasználó idP CA-házirendje is ki legyen értékelve. Jelenleg csak az erőforrás-bérlő CA-szabályzata vonatkozik a vendégekre.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD Multi-Factor Authentication B2B-felhasználók számára

A szervezetek több Azure AD Multi-Factor Authentication-szabályzatot is kikényszeríteni tudnak a B2B vendégfelhasználóik számára. Ezek a szabályzatok a bérlő, az alkalmazás vagy az egyes felhasználók szintjén kényszeríthetőek ugyanúgy, mint a teljes munkaidős alkalmazottak és a szervezet tagjai számára.
Az erőforrás-bérlő mindig a felhasználók Azure AD Multi-Factor Authentication-hitelesítéséért felelős, még akkor is, ha a vendégfelhasználó vállalata rendelkezik Multi-Factor Authentication-képességekkel. Példa:

1. Egy Fabrikam nevű vállalat rendszergazdája vagy információ-feldolgozója meghívja egy másik, Contoso nevű vállalat felhasználóját a Woodgrove alkalmazás használatára.

2. A Fabrikam Woodgrove alkalmazása úgy van konfigurálva, hogy hozzáférés esetén megkövetelje az Azure AD Multi-Factor Authentication használatát.

3. Amikor a Contoso B2B-vendégfelhasználója megpróbálja elérni a Woodgrove-ot a Fabrikam-bérlőben, a rendszer az Azure AD Multi-Factor Authentication-feladat befejezését kéri.

4. A vendégfelhasználó ezután beállíthatja az Azure AD Multi-Factor Authenticationt a Fabrikam segítségével, és kiválaszthatja a beállításokat.

5. Ez a forgatókönyv bármilyen identitáshoz használható – Azure AD-hez vagy személyes Microsoft-fiókhoz (MSA). Ha például a Contoso felhasználója közösségi azonosítóval hitelesíti magát.

6. A Fabrikamnak megfelelő prémium szintű Azure AD-licencekkel kell rendelkezik, amelyek támogatják az Azure AD Multi-Factor Authenticationt. A Contoso felhasználója ezután a Fabrikamtól felhasználja ezt a licencet. A B2B-licenceléssel kapcsolatos információkért tekintse meg az [Azure AD külső](./external-identities-pricing.md) identitások számlázási modelljét.

>[!NOTE]
>Az Azure AD Multi-Factor Authentication az erőforrás-bérlőknél történik a kiszámíthatóság érdekében. Amikor a vendégfelhasználó bejelentkezik, a háttérben megjelenik az erőforrás-bérlő bejelentkezési oldala, az előtérben pedig a saját otthoni bérlő bejelentkezési oldala és a cég emblémája.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Az Azure AD Multi-Factor Authentication beállítása B2B-felhasználók számára

Az Azure AD Multi-Factor Authentication B2B-együttműködési felhasználók számára való beállítását az alábbi videóban néztük meg:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-felhasználók Azure AD Multi-Factor Authentication az ajánlat beváltási szolgáltatásához

Az Azure AD Multi-Factor Authentication érvényesítési élményről az alábbi videóban olvashat bővebben:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Az Azure AD Multi-Factor Authentication alaphelyzetbe állítása B2B-felhasználók számára

Most már elérhetők a következő PowerShell-parancsmagok a B2B vendégfelhasználók ellenőrzésére:

1. Csatlakozás az Azure AD szolgáltatáshoz

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Az összes felhasználó lekérte a proof up metódusokkal

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Alább bemutatunk egy példát:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Állítsa alaphelyzetbe egy adott felhasználó Azure AD Multi-Factor Authentication-módszerét, hogy a B2B-együttműködés felhasználója újra megkövetelje az igazolási módszerek beállítását. 
   Alább bemutatunk egy példát:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Feltételes hozzáférés B2B-felhasználók számára

A B2B vendégfelhasználók CA-szabályzatát számos tényező befolyásolja.

### <a name="device-based-conditional-access"></a>Eszközalapú feltételes hozzáférés

A hitelesítésszolgáltatóban lehetőség van arra, hogy a felhasználó eszközének megfelelőnek vagy hibrid [Azure AD-hez](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)csatlakozottnak kell lennie. A B2B-vendégfelhasználók csak akkor teljesítik a megfelelőséget, ha az erőforrás-bérlő felügyelheti az eszközét. Az eszközöket egyszerre csak egy szervezet tudja kezelni. A B2B-vendégfelhasználók nem tudják kielégíteni a hibrid Azure AD-csatlakozást, mert nincs helyszíni AD-fiókjuk. Csak akkor regisztrálhatja vagy regisztrálhatja az eszközét az erőforrás-bérlőben, ha a vendégfelhasználó eszköze nem rendelkezik a megfelelő eszközzel. A felhasználó ezután megfelelhet a megadó vezérlőnek.

>[!Note]
>Külső felhasználók számára nem ajánlott felügyelt eszközt igényelni.

### <a name="mobile-application-management-policies"></a>Mobilalkalmazás-kezelési szabályzatok

A hitelesítésszolgáltató olyan engedélyvezérlőinek, mint a **Jóváhagyott** ügyfélalkalmazások megkövetelása és az Alkalmazásvédelmi szabályzatok megkövetelása, regisztrálni kell az eszközt a bérlőben.  Ezek a vezérlők csak [iOS- és Android-eszközökre alkalmazhatók.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) Ezek a vezérlők azonban nem alkalmazhatók b2B vendégfelhasználókra, ha a felhasználó eszközét már egy másik szervezet kezeli. Mobileszköz nem regisztrálható egyszerre egynél több bérlőben. Ha a mobileszközt egy másik szervezet kezeli, a felhasználó le lesz tiltva. Csak akkor regisztrálhatja az eszközét az erőforrás-bérlőben, ha a vendégfelhasználó eszköze nincs regisztrálva. A felhasználó ezután megfelelhet a hozzáférés-vezérlésnek.  

>[!NOTE]
>Külső felhasználók számára nem ajánlott alkalmazásvédelmi szabályzatot megkövetelni.

### <a name="location-based-conditional-access"></a>Helyalapú feltételes hozzáférés

Az [IP-tartományokon](../conditional-access/concept-conditional-access-conditions.md#locations) alapuló helyalapú szabályzat akkor kényszeríthető ki, ha a meghívó szervezet létrehozhat egy megbízható IP-címtartományt, amely meghatározza a partnerszervezeteket.

A házirendek a földrajzi helyek alapján is **kikényszeredhetőek.**

### <a name="risk-based-conditional-access"></a>Kockázatalapú feltételes hozzáférés

A [bejelentkezési kockázati szabályzat akkor](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) lép érvénybe, ha a B2B vendégfelhasználó megfelel a hozzáférés-vezérlésnek. Egy szervezet például megkövetelheti az Azure AD Multi-Factor Authentication használatát közepes vagy magas bejelentkezési kockázat esetén. Ha azonban egy felhasználó korábban még nem regisztrált az Azure AD Multi-Factor Authenticationre az erőforrás-bérlőben, a felhasználó le lesz tiltva. Ezzel megakadályozhatja, hogy rosszindulatú felhasználók regisztrálják a saját Azure AD Multi-Factor Authentication hitelesítő adataikat arra az esetre, ha veszélyeztetné egy megbízható felhasználó jelszavát.

A [felhasználói kockázati szabályzat](../conditional-access/concept-conditional-access-conditions.md#user-risk) azonban nem oldható fel az erőforrás-bérlőben. Ha például jelszóváltozásra van szükség a magas kockázatú vendégfelhasználók számára, azok le lesznek tiltva, mert nem lehet új jelszavakat visszaállítani az erőforráskönyvtárban.

### <a name="conditional-access-client-apps-condition"></a>Feltételes hozzáférés ügyfélalkalmazások feltétele

[Az ügyfélalkalmazások feltételei](../conditional-access/concept-conditional-access-conditions.md#client-apps) ugyanúgy viselkednek a B2B vendégfelhasználók esetében, mint bármely más típusú felhasználó esetében. Megakadályozhatja például, hogy a vendégfelhasználók régi hitelesítési protokollokat használjanak.

### <a name="conditional-access-session-controls"></a>Feltételes hozzáférés munkamenet-vezérlői

[A munkamenet-vezérlők](../conditional-access/concept-conditional-access-session.md) ugyanúgy viselkednek a B2B vendégfelhasználók esetén, mint bármely más felhasználótípus esetén.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg az Azure AD B2B-együttműködésről a következő cikkeket:

- [Mi az az Azure AD B2B együttműködés?](./what-is-b2b.md)
- [Identity Protection- és B2B-felhasználók](../identity-protection/concept-identity-protection-b2b.md)
- [Az External Identities díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
- [Gyakori kérdések (GYIK)](./faq.md)
