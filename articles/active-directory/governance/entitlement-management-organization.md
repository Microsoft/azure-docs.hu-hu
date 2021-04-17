---
title: Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságkezelésben – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a szervezeten kívüli személyeknek, hogy hozzáférési csomagokat kérjenek a projekteken való együttműködéshez.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532084"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságkezelésben

A Azure Active Directory (Azure AD) jogosultságkezelésével szervezeten kívüli emberekkel is együttműködhet. Ha gyakran dolgozik együtt külső Azure AD-címtárban vagy -tartományban található felhasználókkal, hozzáadhatja őket csatlakoztatott szervezetként. Ez a cikk bemutatja, hogyan adhat hozzá egy csatlakoztatott szervezetet, hogy lehetővé tegye a szervezeten kívüli felhasználók számára a címtárban található erőforrások igénylését.

## <a name="what-is-a-connected-organization"></a>Mi az a csatlakoztatott szervezet?

A csatlakoztatott szervezet egy másik szervezet, amelyhez kapcsolódik.  Ahhoz, hogy a szervezet felhasználói hozzáférjenek az erőforrásokhoz, például a SharePoint Online-webhelyekhez vagy -alkalmazásokhoz, szüksége lesz a szervezet adott címtárbeli felhasználóinak megfelelő ábrázolásra.  Mivel a legtöbb esetben a szervezet felhasználói még nem az Azure AD-címtárban vannak, a jogosultságkezelés segítségével szükség szerint behozhatja őket az Azure AD-címtárba.  

A jogosultságkezelés háromféleképpen teszi lehetővé a csatlakoztatott szervezetet alkotnak felhasználók megadását.  Ez lehet

* felhasználók egy másik Azure AD-címtárban,
* más, nem Azure AD-címtárban található, közvetlen összevonásra konfigurált felhasználók, vagy
* más, nem Azure AD-címtárban található felhasználók, akiknek az e-mail-címei azonos tartománynévvel vannak megegyezik.

Tegyük fel például, hogy a Woodgrove Banknál dolgozik, és két külső szervezettel szeretne együttműködni. A két szervezet konfigurációja eltérő:

- A Graphic Design Institute az Azure AD-t használja, és a felhasználóik egyszerű felhasználónevének vége a *graphicdesigninstitute.com.*
- A Contoso még nem használja az Azure AD-t. A Contoso-felhasználók egyszerű felhasználóneve a névre *végződik contoso.com.*

Ebben az esetben két csatlakoztatott szervezetet konfigurálhat. Létrehoz egy csatlakoztatott szervezetet a Graphic Design Institute-hoz, egyet pedig a Contosóhoz. Ha ezután hozzáadja a két csatlakoztatott szervezetet egy szabályzathoz, az egyes szervezetek felhasználói a szabályzatnak megfelelő egyszerű felhasználónévvel kérhetnek hozzáférési csomagokat. Az egyszerű felhasználónévvel és tartománynévvel contoso.com a Contoso által csatlakoztatott szervezettel egyeznek, és csomagokat is kérhetnek. Az egyszerű felhasználónévvel és tartománynévvel graphicdesigninstitute.com a Graphic Design Institute-hoz kapcsolódó szervezettel, és a felhasználók küldhetnek kérelmeket.  És mivel a Graphic Design Institute az Azure AD-t [](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) használja, a bérlőhöz hozzáadott ellenőrzött tartománynak megfelelő egyszerű névvel (például *graphicdesigninstitute.example)* szintén kérhetnek hozzáférési csomagokat ugyanazokkal a szabályzatokkal. Ha be van kapcsolva az e-mailek egyszeri jelszavas [(OTP)](../external-identities/one-time-passcode.md) hitelesítése, akkor ez azokat a tartományokat is magában foglalja, akik még nem rendelkezik Azure AD-fiókkal, és e-mailes egyszeri jelszavas hitelesítéssel hitelesítik magukat az erőforrások elérésekor. 

![Példa csatlakoztatott szervezetre](./media/entitlement-management-organization/connected-organization-example.png)

Az Azure AD-címtár vagy -tartomány felhasználóinak hitelesítése a hitelesítési típustól függ. A csatlakoztatott szervezetek hitelesítési típusai a következőek:

- Azure AD
- [Közvetlen összevonás](../external-identities/direct-federation.md)
- [Egyszeres PIN-kód](../external-identities/one-time-passcode.md) (tartomány)

A csatlakoztatott szervezet hozzáadásának bemutatóját az alábbi videóban szemlélteti:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Csatlakoztatott szervezet hozzáadása

Ha külső Azure AD-címtárat vagy -tartományt szeretne csatlakoztatott szervezetként hozzáadni, kövesse az ebben a szakaszban található utasításokat.

**Előfeltételként szükséges** szerepkör: *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal **válassza** a Azure Active Directory, majd az Identity **Governance lehetőséget.**

1. A bal oldali panelen válassza a **Csatlakoztatott szervezetek,** majd a **Csatlakoztatott szervezet hozzáadása lehetőséget.**

    ![A "Csatlakoztatott szervezet hozzáadása" gomb](./media/entitlement-management-organization/connected-organization.png)

1. Válassza az **Alapvető beállítások** lapot, majd adja meg a szervezet megjelenített nevét és leírását.

    ![A "Csatlakoztatott szervezet hozzáadása" Alapszintű panel](./media/entitlement-management-organization/organization-basics.png)

1. Új csatlakoztatott szervezet létrehozásakor  az állapot automatikusan Konfigurálva lesz. Az állapottulajdonságokkal kapcsolatos további információkért lásd: [Csatlakoztatott szervezetek állapottulajdonságai.](#state-properties-of-connected-organizations)

1. Válassza a **Címtár és tartomány** lapot, majd a Címtár és tartomány hozzáadása **lehetőséget.**

    Megnyílik **a Könyvtárak + tartományok kiválasztása** panel.

1. A keresőmezőbe írjon be egy tartománynevet az Azure AD-címtár vagy -tartomány kereséséhez. Mindenképpen adja meg a teljes tartománynevet.

1. Ellenőrizze, hogy a szervezet neve és hitelesítési típusa helyes-e. A felhasználók bejelentkezésének módja a hitelesítés típusától függ.

    ![A "Könyvtárak + tartományok kiválasztása" panel](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Az  Azure AD-címtár vagy -tartomány hozzáadásához válassza a Hozzáadás lehetőséget. Jelenleg csatlakoztatott szervezetenként csak egy Azure AD-címtárat vagy -tartományt adhat hozzá.

    > [!NOTE]
    > Az Azure AD-címtár vagy -tartomány összes felhasználója le tudja kérni ezt a hozzáférési csomagot. Ez magában foglalja az Azure AD-beli felhasználókat a címtárhoz társított összes altartományból, kivéve, ha az Azure AD business to Business to Business (B2B) engedélyezi vagy megtagadja ezeket a tartományokat. További információ: Meghívás engedélyezése vagy blokkolása adott szervezetek [B2B-felhasználói számára.](../external-identities/allow-deny-list.md)

1. Az Azure AD-címtár vagy -tartomány hozzáadása után válassza a **Kijelölés lehetőséget.**

    A szervezet megjelenik a listában.

    ![A "Címtár és tartomány" panel](./media/entitlement-management-organization/organization-directory-domain.png)

1. Válassza a **Szponzorok** lapot, majd adjon hozzá választható szponzorokat ehhez a csatlakoztatott szervezethez.

    A szponzorok olyan belső vagy külső felhasználók, akik már a címtárban vannak, és a kapcsolódó szervezettel való kapcsolatfelvételi pontot jelentik. A belső szponzorok a címtár tagfelhasználói. A külső szponzorok a csatlakoztatott szervezet olyan vendégfelhasználói, akik korábban meg voltak hívva, és már a címtárban vannak. A szponzorok jóváhagyókként használhatók, ha a csatlakoztatott szervezet felhasználói hozzáférést kérnek ehhez a hozzáférési csomaghoz. További információ a vendégfelhasználók címtárba való meghívásának mikéntjére: [Azure Active Directory B2B](../external-identities/add-users-administrator.md)együttműködési felhasználók hozzáadása a Azure Portal.

    A **Hozzáadás/eltávolítás lehetőség kiválasztásakor** megnyílik egy panel, amelyen belső vagy külső szponzorokat választhat. A panel a címtárban található felhasználók és csoportok szűretlen listáját jeleníti meg.

    ![A Szponzorok panel](./media/entitlement-management-organization/organization-sponsors.png)

1. Válassza az **Áttekintés + létrehozás** lapot, tekintse át a szervezeti beállításokat, majd válassza a Létrehozás **lehetőséget.**

    ![Az "Áttekintés + létrehozás" panel](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Csatlakoztatott szervezet frissítése 

Ha a csatlakoztatott szervezet egy másik tartományra változik, a szervezet neve megváltozik, vagy Ha módosítani szeretné a szponzorokat, frissítheti a csatlakoztatott szervezetet az ebben a szakaszban található utasítások szerint.

**Előfeltételként szükséges** szerepkör: *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal **válassza** a Azure Active Directory, majd az Identity **Governance lehetőséget.**

1. A bal oldali panelen válassza a **Csatlakoztatott szervezetek** lehetőséget, majd válassza ki a csatlakoztatott szervezetet annak megnyitásához.

1. A csatlakoztatott szervezet áttekintési panelen válassza a **Szerkesztés** lehetőséget a szervezet nevének, leírásának vagy állapotának a szerkesztéséhez.  

1. A Címtár **és tartomány panelen** válassza a **Címtár és** tartomány frissítése lehetőséget egy másik címtárra vagy tartományra való váltáshoz.

1. A **Szponzorok panelen** válassza a **Belső** szponzorok hozzáadása vagy a **Külső** szponzorok hozzáadása lehetőséget egy felhasználó szponzorként való hozzáadásához. A szponzor eltávolításához válassza ki a szponzort, majd a jobb oldali panelen válassza a **Törlés lehetőséget.**


## <a name="delete-a-connected-organization"></a>Csatlakoztatott szervezet törlése

Ha már nincs kapcsolata egy külső Azure AD-címtárral vagy -tartománnyal, törölheti a csatlakoztatott szervezetet.

**Előfeltétel-szerepkör:** *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal **válassza** a Azure Active Directory, majd az Identity **Governance lehetőséget.**

1. A bal oldali panelen válassza a **Csatlakoztatott szervezetek** lehetőséget, majd válassza ki a csatlakoztatott szervezetet annak megnyitásához.

1. A csatlakoztatott szervezet áttekintési paneljének törléséhez válassza a **Törlés** lehetőséget.

    ![A csatlakoztatott szervezet Törlés gombja](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Csatlakoztatott szervezet programozott kezelése

A csatlakoztatott szervezeteket létrehozhatja, listába sorolhatja, frissítheti és törölheti is a Microsoft Graph. A megfelelő szerepkörben lévő, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók az API-t hívva kezelhetik a csatlakoztatottSzervezeti objektumokat, és szponzorokat állíthatnak `EntitlementManagement.ReadWrite.All` be hozzájuk. [](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

## <a name="state-properties-of-connected-organizations"></a>A csatlakoztatott szervezetek állapottulajdonságai

Az Azure AD-jogosultságkezelésben jelenleg két különböző típusú állapottulajdonság létezik a csatlakoztatott szervezetek számára, amelyek konfigurálva és javasoltak: 

- A konfigurált csatlakoztatott szervezet egy teljesen működőképes csatlakoztatott szervezet, amely lehetővé teszi a szervezeten belüli felhasználók számára a hozzáférési csomagokhoz való hozzáférést. Amikor egy rendszergazda létrehoz egy új csatlakoztatott szervezetet a Azure Portal, az alapértelmezés szerint konfigurált állapotban lesz, mivel a rendszergazda létrehozta és használni szeretné ezt a csatlakoztatott szervezetet.  Emellett amikor egy csatlakoztatott szervezetet programozott módon hoz létre az API-n keresztül, az alapértelmezett állapotot konfigurálni kell, **kivéve,** ha explicit módon egy másik állapotra van beállítva. 

    A konfigurált csatlakoztatott szervezetek a csatlakoztatott szervezetek kiválasztóiban fognak jelenni, és az "összes" csatlakoztatott szervezetet célzó szabályzatok hatókörében lesznek.

- A javasolt csatlakoztatott szervezet olyan csatlakoztatott szervezet, amely automatikusan lett létrehozva, de nem rendelkezik rendszergazdai jogosultságokkal a szervezet létrehozásához vagy jóváhagyásához. Amikor egy felhasználó egy konfigurált csatlakoztatott szervezeten kívüli hozzáférési csomagra jelentkezik,  az automatikusan létrehozott csatlakoztatott szervezetek a javasolt állapotban lesznek, mivel a bérlő egyetlen rendszergazdája sem állította be ezt a partnerséget. 
    
    A javasolt csatlakoztatott szervezetek egyik szabályzatra sem vonatkoznak az "összes konfigurált csatlakoztatott szervezet" beállítás hatókörében, de csak adott szervezetekre vonatkozó szabályzatok esetén használhatók. 

Csak a konfigurált csatlakoztatott szervezetek felhasználói kérhetnek az összes konfigurált szervezet felhasználói számára elérhető hozzáférési csomagokat. A javasolt csatlakoztatott szervezetek felhasználói úgy tapasztalják a felhasználói élményt, mintha ehhez a tartományhoz nem lenne csatlakoztatott szervezet; csak az adott szervezetre vagy bármely felhasználóra vonatkozó hozzáférési csomagokat láthatja és kérheti.

> [!NOTE]
> Az új funkció használatának részeként a 09/09/20 előtt létrehozott összes csatlakoztatott szervezet **konfiguráltnak minősül.** Ha olyan hozzáférési csomaggal rendelkezik, amely bármely szervezet felhasználói számára lehetővé teszi a regisztrációt, tekintse át az adott dátum előtt létrehozott csatlakoztatott szervezetek listáját, és győződjön meg arról, hogy egyik sem lett a konfiguráltnak megfelelően kategorizálva.  A rendszergazdák szükség szerint **frissítheti a State** tulajdonságot. Útmutatásért lásd: [Csatlakoztatott szervezet frissítése.](#update-a-connected-organization)

## <a name="next-steps"></a>Következő lépések

- [Külső felhasználók hozzáférésének szabályozása](./entitlement-management-external-users.md)
- [A címtárban nem található felhasználók hozzáférésének szabályozása](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
