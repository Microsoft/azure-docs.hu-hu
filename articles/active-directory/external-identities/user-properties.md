---
title: B2B vendég felhasználó tulajdonságai – Azure Active Directory | Microsoft Docs
description: A B2B vendég felhasználói tulajdonságainak és állapotának Azure Active Directory a meghívás beváltásához
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e9189b6608b74be6a72dbdfe66276c5050f042f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365463"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egy Azure Active Directory B2B csoportmunka-felhasználó tulajdonságai

Ez a cikk a B2B vendég felhasználói objektumának tulajdonságait és állapotát ismerteti Azure Active Directoryban (Azure AD) a meghívás megkezdése előtt és után. Az Azure AD vállalatközi (B2B) együttműködési felhasználója a UserType = Guest felhasználó. A vendég felhasználó általában egy partner szervezettől származik, és a Meghívási könyvtárban alapértelmezés szerint korlátozott jogosultságokkal rendelkezik.

A meghívó szervezet igényeitől függően az Azure AD B2B csoportmunka-felhasználó a következő fiókok egyikében lehet:

- 1. állapot: az Azure AD külső példányán, a meghívóban pedig vendég felhasználóként jelennek meg. Ebben az esetben a B2B-felhasználó a meghívott bérlőhöz tartozó Azure AD-fiókkal jelentkezik be. Ha a partnerszervezet nem használja az Azure AD-t, a vendég felhasználó az Azure AD-ben továbbra is létrejön. A követelmények az, hogy beváltják a meghívót, és az Azure AD ellenőrzi az e-mail-címüket. Ezt a megállapodást az igény szerinti (JIT) bérletnek vagy a "vírus" bérletnek is nevezik.

   > [!IMPORTANT]
   > **Október 2021-től kezdve** a Microsoft nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

- 2. állapot: a Microsoft vagy más fiókban található, és vendég felhasználóként képviselteti magát a gazdagépen. Ebben az esetben a vendég felhasználó egy Microsoft-fiók vagy egy közösségi fiókkal jelentkezik be (google.com vagy hasonló). A meghívott felhasználó identitása Microsoft-fiókként jön létre a meghívó szervezet címtárában az ajánlat beváltásakor.

- 3. állapot: a gazda szervezet helyszíni Active Directoryja, és szinkronizálva van a gazdagép szervezetének Azure AD-vel. Az Azure AD Connect segítségével szinkronizálhatja a partneri fiókokat a felhőbe Azure AD B2B-felhasználókként a UserType = Guest használatával. Lásd: [a helyileg felügyelt partneri fiókok hozzáférésének biztosítása a felhőalapú erőforrásokhoz](hybrid-on-premises-to-cloud.md).

- 4. állapot: a gazdagép szervezete Azure AD-beli UserType = vendég és a gazdagép által felügyelt hitelesítő adatok.

  ![A négy felhasználói állapotot ábrázoló diagram](media/user-properties/redemption-diagram.png)


Most lássuk, mi az Azure AD B2B együttműködés felhasználója az Azure AD-ben.

### <a name="before-invitation-redemption"></a>Meghívás beváltása előtt

Az 1. és a 2. állapotú fiókok a vendég felhasználók saját hitelesítő adataival való együttműködésre való felhívásának eredményei. Amikor a rendszer először elküldi a meghívót a vendég felhasználónak, a rendszer létrehoz egy fiókot a címtárban. Ez a fiók nem rendelkezik a hozzá társított hitelesítő adatokkal, mert a hitelesítést a vendég felhasználójának személyazonosság-szolgáltatója végzi. A címtár vendég felhasználói fiókjának **forrás** tulajdonsága a **meghívott felhasználóra** van beállítva. 

![A felhasználói tulajdonságokat bemutató képernyőfelvétel az ajánlat megváltása előtt](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Meghívás beváltását követően

Miután a vendég felhasználó elfogadta a meghívót, a **forrás** tulajdonság a vendég felhasználó identitás-szolgáltatója alapján frissül.

Az 1. állapotú vendég felhasználók esetében  a forrás **külső Azure Active Directory**.

![1. állapotú vendég felhasználó az ajánlat megváltása után](media/user-properties/after-redemption-state1.png)

A 2. állapotú vendég felhasználók esetében a **forrás** a **Microsoft-fiók**.

![Állapot 2 vendég felhasználó az ajánlat megváltása után](media/user-properties/after-redemption-state2.png)

A 3. állapotú és a 4. állapotú vendég felhasználók esetében a **forrás** tulajdonság értéke **Azure Active Directory** vagy a **Windows Server Active Directory**, a következő szakaszban leírtak szerint.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Az Azure AD B2B csoportmunka-felhasználó legfontosabb tulajdonságai
### <a name="usertype"></a>UserType (Felhasználótípus)
Ez a tulajdonság jelzi a felhasználó kapcsolatát a gazdagép bérletével. Ennek a tulajdonságnak két értéke lehet:
- Tag: ez az érték a gazda szervezet alkalmazottját és a szervezet bérszámfejtésében lévő felhasználót jelöli. Ez a felhasználó például arra vár, hogy csak a belső webhelyekhez férhessen hozzá. Ez a felhasználó nem minősül külső közreműködőnek.

- Vendég: ez az érték azt a felhasználót jelöli, aki a vállalatnak nem minősül belsőnek, például külső közreműködő, partner vagy ügyfél. Egy ilyen felhasználónak nem kell elfogadnia a VEZÉRIGAZGATÓ belső feljegyzését, vagy például a vállalattól kapott előnyöket.

  > [!NOTE]
  > A UserType nem kapcsolódik a felhasználó bejelentkezése, a felhasználó címtárbeli szerepköre és így tovább. Ez a tulajdonság csak azt jelzi, hogy a felhasználó kapcsolódik a gazdagéphez, és lehetővé teszi, hogy a szervezet olyan házirendeket kényszerítse ki, amelyek ettől a tulajdonságtól függenek.

A díjszabással kapcsolatos részletekért lásd a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Forrás
Ez a tulajdonság azt jelzi, hogy a felhasználó hogyan jelentkezik be.

- Meghívott felhasználó: Ez a felhasználó meghívást kapott, de még nem váltott ki meghívót.

- Külső Azure Active Directory: Ez a felhasználó külső szervezetnek van felhasználva, és egy másik szervezethez tartozó Azure AD-fiók használatával hitelesíti magát. Ez a bejelentkezési típus az 1. állapotnak felel meg.

- Microsoft-fiók: Ez a felhasználó egy Microsoft-fiók, és egy Microsoft-fiók használatával hitelesíti magát. Ez a bejelentkezési típus a 2. állapotnak felel meg.

- Windows Server Active Directory: Ez a felhasználó a szervezethez tartozó helyszíni Active Directoryba van bejelentkezve. Ez a bejelentkezési típus a 3. állapotnak felel meg.

- Azure Active Directory: Ez a felhasználó egy, a szervezethez tartozó Azure AD-fiók használatával hitelesíti magát. Ez a bejelentkezési típus a 4. állapotnak felel meg.
  > [!NOTE]
  > A forrás-és a UserType független tulajdonságok. A forrás értéke nem a UserType adott értékét jelenti.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Hozzáadhatók az Azure AD B2B-felhasználók a vendégek helyett tagként?
Az Azure AD B2B-felhasználók és a vendég felhasználók általában szinonimák. Ezért az Azure AD B2B együttműködés felhasználója alapértelmezés szerint a UserType = Guest értékkel rendelkező felhasználóként lesz hozzáadva. Bizonyos esetekben azonban a partner szervezet egy nagyobb szervezet tagja, amelyhez a gazda szervezet is tartozik. Ebben az esetben előfordulhat, hogy a gazda szervezet a partner szervezeten belüli felhasználókat a vendég helyett tagként szeretné kezelni. Az Azure AD B2B meghívót használó API-k segítségével tagként adhat hozzá vagy hívhat meg egy felhasználót a partnervállalat számára a gazda szervezethez.

## <a name="filter-for-guest-users-in-the-directory"></a>A címtárban lévő vendég felhasználók szűrése

![A vendég felhasználók szűrőjét ábrázoló képernyőfelvétel](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType konvertálása
A UserType a PowerShell használatával lehetséges a tagokról vendégre konvertálni, és fordítva. A UserType tulajdonság azonban a felhasználó és a szervezet közötti kapcsolatot jelöli. Ezért ezt a tulajdonságot csak akkor kell módosítania, ha a felhasználó és a szervezet közötti kapcsolat megváltozik. Ha megváltozik a felhasználó kapcsolata, az egyszerű felhasználónevet (UPN) kell megváltoztatnia? A felhasználónak továbbra is hozzá kell férnie ugyanahhoz az erőforráshoz? Ki kell rendelni egy postaládát? Nem javasoljuk, hogy módosítsa a UserType a PowerShell használatával atomi tevékenységként. Abban az esetben is, ha ez a tulajdonság a PowerShell használatával is változhat, nem javasoljuk, hogy ezen értéktől függ.

## <a name="remove-guest-user-limitations"></a>Vendég felhasználói korlátozások eltávolítása
Előfordulhatnak olyan esetek, amikor a vendég felhasználóknak magasabb szintű jogosultságokat kíván adni. Bármely szerepkörhöz hozzáadhat egy vendég felhasználót, és akár el is távolíthatja az alapértelmezett vendég felhasználói korlátozásokat a címtárban, hogy a felhasználók ugyanazokat a jogosultságokat adja meg, mint a tagok.

Kikapcsolhatja az alapértelmezett korlátozásokat annak érdekében, hogy a vállalati könyvtárban lévő vendég felhasználó ugyanazokkal az engedélyekkel rendelkezik, mint a tag felhasználója.

![A felhasználói beállításokban a külső felhasználók lehetőséget ábrázoló képernyőkép](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Elérhetővé tehetem vendég felhasználókat az Exchange globális címlistában?
Igen. Alapértelmezés szerint a vendég objektumok nem láthatók a szervezet globális címeinek listájában, de a Azure Active Directory PowerShell használatával láthatóvá teheti őket. Részletekért lásd: a vendég **objektumok megjelenítése a globális címlistában?** a [vendég hozzáférés kezelése Microsoft 365 csoportokban](/office365/admin/create-groups/manage-guest-access-in-groups).

## <a name="can-i-update-a-guest-users-email-address"></a>Frissíthetem a vendég felhasználó e-mail-címét?

Ha a vendég felhasználói elfogadják a meghívót, és később módosítják az e-mail-címüket, az új e-mail nem szinkronizálódik automatikusan a címtár vendég felhasználói objektumával. A mail tulajdonságot [Microsoft Graph API](/graph/api/resources/user)-n keresztül hozza létre a rendszer. A mail tulajdonságot a Microsoft Graph API, az Exchange felügyeleti központ vagy az [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser)segítségével frissítheti. A módosítás az Azure AD vendég felhasználói objektumában jelenik meg.

## <a name="next-steps"></a>Következő lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B együttműködési felhasználói jogkivonatok](user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](claims-mapping.md)
