---
title: Feltételes hozzáférés VÁLLALATKÖZI együttműködési felhasználók számára – Azure AD
description: Megtudhatja, hogyan kényszerítheti ki a többtényezős hitelesítési házirendeket Azure Active Directory B2B-felhasználók számára.
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
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646282"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Feltételes hozzáférés VÁLLALATKÖZI együttműködéssel rendelkező felhasználók számára

Ez a cikk azt ismerteti, hogyan használhatók a szervezetek a VÁLLALATKÖZI vendég felhasználói számára az erőforrásokhoz való hozzáférésre vonatkozó feltételes hozzáférési (CA-) szabályzatokkal.
>[!NOTE]
>Ez a hitelesítési vagy engedélyezési folyamat egy kicsit eltér a vendég felhasználóknál, mint az adott személyazonosság-szolgáltató (identitásszolgáltató) meglévő felhasználóinál.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Hitelesítési folyamat a VÁLLALATKÖZI vendég felhasználói számára külső címtárból

Az alábbi ábrán a flow: ![ kép mutatja a hitelesítési folyamatot a B2B vendég felhasználók számára egy külső címtárból](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Lépés | Description |
|--------------|-----------------------|
| 1. | A B2B vendég felhasználó egy erőforráshoz való hozzáférést kér. Az erőforrás átirányítja a felhasználót az erőforrás-bérlőre, egy megbízható identitásszolgáltató.|
| 2. | Az erőforrás-bérlő külsőként azonosítja a felhasználót, és átirányítja a felhasználót a B2B vendég felhasználó identitásszolgáltató. A felhasználó elsődleges hitelesítést hajt végre a identitásszolgáltató.
| 3. | A B2B vendég felhasználó identitásszolgáltató kiállít egy jogkivonatot a felhasználó számára. A rendszer visszairányítja a felhasználót az erőforrás-bérlőhöz a jogkivonattal. Az erőforrás-bérlő érvényesíti a jogkivonatot, majd kiértékeli a felhasználót a HITELESÍTÉSSZOLGÁLTATÓ házirendjeivel. Az erőforrás-bérlő például megkövetelheti, hogy a felhasználó Azure Active Directory (AD) Multi-Factor Authentication végezzen.
| 4. | Miután az összes erőforrás-bérlői HITELESÍTÉSSZOLGÁLTATÓI házirend teljesült, az erőforrás-bérlő kiadja a saját jogkivonatát, és átirányítja a felhasználót az erőforráshoz.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Hitelesítési folyamat a B2B vendég felhasználói számára egyszeri jelszóval

Az alábbi ábrán a folyamat látható: a ![ kép a vállalatközi vendég felhasználói számára egyszeri jelszóval rendelkező hitelesítési folyamatot mutatja.](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Lépés | Description |
|--------------|-----------------------|
| 1. |A felhasználó hozzáférést kér egy másik bérlő erőforrásához. Az erőforrás átirányítja a felhasználót az erőforrás-bérlőre, egy megbízható identitásszolgáltató.|
| 2. | Az erőforrás-bérlő azonosítja a felhasználót egy [külső e-mail-jelszó (OTP) felhasználóként](./one-time-passcode.md) , és e-mailt küld a felhasználónak az egyszeri jelszavas hitelesítéssel.|
| 3. | A felhasználó lekéri az OTP-t, és elküldi a kódot. Az erőforrás-bérlő kiértékeli a felhasználót a HITELESÍTÉSSZOLGÁLTATÓI házirendek alapján.
| 4. | Ha minden HITELESÍTÉSSZOLGÁLTATÓI házirend teljesül, az erőforrás-bérlő kiállít egy jogkivonatot, és átirányítja a felhasználót az erőforráshoz. |

>[!NOTE]
>Ha a felhasználó egy külső erőforrás-bérlőből származik, nem lehetséges, hogy a B2B vendég felhasználó identitásszolgáltató HITELESÍTÉSSZOLGÁLTATÓI házirendjeit is ki kell értékelni. Napjainkban csak az erőforrás-bérlő HITELESÍTÉSSZOLGÁLTATÓI házirendjei érvényesek a vendégek rendelkezésére.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD-Multi-Factor Authentication B2B-felhasználók számára

A szervezetek több Azure AD-Multi-Factor Authentication szabályzatot is kikényszerítenek a B2B vendég felhasználói számára. Ezeket a szabályzatokat a bérlő, az alkalmazás vagy az egyéni felhasználói szint is érvényesítheti ugyanúgy, ahogy a teljes munkaidőben dolgozó alkalmazottak és a szervezet tagjai számára engedélyezve vannak.
Az erőforrás-bérlő minden esetben az Azure AD-Multi-Factor Authentication felelős a felhasználók számára, még akkor is, ha a vendég felhasználó szervezete rendelkezik Multi-Factor Authentication képességekkel. Íme egy példa –

1. A fabrikam nevű vállalat rendszergazdájának vagy információinak dolgozója egy másik, contoso nevű vállalattól hívja meg a felhasználót, hogy az alkalmazás Woodgrove használják.

2. A fabrikam-beli Woodgrove alkalmazás úgy van konfigurálva, hogy az Azure AD-Multi-Factor Authentication hozzáférésre van szükség.

3. Amikor a contoso vendég felhasználója megpróbál hozzáférni a fabrikam-bérlő Woodgrove, a rendszer megkéri az Azure AD Multi-Factor Authentication Challenge befejezésére.

4. A vendég felhasználó Ezután beállíthatja az Azure AD Multi-Factor Authentication a fabrikam használatával, és kiválaszthatja a beállításokat.

5. Ez a forgatókönyv bármilyen identitás – Azure AD vagy személyes Microsoft-fiók (MSA) esetében működik. Ha például a contoso felhasználója a közösségi azonosító használatával hitelesíti magát.

6. A fabrikam-nek elegendő prémium szintű Azure AD-licenccel kell rendelkeznie az Azure AD Multi-Factor Authentication támogatásához. A contoso felhasználója ezt a licencet használja a fabrikam-től. A B2B licenceléssel kapcsolatos információkért tekintse meg az [Azure ad külső identitások számlázási modelljét](./external-identities-pricing.md) .

>[!NOTE]
>Az Azure AD Multi-Factor Authentication az erőforrás-kiszolgálás révén biztosítja a kiszámíthatóságot.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD-Multi-Factor Authentication beállítása B2B-felhasználók számára

Az Azure AD Multi-Factor Authentication a B2B együttműködés felhasználói számára való beállításához tekintse meg ezt a videót:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-felhasználók Azure AD-Multi-Factor Authentication az ajánlat beváltásához

Ha többet szeretne megtudni az Azure AD Multi-Factor Authentication beváltási élményről, tekintse meg ezt a videót:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Azure AD-Multi-Factor Authentication alaphelyzetbe állítása B2B-felhasználók számára

Most a következő PowerShell-parancsmagok érhetők el a B2B vendég felhasználóinak ellenőrzéséhez:

1. Csatlakozás az Azure AD szolgáltatáshoz

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Az összes felhasználó beolvasása a proof up metódusokkal

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Alább bemutatunk egy példát:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Állítsa alaphelyzetbe az Azure AD Multi-Factor Authentication metódust egy adott felhasználó számára, hogy a B2B Collaboration-felhasználó megkövetelje a tesztelési módszerek újbóli beállítását. 
   Alább bemutatunk egy példát:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Feltételes hozzáférés a B2B-felhasználók számára

Számos tényező befolyásolja a B2B vendég felhasználói számára a HITELESÍTÉSSZOLGÁLTATÓI házirendeket.

### <a name="device-based-conditional-access"></a>Eszközalapú feltételes hozzáférés

A CA-ban lehetőség van arra, hogy megkövetelje a felhasználó [eszközének megfelelőségi vagy hibrid Azure ad-hez való csatlakoztatását](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). A B2B vendég felhasználók csak akkor tudják kielégíteni a megfelelőséget, ha az erőforrás-bérlő felügyelheti az eszközét. Az eszközöket egyszerre csak egy szervezet felügyelheti. A B2B vendég felhasználói nem tudják kielégíteni a hibrid Azure AD-csatlakozást, mert nem rendelkeznek helyszíni AD-fiókkal. Csak akkor, ha a vendég felhasználó eszköze nem felügyelt, regisztrálhatja vagy regisztrálhatja az eszközét az erőforrás-bérlőben, majd megteheti az eszköz megfelelőségét. A felhasználó ezután eleget tehet az engedélyezési vezérlőnek.

>[!Note]
>A külső felhasználók felügyelt eszközének megkövetelése nem ajánlott.

### <a name="mobile-application-management-policies"></a>Mobilalkalmazás-kezelési szabályzatok

A HITELESÍTÉSSZOLGÁLTATÓ olyan vezérlőket biztosít, mint például a **jóváhagyott ügyfélalkalmazások megkövetelése** , és az **alkalmazás-védelmi szabályzatok megkövetelése** szükséges az eszköz regisztrálásához a bérlőben. Ezeket a vezérlőket csak [iOS-és Android-eszközökön](../conditional-access/concept-conditional-access-conditions.md#device-platforms)lehet alkalmazni. Ezek a vezérlőelemek azonban nem alkalmazhatók a B2B vendég felhasználóira, ha a felhasználó eszközét már egy másik szervezet felügyeli. Egy mobileszköz nem regisztrálható egyszerre egynél több bérlőn. Ha a mobileszköz felügyeletét egy másik szervezet felügyeli, a rendszer letiltja a felhasználót. Csak akkor regisztrálja az eszközt az erőforrás-bérlőn, ha a vendég felhasználó eszköze nem felügyelt. A felhasználó ezután eleget tehet az engedélyezési vezérlőnek.  

>[!NOTE]
>A külső felhasználók számára nem ajánlott az alkalmazás védelmi szabályzatának megkövetelése.

### <a name="location-based-conditional-access"></a>Hely-alapú feltételes hozzáférés

Az IP-címtartományok alapján megadható, [hogy a](../conditional-access/concept-conditional-access-conditions.md#locations) meghívó szervezet létrehozhat-e olyan megbízható IP-címtartományt, amely meghatározza a partner szervezeteiket.

A házirendek a **földrajzi helyszínek** alapján is érvényben lehetnek.

### <a name="risk-based-conditional-access"></a>Kockázatalapú feltételes hozzáférés

A [bejelentkezési kockázati házirend](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) érvénybe lép, ha a B2B vendég felhasználója eleget tesz az engedélyezési vezérlőnek. Előfordulhat például, hogy egy szervezetnek közepes vagy magas szintű bejelentkezési kockázatra van szüksége az Azure AD Multi-Factor Authentication. Ha azonban egy felhasználó korábban még nem regisztrált az Azure AD-Multi-Factor Authentication az erőforrás-bérlőben, a felhasználó le lesz tiltva. Ezzel megakadályozható, hogy a rosszindulatú felhasználók regisztrálják saját Azure AD-Multi-Factor Authentication hitelesítő adataikat abban az esetben, ha a felhasználó jelszavát veszélyeztetik.

A [felhasználói kockázati házirendet](../conditional-access/concept-conditional-access-conditions.md#user-risk) azonban nem lehet feloldani az erőforrás-bérlőben. Ha például a nagy kockázatú vendég felhasználók jelszavának módosítására van szükség, akkor a rendszer letiltja a jelszavaknak az erőforrás-címtárban való alaphelyzetbe állítására való képtelenség miatt.

### <a name="conditional-access-client-apps-condition"></a>Feltételes hozzáférésű ügyfélalkalmazások alkalmazási feltétele

Az [ügyfélalkalmazások feltételei](../conditional-access/concept-conditional-access-conditions.md#client-apps) ugyanúgy viselkednek a B2B vendég felhasználói számára, mint bármely más típusú felhasználó esetében. Megakadályozhatja például, hogy a vendég felhasználók örökölt hitelesítési protokollokat használjanak.

### <a name="conditional-access-session-controls"></a>Feltételes hozzáférésű munkamenet-vezérlők

A [munkamenet-vezérlők](../conditional-access/concept-conditional-access-session.md) ugyanúgy viselkednek a B2B vendég felhasználói számára, mint bármely más típusú felhasználó esetében.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg az Azure AD B2B együttműködés alábbi cikkeit:

- [Mi az az Azure AD B2B együttműködés?](./what-is-b2b.md)
- [Identity Protection- és B2B-felhasználók](../identity-protection/concept-identity-protection-b2b.md)
- [Az External Identities díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
- [Gyakori kérdések (GYIK)](./faq.md)