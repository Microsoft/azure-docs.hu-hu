---
title: Azure Active Directory Connect Health műveletek
description: Ez a cikk további műveleteket ismertet, amelyek az üzembe helyezésük után Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 836c7bf9aefd4b2cb7d52c66bbd37e7ba38a467c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377310"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health műveletek
Ez a témakör a Azure Active Directory (Azure AD) Connect Health használatával végrehajtható különböző műveleteket ismerteti.

## <a name="enable-email-notifications"></a>E-mail-értesítések engedélyezése
A szolgáltatás konfigurálható úgy Azure AD Connect Health e-mailes értesítéseket küldjön, amikor a riasztások azt jelzik, hogy az identitás-infrastruktúra nem megfelelő állapotú. Ez riasztás generálása és feloldása esetén fordul elő.

![Képernyőkép Azure AD Connect Health e-mail értesítési beállításairól](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Az e-mail-értesítések alapértelmezés szerint engedélyezve vannak.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>E-Azure AD Connect Health engedélyezése
1. Az Azure Portalon keressen rá az Azure AD Connect Health
2. Szinkronizálási **hibák kiválasztása**
3. Válassza az **Értesítési beállítások lehetőséget.**
5. Az e-mail-értesítési kapcsolónál válassza a **BE lehetőséget.**
6. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy minden globális rendszergazda e-mailes értesítéseket kap.
7. Ha más e-mail-címekről szeretne e-mail-értesítéseket kapni, adja meg őket a További e-mail **címzettek mezőben.** Ha el szeretne távolítani egy e-mail-címet a listából, kattintson a jobb gombbal a bejegyzésre, és válassza a **Törlés lehetőséget.**
8. A módosítások végleges mentéshez kattintson a **Mentés gombra.** A módosítások csak a mentés után lépnek életbe.

>[!NOTE] 
> Ha problémák lépnek fel a szinkronizálási kérések feldolgozásakor a háttérszolgáltatásban, a szolgáltatás e-mailt küld a hiba részleteiről a bérlő rendszergazdai e-mail-címére vagy e-mail-címére. Ügyfeleink visszajelzést kaptunk arról, hogy bizonyos esetekben ezeknek az üzeneteknek a mennyisége tiltottan nagy, ezért megváltoztatjuk az üzenetek küldésének módját. 
>
> Ahelyett, hogy minden egyes alkalommal üzenetet küldetünk volna minden egyes szinkronizálási hibáról, a háttérszolgáltatás által visszaadott összes hiba napi kivonatát küldjük el. Ez lehetővé teszi az ügyfelek számára a hibák hatékonyabb feldolgozását, és csökkenti a duplikált hibaüzenetek számát.

## <a name="delete-a-server-or-service-instance"></a>Kiszolgáló vagy szolgáltatáspéldány törlése

>[!NOTE] 
> A törlési lépésekhez prémium szintű Azure AD-licenc szükséges.

Bizonyos esetekben előfordulhat, hogy el szeretne távolítani egy kiszolgálót a figyelásból. A következőt kell tudni ahhoz, hogy eltávolítson egy kiszolgálót a Azure AD Connect Health szolgáltatásból.

Amikor töröl egy kiszolgálót, vegye figyelembe a következőket:

* Ez a művelet leállítja a további adatok gyűjtését a kiszolgálóról. Ez a kiszolgáló el lesz távolítva a monitorozási szolgáltatásból. A művelet után nem fogja tudni megtekinteni a kiszolgáló új riasztási, monitorozási és használati elemzési adatait.
* Ez a művelet nem távolítja el az állapotügynöket a kiszolgálóról. Ha a lépés végrehajtása előtt nem távolította el az állapotügynöket, a kiszolgálón az állapotügynökkel kapcsolatos hibákat láthat.
* Ez a művelet nem törli a kiszolgálóról már összegyűjtött adatokat. Az adatok az Azure adatmegőrzési szabályzatának megfelelően törlődnek.
* Ha a művelet végrehajtása után újra szeretné monitorozást végezni ugyanazon a kiszolgálón, el kell távolítania és újra kell telepítenie az állapotügynöket ezen a kiszolgálón.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Kiszolgáló törlése a Azure AD Connect Health szolgáltatásból

>[!NOTE] 
> A törlési lépésekhez Prémium szintű Azure AD-licenc szükséges.

Azure AD Connect Health a Active Directory összevonási szolgáltatások (AD FS) (AD FS) és a Azure AD Connect (Szinkronizálás) hez:

1. Nyissa meg **a Kiszolgáló** panelt a **Kiszolgálólista panelről** az eltávolítani kívánt kiszolgálónév kiválasztásával.
2. A Kiszolgáló **panel** műveletsávjában kattintson a Törlés **elemre.**
![Képernyőkép a Azure AD Connect Health törléséről](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Erősítse meg a kiszolgáló nevét a megerősítési mezőben.
4. Kattintson a **Törlés** gombra.

Azure AD Connect Health a Azure Active Directory Domain Services:

1. Nyissa meg **a Tartományvezérlők irányítópultot.**
2. Válassza ki az eltávolítani kívánt tartományvezérlőt.
3. A műveletsávon kattintson a **Kijelölt törlése elemre.**
4. Erősítse meg a kiszolgáló törlésére vonatkozó műveletet.
5. Kattintson a **Törlés** gombra.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Szolgáltatáspéldány törlése a Azure AD Connect Health szolgáltatásból
Bizonyos esetekben érdemes lehet eltávolítani egy szolgáltatáspéldányt. A következő okkal távolíthat el egy szolgáltatáspéldányt a Azure AD Connect Health szolgáltatásból.

Szolgáltatáspéldány törlésekor vegye figyelembe a következőket:

* Ez a művelet eltávolítja az aktuális szolgáltatáspéldányt a monitorozási szolgáltatásból.
* Ez a művelet nem távolítja el vagy távolítja el az állapotügynöket a szolgáltatáspéldány részeként figyelt kiszolgálók egyik kiszolgálóról sem. Ha a lépés végrehajtása előtt nem távolította el az állapotügynöket, a kiszolgálókon az állapotügynökkel kapcsolatos hibákat láthat.
* A szolgáltatáspéldány összes adata törlődik az Azure adatmegőrzési szabályzatának megfelelően.
* Ha a művelet végrehajtása után el szeretné kezdeni a szolgáltatás monitorozását, távolítsa el és telepítse újra az állapotügynöket az összes kiszolgálón. Ha a művelet végrehajtása után újra szeretné monitorozást végezni ugyanazon a kiszolgálón, távolítsa el, telepítse újra és regisztrálja az állapotügynöket a kiszolgálón.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Szolgáltatáspéldány törlése a Azure AD Connect Health szolgáltatásból
1. Nyissa meg **a Szolgáltatás** panelt a **Szolgáltatáslista panelen** az eltávolítani kívánt szolgáltatásazonosító (farm neve) kiválasztásával. 
2. A Szolgáltatás **panel műveletsávjában** kattintson a **Törlés elemre.** 
![Képernyőkép a Azure AD Connect Health törléséről](./media/how-to-connect-health-operations/DeleteServer.png)
3. A megerősítési mezőbe írja be a szolgáltatás nevét (például: sts.contoso.com).
4. Kattintson a **Törlés** gombra.
   <br><br>

[//]: # (Az RBAC szakasz kezdete)
## <a name="manage-access-with-azure-rbac"></a>Hozzáférés kezelése az Azure RBAC használatával
[Az Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) Azure AD Connect Health hozzáférést biztosít a globális rendszergazdáktól különböző felhasználók és csoportok számára. Az Azure RBAC hozzárendeli a szerepköröket a kívánt felhasználókhoz és csoportokhoz, és egy olyan mechanizmust biztosít, amely korlátozza a címtár globális rendszergazdáit.

### <a name="roles"></a>Szerepkörök
Azure AD Connect Health a következő beépített szerepköröket támogatja:

| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |A *tulajdonosok* kezelhetik a hozzáférést (például szerepkört rendelhetnek egy felhasználóhoz vagy *csoporthoz),* megtekinthetik az összes információt (például a riasztásokat) a portálon, és módosíthatják a beállításokat *(például* az e-mail-értesítéseket) a Azure AD Connect Health. <br>Alapértelmezés szerint az Azure AD globális rendszergazdáihoz ez a szerepkör van hozzárendelve, és ez nem módosítható. |
| Közreműködő |A közreműködők *megtekinthetik az* összes információt (például a riasztásokat) a portálon, és módosíthatják a *beállításokat* (például az e-mail-értesítéseket) a Azure AD Connect Health. |
| Olvasó |Az olvasók *megtekinthetik az összes információt* (például a riasztásokat) a portálon a Azure AD Connect Health. |

Az összes többi szerepkör (például a felhasználói hozzáférés-rendszergazdák vagy a DevTest Labs-felhasználók) nem befolyásolják a hozzáférést a Azure AD Connect Health-ban, még akkor sem, ha a szerepkörök elérhetők a portálon.

### <a name="access-scope"></a>Hozzáférési hatókör
Azure AD Connect Health két szinten támogatja a hozzáférések felügyeletét:

* **Minden szolgáltatáspéldány:** Ez az ajánlott elérési út a legtöbb esetben. Ez szabályozza az összes szolgáltatáspéldány (például egy AD FS farm) hozzáférését az összes szerepkörtípusra, amelyet a Azure AD Connect Health.
* **Szolgáltatáspéldány:** Bizonyos esetekben szükség lehet a hozzáférés szerepkörtípusok vagy szolgáltatáspéldányok alapján való elkülönítésre. Ebben az esetben a hozzáférést a szolgáltatáspéldány szintjén kezelheti.  

Az engedély akkor adható meg, ha a végfelhasználó címtár- vagy szolgáltatáspéldány-szinten rendelkezik hozzáféréssel.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Felhasználók vagy csoportok hozzáférésének engedélyezése Azure AD Connect Health
A következő lépések azt mutatják be, hogyan engedélyezheti a hozzáférést.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. lépés: A megfelelő hozzáférési hatókör kiválasztása
Ha engedélyeznie kell  egy felhasználó hozzáférését a szolgáltatáspéldányok Azure AD Connect Health szintjén, nyissa meg a fő panelt a Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. lépés: Felhasználók és csoportok hozzáadása és szerepkörök hozzárendelése
1. A **Konfigurálás szakaszban** kattintson a Felhasználók **elemre.**<br>
   ![Képernyőkép az Azure AD Connect Health oldalsávról](./media/how-to-connect-health-operations/startRBAC.png)
2. Válassza a **Hozzáadás** lehetőséget.
3. A Szerepkör **kiválasztása panelen** válasszon ki egy szerepkört (például **Tulajdonos).**<br>
   ![Képernyőkép a Azure AD Connect Health És az Azure RBAC konfigurálás menüjéről](./media/how-to-connect-health-operations/RBAC_add.png)
4. Írja be a megcélzott felhasználó vagy csoport nevét vagy azonosítóját. Egyszerre egy vagy több felhasználót vagy csoportot is kijelölhet. Kattintson a **Kiválasztás** elemre.
   ![Képernyőkép a Azure AD Connect Health azure-beli szerepkörlistáról](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Válassza az **OK** lehetőséget.<br>
6. A szerepkör-hozzárendelés befejezése után a felhasználók és csoportok megjelennek a listában.<br>
   ![Képernyőkép a Azure AD Connect Health kiemelt Azure RBAC-ről és új felhasználókról](./media/how-to-connect-health-operations/RBAC_user_list.png)

A felsorolt felhasználók és csoportok most már a hozzárendelt szerepköröknek megfelelően hozzáféréssel is rendelkezik.

> [!NOTE]
> * A globális rendszergazdák mindig teljes hozzáféréssel rendelkezik az összes művelethez, de a globális rendszergazdai fiókok nem megjelenik az előző listában.
> * A Felhasználók meghívása funkció nem támogatott a Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. lépés: A panel helyének megosztása felhasználókkal vagy csoportokkal
1. Az engedélyek hozzárendelése után a felhasználók a következő Azure AD Connect Health férhetnek [hozzá.](https://aka.ms/aadconnecthealth)
2. A panelen a felhasználó rögzítheti a panelt vagy annak különböző részeit az irányítópulton. Egyszerűen kattintson a Rögzítés **az irányítópulton ikonra.**<br>
   ![Képernyőkép a Azure AD Connect Health Azure RBAC rögzítési paneljről, kiemelt rögzítés ikonnal](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> A hozzárendelt Olvasó szerepkörű felhasználók nem Azure AD Connect Health a bővítményt a Azure Marketplace. A felhasználó nem tudja végrehajtani a szükséges "létrehozási" műveletet. A felhasználó továbbra is az előző hivatkozásra kattintva használhatja a panelt. Későbbi használat esetén a felhasználó rögzítheti a panelt az irányítópulton.
>
>

### <a name="remove-users-or-groups"></a>Felhasználók vagy csoportok eltávolítása
Eltávolíthat egy felhasználót vagy csoportot, amely hozzá van adva a Azure AD Connect Health Azure RBAC-hez. Egyszerűen kattintson a jobb gombbal a felhasználóra vagy csoportra, és válassza az **Eltávolítás lehetőséget.**<br>
![Képernyőkép a Azure AD Connect Health Azure RBAC-ről az Eltávolítás kiemeléssel](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC-szakasz vége)

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health ügynök telepítése](how-to-connect-health-agent-install.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health használata szinkronizáláshoz](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
