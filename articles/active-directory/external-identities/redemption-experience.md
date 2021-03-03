---
title: Meghívás megváltása B2B-együttműködésben – Azure AD
description: Ismerteti az Azure AD B2B együttműködés meghívásának beváltási élményét a végfelhasználók számára, beleértve az adatvédelmi feltételeket is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c7ca826eaf7d72cb35985b154458f149ef4a0e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649315"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-együttműködésre irányuló meghívás beváltása

Ez a cikk bemutatja, hogyan érhetik el a vendég felhasználói az erőforrásokat és az azokhoz való belekapcsolódási folyamatot. Ha meghívó e-mailt küld a vendégnek, a meghívás tartalmaz egy hivatkozást, amely beválthat, hogy hozzáférjen az alkalmazáshoz vagy a portálhoz. A meghívás e-mail-címe csak egyike annak, hogy a vendégek hozzáférjenek az erőforrásokhoz. Másik lehetőségként hozzáadhat vendégeket a címtárhoz, és közvetlenül a megosztani kívánt portálra vagy alkalmazásra mutató hivatkozást adhat hozzájuk. Az általuk használt módszertől függetlenül a vendégek az első alkalommal történő belefoglalási folyamaton keresztül vezérlik. Ez a folyamat biztosítja, hogy a vendégek elfogadják az adatvédelmi feltételeket, és elfogadják a beállított használati [feltételeket](../conditional-access/terms-of-use.md) .

Amikor vendég felhasználót ad hozzá a címtárhoz, a vendég felhasználói fióknak van egy engedélyezési állapota (megtekinthető a PowerShellben), amely eredetileg **PendingAcceptance**-re van állítva. Ez a beállítás addig marad, amíg a vendég elfogadja a meghívót, és elfogadja az adatvédelmi szabályzatát és használati feltételeit. Ezt követően a hozzájárulási állapot **elfogadva** értékűre változik, és a jóváhagyás lapjai már nem jelennek meg a vendég számára.

   > [!IMPORTANT]
   > - **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.
   > - **Október 2021-től kezdve** a Microsoft nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Váltás és bejelentkezés közös végponton keresztül

A vendég felhasználók mostantól egy közös végponton (URL) keresztül jelentkezhetnek be a több-bérlős vagy a Microsoft-alkalmazásokba `https://myapps.microsoft.com` . Korábban egy közös URL-cím átirányítja a vendég felhasználót a saját otthoni bérlőre az erőforrás-bérlő helyett a hitelesítéshez, így szükség van egy bérlőre vonatkozó hivatkozásra (például `https://myapps.microsoft.com/?tenantid=<tenant id>` ). Most, hogy a vendég felhasználó megtekintheti az alkalmazás közös URL-címét, válassza a **bejelentkezési beállítások lehetőséget**, majd válassza a **Bejelentkezés a szervezetbe** lehetőséget. A felhasználó ezután beírja a szervezet nevét.

![Gyakori végpont-bejelentkezés](media/redemption-experience/common-endpoint-flow-small.png)

Ezt követően a rendszer átirányítja a felhasználót a bérlői végpontra, ahol bejelentkezhet az e-mail-címükkel, vagy kijelölhet egy Ön által konfigurált identitás-szolgáltatót.
## <a name="redemption-through-a-direct-link"></a>Váltás közvetlen kapcsolaton keresztül

A meghívót tartalmazó e-mail-cím vagy egy alkalmazás közös URL-címének alternatívájaként a vendég közvetlen hivatkozást adhat az alkalmazáshoz vagy a portálhoz. Először hozzá kell adnia a vendég felhasználót a címtárhoz a [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) vagy a [PowerShell](./b2b-quickstart-invite-powershell.md)használatával. Ezután bármilyen [testreszabható módszert használhat az alkalmazások felhasználók számára történő központi telepítéséhez](../manage-apps/end-user-experiences.md), beleértve a közvetlen bejelentkezési hivatkozásokat is. Ha egy vendég közvetlen hivatkozást használ a meghívó e-mail-címe helyett, a rendszer továbbra is az első belefoglalási élményt fogja követni.

> [!NOTE]
> A közvetlen hivatkozás a bérlőre jellemző. Más szóval magában foglalja a bérlői azonosítót vagy az ellenőrzött tartományt, így a vendég hitelesíthető a bérlőben, ahol a megosztott alkalmazás található. Íme néhány példa a bérlői környezettel kapcsolatos közvetlen hivatkozásokra:
 > - Alkalmazások hozzáférési panelje: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Az ellenőrzött tartományhoz tartozó alkalmazások hozzáférési panelje: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure Portal: `https://portal.azure.com/<tenant id>`
 > - Egyéni alkalmazás: lásd a [közvetlen bejelentkezési hivatkozás](../manage-apps/end-user-experiences.md#direct-sign-on-links) használatát ismertető témakört.

Vannak olyan esetek, amikor a meghívó e-mail-címe közvetlen hivatkozáson keresztül ajánlott. Ha ezek a speciális esetek fontosak a szervezet számára, javasoljuk, hogy a meghívó e-mailt még elküldő metódusok használatával meghívja a felhasználókat:
 - A felhasználónak nincs Azure AD-fiókja, MSA vagy e-mail-fiókja egy összevont szervezetben. Hacsak nem használja az egyszeri PIN-kódot, a vendégnek be kell váltania a meghívó e-mail-címét, hogy a MSA létrehozásának lépésein legyenek vezérelve.
 - Előfordulhat, hogy a meghívott felhasználói objektum nem rendelkezik e-mail-címmel, mert ütközik egy Contact objektummal (például egy Outlook Contact objektummal). Ebben az esetben a felhasználónak a meghívót tartalmazó e-mailben kell kattintania a beváltási URL-címre.
 - A felhasználó a meghívott e-mail-cím aliasával jelentkezhet be. (Az alias egy e-mail-fiókhoz társított e-mail-cím.) Ebben az esetben a felhasználónak a meghívót tartalmazó e-mailben kell kattintania a beváltási URL-címre.

## <a name="redemption-through-the-invitation-email"></a>Visszaváltás a meghívó e-mailben

Ha [a Azure Portal használatával](./b2b-quickstart-add-guest-users-portal.md)ad hozzá egy vendég felhasználót a címtárhoz, a rendszer meghívót küld a vendégnek a folyamatban. Azt is megteheti, hogy a [PowerShell használatával](./b2b-quickstart-invite-powershell.md) küldi el a meghívó e-maileket, ha vendég felhasználókat ad hozzá a címtárhoz. Itt látható a vendég felhasználói felületének leírása, amikor beváltják a hivatkozást az e-mailben.

1. A vendég a **Microsoft meghívótól** kapott [meghívó e-mailt](./invitation-email-elements.md) kap.
2. A vendég kiválasztja a **meghívót** az e-mailben.
3. A vendég a saját hitelesítő adatait fogja használni a címtárba való bejelentkezéshez. Ha a vendégnek nincs olyan fiókja, amely összevonható a címtárral, és nincs engedélyezve az [e-mail egyszeri jelszava (OTP)](./one-time-passcode.md) szolgáltatás. a vendég megkéri, hogy hozzon létre egy személyes [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) vagy egy [Azure ad](../enterprise-users/directory-self-service-signup.md)önkiszolgáló fiókot. A részletekért tekintse meg a [meghívás beváltási folyamatát](#invitation-redemption-flow) .
4. A vendég irányítását az alábbiakban ismertetett belefoglalási [élmény](#consent-experience-for-the-guest) vezérli.
## <a name="invitation-redemption-flow"></a>Meghívás beváltási folyamata

Ha a felhasználó a meghívás **elfogadása** hivatkozásra kattint egy [meghívás e-mailben](invitation-email-elements.md), az Azure ad automatikusan beváltja a meghívót a beváltási folyamat alapján, ahogy az alábbi ábrán látható:

![A beváltási folyamat diagramját ábrázoló képernyőfelvétel](media/redemption-experience/invitation-redemption-flow.png)

**Ha a felhasználó felhasználói alapelvének neve (UPN) megegyezik egy meglévő Azure AD-és személyes MSA-fiókkal, a rendszer megkéri a felhasználót, hogy válassza ki, hogy melyik fiókot szeretné beváltani.*

1. Az Azure AD felhasználói alapú felderítést végez annak megállapításához, hogy a felhasználó létezik-e egy [meglévő Azure ad-bérlőben](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Ha egy rendszergazda engedélyezte a [közvetlen összevonást](./direct-federation.md), az Azure ad ellenőrzi, hogy a felhasználó tartományának utótagja megfelel-e a konfigurált SAML/ws-fed identitás-szolgáltató tartományának, és átirányítja a felhasználót az előre konfigurált identitás-szolgáltatóhoz.

3. Ha egy rendszergazda engedélyezte a [Google-összevonást](./google-federation.md), az Azure ad ellenőrzi, hogy a felhasználó tartományának utótagja gmail.com vagy googlemail.com, és átirányítja a felhasználót a Google-ba.

4. A beváltási folyamat ellenőrzi, hogy a felhasználó rendelkezik-e meglévő személyes [Microsoft-fiókával (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

5. A felhasználó **kezdőkönyvtárának** azonosítása után a rendszer elküldi a felhasználót a megfelelő identitás-szolgáltatónak a bejelentkezéshez.  

6. Ha az 1 – 4. lépés nem talál kezdőkönyvtárat a meghívott felhasználó számára, akkor az Azure AD meghatározza, hogy a meghívó bérlő engedélyezte- [e az e-mailek egyszeri jelszavas (OTP)](./one-time-passcode.md) funkcióját a vendégek számára.

7. Ha a [vendégek számára engedélyezve van az egyszeri e-mail jelszó](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), a rendszer a meghívott e-mailben küldi el a jelszót a felhasználónak. A felhasználó beolvassa és megadja ezt a PIN-kódot az Azure AD bejelentkezési oldalán.

8. Ha a vendégeknek szóló e-mailek egyszeri jelszava le van tiltva, az Azure AD ellenőrzi a tartomány utótagját annak megállapításához, hogy az egy fogyasztói fiókhoz tartozik-e. Ha igen, a rendszer megkéri a felhasználót, hogy hozzon létre egy személyes [Microsoft-fiók](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Ha nem, a rendszer megkéri a felhasználót, hogy hozzon létre egy [Azure ad](../enterprise-users/directory-self-service-signup.md)önkiszolgáló fiókot.

9. Az Azure AD megpróbál létrehozni egy [Azure ad](../enterprise-users/directory-self-service-signup.md) önkiszolgáló fiókot az e-mailhez való hozzáférés ellenőrzésével. A fiók ellenőrzésének elvégzéséhez egy kódot kell elküldeni az e-mailbe, és a felhasználónak le kell kérnie és elküldeni az Azure AD-be. Ha azonban a meghívott felhasználó bérlője összevont, vagy ha a meghívott felhasználó bérlője hamis értékre van állítva, a felhasználó nem tudja befejezni a beváltást, és a folyamat hibát eredményezett AllowEmailVerifiedUsers. További információ: [Azure Active Directory B2B együttműködés hibaelhárítása](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. A rendszer megkéri a felhasználót, hogy hozzon létre egy személyes [Microsoft-fiók (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. A megfelelő identitás-szolgáltatóhoz való hitelesítés után a rendszer átirányítja a felhasználót az Azure AD-be a [beleegyező felhasználói élmény](#consent-experience-for-the-guest)befejezéséhez.  

Az igény szerinti (JIT) váltáshoz, ahol a beváltást egy bérlői alkalmazási hivatkozáson keresztül, a 8 – 10. lépés nem érhető el. Ha a felhasználó eléri a 6. lépést, és az egyszeri E-mail jelszó funkció nincs engedélyezve, a felhasználó hibaüzenetet kap, és nem tudja beváltani a meghívót. Ennek a hibának a megelőzése érdekében a rendszergazdáknak [engedélyezniük kell az e-mailek egyszeri](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) jelszavát, vagy a felhasználónak a Meghívási hivatkozásra kell kattintania.

## <a name="consent-experience-for-the-guest"></a>A vendég beleegyezett a felhasználói élménybe

Amikor egy vendég először jelentkezik be a partner szervezet erőforrásaihoz, a következő lapokon keresztül vezérelheti őket. 

1. A vendég **áttekinti a felülvizsgálati engedélyek** lapot, amely a meghívó szervezet adatvédelmi nyilatkozatát ismerteti. A felhasználóknak **el kell fogadniuk** az adataikat a meghívó szervezet adatvédelmi szabályzatai alapján a folytatáshoz.

   ![A felülvizsgálati engedélyek oldalát ábrázoló képernyőkép](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Arról, hogy a bérlői rendszergazda hogyan hivatkozhat a szervezete adatvédelmi nyilatkozatára, tekintse meg az [útmutató: a szervezet adatvédelmi adatainak hozzáadása Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Ha a használati feltételek konfigurálva vannak, a vendég megnyílik, és áttekinti a használati feltételeket, majd kiválasztja az **elfogadás** lehetőséget. 

   ![Az új használati feltételeket bemutató képernyőkép](media/redemption-experience/terms-of-use-accept.png) 

   A használati [feltételeket](../conditional-access/terms-of-use.md) **külső identitások**  >  **használati feltételek** is konfigurálhatja.

3. Ha másként nincs megadva, a rendszer átirányítja a vendéget az alkalmazások hozzáférési paneljére, amely felsorolja a vendég által elérhető alkalmazásokat.

   ![Az alkalmazások hozzáférési paneljét ábrázoló képernyőfelvétel](media/redemption-experience/myapps.png) 

A címtárban a vendég **meghívásának elfogadott** értéke **Igen** értékre változik. Ha létrejött egy MSA, a vendég **forrása** a Microsoft- **fiókot** jeleníti meg. További információ a vendég felhasználói fiók tulajdonságairól: [Azure ad B2B csoportmunka-felhasználó tulajdonságai](user-properties.md). 

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B Collaboration-felhasználók hozzáadása a Azure Portal](add-users-administrator.md)
- [Hogyan vehetik igénybe az Information Worker VÁLLALATKÖZI együttműködési felhasználókat a Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B csoportmunka-felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Szervezet elhagyása vendégfelhasználóként](leave-the-organization.md)