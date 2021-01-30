---
title: A közzétevő ellenőrzésének hibakeresése | Azure
titleSuffix: Microsoft identity platform
description: Ismerteti, hogyan lehet elhárítani a Microsoft Identity platform közzétevői ellenőrzését Microsoft Graph API-k meghívásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 080d7c29a9e6950117fe475445458a84fb092e94
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090204"
---
# <a name="troubleshoot-publisher-verification"></a>Közzétevő ellenőrzésének hibaelhárítása
Ha nem tudja befejezni a folyamatot, vagy nem várt viselkedést tapasztal a [közzétevő ellenőrzésekor](publisher-verification-overview.md), akkor a következő lépésekkel kell kezdenie, ha hibákat kap, vagy nem várt viselkedés jelenik meg: 

1. Tekintse át a [követelményeket](publisher-verification-overview.md#requirements) , és győződjön meg arról, hogy az összes teljesült.

1. Tekintse át az [alkalmazás közzétevőként való megjelölésének](mark-app-as-publisher-verified.md) utasításait, és győződjön meg arról, hogy az összes lépés sikeresen elvégezve.

1. Tekintse át a [gyakori problémák](#common-issues)listáját.

1. A kérelem ismételt előállítása a [Graph Explorerrel](#making-microsoft-graph-api-calls) további információk összegyűjtéséhez és a felhasználói felületen felmerülő problémák kizárásához.

## <a name="common-issues"></a>Gyakori problémák
Az alábbiakban néhány gyakori probléma merülhet fel, amelyek a folyamat során fordulhatnak elő. 

- **Nem tudom a Microsoft Partner Network AZONOSÍTÓját (MPN-AZONOSÍTÓját), vagy nem tudom, ki a fiók elsődleges kapcsolattartója** 
    1. Navigáljon az [MPN-regisztráció oldalra](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Bejelentkezés felhasználói fiókkal a szervezeti elsődleges Azure AD-bérlőben 
    1. Ha egy MPN-fiók már létezik, a rendszer felismeri, és hozzáadja a fiókhoz. 
    1. Lépjen a [partner profil oldalra](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , ahol megjelenik az MPN-azonosító és az elsődleges fiók kapcsolata

- **Nem tudom, kik az Azure AD globális rendszergazdája (más néven vállalati rendszergazda vagy bérlői rendszergazda), hogyan találom meg őket? Mi a helyzet az alkalmazás-rendszergazda vagy a Felhőbeli alkalmazás rendszergazdájával?**
    1. Jelentkezzen be az [Azure ad-portálra](https://aad.portal.azure.com) a szervezete elsődleges bérlője felhasználói fiókjának használatával
    1. Navigáljon a [szerepkör-kezelés](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) elemre
    1. Kattintson a kívánt rendszergazdai szerepkörre
    1. Megjelenik a szerepkörhöz hozzárendelt felhasználók listája

- **Nem tudom, kik az MPN-fiókomhoz tartozó rendszergazda (k)** Nyissa meg az [MPN felhasználói kezelés lapot](https://partner.microsoft.com/pcv/users) , és a felhasználók listájának szűrésével tekintse meg, hogy a felhasználók milyen rendszergazdai szerepkörökkel rendelkeznek.

- **Hibaüzenet jelenik meg, hogy az MPN-azonosító érvénytelen, vagy nem fér hozzá.**
    1. Lépjen a [partner profiljához](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , és ellenőrizze, hogy: 
        - Az MPN-azonosító helyes. 
        - Nincsenek hibák vagy "függőben lévő műveletek", valamint a jogi üzleti profilban és a partneri adatokban található ellenőrzési állapot "engedélyezett" vagy "sikeres".
    1. Nyissa meg az [MPN bérlői felügyelet oldalt](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) , és ellenőrizze, hogy az alkalmazás regisztrálva van-e a bérlőben, és hogy felhasználói fiókkal van-e aláírva a társított bérlők listáján. Ha további bérlőt kell felvennie, kövesse az [itt](/partner-center/multi-tenant-account)található utasításokat. Vegye figyelembe, hogy minden Ön által hozzáadott bérlő globális rendszergazdája globális rendszergazdai jogosultságokat kap a partner Center-fiókjában.
    1. Nyissa meg az [MPN-felhasználó felügyelete lapot](https://partner.microsoft.com/pcv/users) , és erősítse meg, hogy a bejelentkezéshez használt felhasználó globális rendszergazda, MPN-rendszergazda vagy rendszergazdai fiók. Ha felhasználót kell hozzáadnia egy szerepkörhöz a partner Centerben, kövesse az [alábbi](/partner-center/create-user-accounts-and-set-permissions)utasításokat.

- **Az Azure AD-portálra való bejelentkezéskor nem jelenik meg a regisztrált alkalmazások egyike sem. miért?** 
    Előfordulhat, hogy az alkalmazás regisztrációja egy másik felhasználói fiókkal lett létrehozva ebben a bérlőben, egy személyes/fogyasztói fiókban vagy egy másik bérlőben. Győződjön meg arról, hogy a megfelelő fiókkal van bejelentkezve abban a bérlőben, amelyben az alkalmazás regisztrációja létrejött.

- **A multi-Factor Authentication szolgáltatással kapcsolatos hibaüzenetet kapok. Mit tegyek?** 
    Győződjön meg arról, hogy a [többtényezős hitelesítés](../fundamentals/concept-fundamentals-mfa-get-started.md) engedélyezve van, és **szükséges** ahhoz a felhasználóhoz, amelyhez be van jelentkezve, és ehhez a forgatókönyvhöz be kell jelentkezni. Az MFA például a következő lehet:
    - Mindig szükséges ahhoz a felhasználóhoz, amelyhez bejelentkezik
    - [Az Azure-felügyelethez szükséges](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Azon rendszergazda típusához szükséges](../conditional-access/howto-conditional-access-policy-admin-mfa.md) , amelyhez be van jelentkezve.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API-hívások készítése 

Ha probléma merül fel, de nem tudja megérteni, hogy mit lát a felhasználói felületen, érdemes lehet további hibaelhárítást végezni, ha Microsoft Graph hívásokat hajt végre az alkalmazás regisztrációs portálján végrehajtható műveletek végrehajtásához.

Ezeknek a kéréseknek a legegyszerűbb módja a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használata. Érdemes lehet más lehetőségeket is megfontolni, például a [Poster](https://www.postman.com/)használatát, vagy a PowerShell használatával [webes kérést meghívni](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

A Microsoft Graph az alkalmazás ellenőrzött közzétevőjét is beállíthatja és törölheti, és az eredményt az alábbi műveletek egyikének végrehajtása után ellenőrizheti. Az eredmény az alkalmazás regisztrációjának megfelelő [Application](/graph/api/resources/application) objektumon és az alkalmazásból létrehozott [egyszerű szolgáltatásokon](/graph/api/resources/serviceprincipal) is látható. Az objektumok közötti kapcsolattal kapcsolatos további információkért lásd: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](app-objects-and-service-principals.md).  

Példák néhány hasznos kérelemre:  

### <a name="set-verified-publisher"></a>Ellenőrzött közzétevő beállítása 

Kérés

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Reagálás 
```
204 No Content 
```
> [!NOTE]
> a *verifiedPublisherID* az MPN-azonosító. 

### <a name="unset-verified-publisher"></a>Ellenőrzött közzétevő nem lett beállítva 

Kérés:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Reagálás 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Ellenőrzött közzétevői adatok beolvasása az alkalmazásból 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Ellenőrzött közzétevői adatok beolvasása az egyszerű Szolgáltatásnévtől 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Hiba leírása 

Az alábbi lista felsorolja a lehetséges hibakódokat, amelyeket a Microsoft Graph vagy az alkalmazás regisztrációs portálon végzett hibaelhárítás során kaphat.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

A megadott MPN-azonosító ( `MPNID` ) nem létezik, vagy Önnek nincs hozzáférése. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra.
    
Leggyakrabban a bejelentkezett felhasználó okozta, hogy nem tagja a megfelelő szerepkörnek az MPN-fiókhoz a partner Centerben – lásd a jogosult szerepkörök listájára vonatkozó [követelményeket](publisher-verification-overview.md#requirements) , és további információt a [gyakori problémák](#common-issues) című részben talál. Azt is okozhatja, hogy az alkalmazás regisztrálva van, és nem kerül be az MPN-fiókba, vagy érvénytelen MPN-azonosító van.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

A megadott MPN-azonosító ( `MPNID` ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra.
    
A leggyakrabban akkor következik be, ha egy olyan MPN-azonosító van megadva, amely egy partneri hely fiókjának (PLA) felel meg. Csak a partner globális fiókjai támogatottak. További részletekért tekintse meg a [partner Center-fiók struktúráját](/partner-center/account-structure) .

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

A megadott MPN-azonosító ( `MPNID` ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra.
    
Leggyakrabban a nem megfelelő MPN-azonosító okozta.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

A megadott MPN-azonosító ( `MPNID` ) nem fejeződött be az átvett folyamaton. Fejezze be ezt a folyamatot a partner Centerben, és próbálkozzon újra. 
    
Általában az okozza, hogy az MPN-fiók nem végezte el az [ellenőrzési](/partner-center/verification-responses) folyamatot.

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

A megadott MPN-azonosító ( `MPNID` ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 
   
Leggyakrabban a nem megfelelő MPN-azonosító okozta.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

A megadott MPN-azonosító ( `MPNID` ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra.
    
Leggyakrabban a nem megfelelő MPN-azonosító okozta.

### <a name="applicationnotfound"></a>ApplicationNotFound

A célalkalmazás ( `AppId` ) nem található. Érvényes alkalmazás-azonosítót adjon meg, majd próbálkozzon újra.
    
A leggyakrabban a Graph APIon keresztüli ellenőrzés végrehajtásakor, a megadott alkalmazás azonosítója pedig helytelen. Megjegyzés – az alkalmazás azonosítóját meg kell adni, nem pedig a AppId/ClientId.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Ez a képesség Azure AD B2C bérlő esetében nem támogatott.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Ez a funkció nem támogatott e-mailben ellenőrzött bérlőn.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

A célalkalmazás ( `AppId` ) közzétevő tartományi készlettel kell rendelkeznie. Állítsa be a közzétevő tartományát, és próbálkozzon újra.

Akkor következik be, amikor a [közzétevő tartomány](howto-configure-publisher-domain.md) nincs konfigurálva az alkalmazásban.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

A célalkalmazás közzétevő tartománya ( `publisherDomain` ) nem egyezik az e-mail-ellenőrzés végrehajtásához használt tartománnyal a partner Centerben ( `pcDomain` ). Győződjön meg arról, hogy ezek a tartományok egyeznek, és próbálkozzon újra. 
    
Akkor következik be, amikor sem az alkalmazás [közzétevője](howto-configure-publisher-domain.md) , sem az Azure ad-bérlőhöz hozzáadott [egyéni tartomány](../fundamentals/add-custom-domain.md) nem egyezik az e-mail-ellenőrzés a partner Centerben való végrehajtásához használt tartománnyal.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

Nem jogosult az ellenőrzött közzétevő tulajdonság beállítására az alkalmazásban (<`AppId` ) 
  
Leggyakrabban a bejelentkezett felhasználó okozta a megfelelő szerepkört az Azure AD-ben az MPN-fiókhoz – lásd a jogosult szerepkörök listájára vonatkozó [követelményeket](publisher-verification-overview.md#requirements) , és további információkat a [gyakori problémák](#common-issues) című részben talál.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

Az MPN-azonosító nem lett megadva a kérelem törzsében, vagy a kérelem tartalomtípusa nem "Application/JSON".

### <a name="msanotsupported"></a>MSANotSupported 

Ez a funkció Microsoft fogyasztói fiókok esetén nem támogatott. Csak az Azure AD-beli Azure AD-felhasználó által regisztrált alkalmazások támogatottak.

### <a name="interactionrequired"></a>InteractionRequired

Akkor következik be, amikor a többtényezős hitelesítés nem lett elvégezve, mielőtt a rendszer megpróbálta hozzáadni a hitelesített közzétevőt az alkalmazáshoz. További információért lásd: [gyakori problémák](#common-issues) . Megjegyzés: az MFA-t ugyanabban a munkamenetben kell végrehajtani, amikor ellenőrzött közzétevőt próbál hozzáadni. Ha az MFA engedélyezve van, de nem szükséges végrehajtani a munkamenetben, a kérelem sikertelen lesz. 

A megjelenített hibaüzenet a következő: "a rendszergazda által végzett konfiguráció módosítása miatt, vagy ha új helyre költözött át, a folytatáshoz a multi-Factor Authentication használata szükséges."

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

A megjelenített hibaüzenet a következő: "egy ellenőrzött közzétevő nem adható hozzá ehhez az alkalmazáshoz. Kérjen segítséget a rendszergazdától. "

Először is ellenőrizze, hogy teljesítette-e a [közzétevő ellenőrzési követelményeit](publisher-verification-overview.md#requirements).

Ha egy ellenőrzött közzétevő hozzáadására vonatkozó kérés készül, a biztonsági kockázatfelmérés elvégzéséhez számos jel használható. Ha a kérést kockázatos értékre állapítja meg, akkor a rendszer hibát ad vissza. Biztonsági okokból a Microsoft nem fedi le azokat a konkrét feltételeket, amelyek alapján megállapítható, hogy a kérés kockázatos-e.

## <a name="next-steps"></a>Következő lépések

Ha áttekintette az összes korábbi információt, és továbbra is a Microsoft Graph hibaüzenetet kap, gyűjtsön annyit az alábbi információk közül, amennyire csak lehetséges, és [forduljon a Microsoft ügyfélszolgálatához](developer-support-help-options.md#open-a-support-request).

- Időbélyeg 
- CorrelationId 
- A bejelentkezett felhasználó ObjectID vagy UserPrincipalName 
- Célalkalmazás ObjectId
- Célalkalmazás AppId
- TenantId, ahol az alkalmazás regisztrálva van
- MPN-azonosító
- REST-kérelem történt 
- Hibakód és visszaadott üzenet