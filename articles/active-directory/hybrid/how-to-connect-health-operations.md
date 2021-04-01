---
title: Azure Active Directory Connect Health műveletek
description: Ez a cikk azokat a további műveleteket ismerteti, amelyek a Azure AD Connect Health üzembe helyezése után végezhetők el.
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
ms.openlocfilehash: 37e41a00c1489d5b7b4822fec8bb4da35e917826
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686461"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health műveletek
Ez a témakör az Azure Active Directory (Azure AD) kapcsolati állapotának használatával végrehajtható különböző műveleteket ismerteti.

## <a name="enable-email-notifications"></a>E-mail-értesítések engedélyezése
A Azure AD Connect Health szolgáltatás beállítható úgy, hogy e-mail-értesítéseket küldjön, ha a riasztások azt jelzik, hogy az identitás-infrastruktúra nem kifogástalan. Ez akkor fordul elő, ha riasztás jön létre, és megoldódott.

![Képernyőkép a Azure AD Connect Health e-mail értesítési beállításairól](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Az e-mail-értesítések alapértelmezés szerint engedélyezve vannak.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health e-mail-értesítések engedélyezése
1. Nyissa meg a **riasztások** panelt ahhoz a szolgáltatáshoz, amelyhez e-mailes értesítést szeretne kapni.
2. A műveleti sávon kattintson az **értesítési beállítások** elemre.
3. Az e-mail értesítési kapcsolónál válassza **a** be lehetőséget.
4. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy az összes globális rendszergazda fogadja az e-mailes értesítéseket.
5. Ha más e-mail-címekről szeretne e-mail-értesítéseket kapni, akkor a **további e-mail-címzettek** mezőben adhatja meg azokat. Ha el szeretne távolítani egy e-mail-címet ebből a listáról, kattintson a jobb gombbal a bejegyzésre, és válassza a **Törlés** lehetőséget.
6. A módosítások véglegesítéséhez kattintson a **Mentés** gombra. A módosítások csak a mentés után lépnek életbe.

>[!NOTE] 
> Ha probléma merül fel a szinkronizálási kérések feldolgozásakor a háttér-szolgáltatásban, ez a szolgáltatás értesítő e-mailt küld a bérlő adminisztratív kapcsolattartási e-mail-címére (i). Meghallottuk az ügyfelek visszajelzéseit, hogy bizonyos esetekben az üzenetek mennyisége megfizethetetlenül nagy, így az üzenetek elküldésének módját módosítjuk. 
>
> Ahelyett, hogy minden alkalommal elküldje az üzenetet minden szinkronizálási hibához, a háttér-szolgáltatás által visszaadott összes hiba napi kivonatát küldi el a rendszer. Ez lehetővé teszi, hogy az ügyfelek hatékonyabban dolgozzák fel ezeket a hibákat, és csökkentik az ismétlődő hibaüzenetek számát.

## <a name="delete-a-server-or-service-instance"></a>Kiszolgáló vagy szolgáltatás példányának törlése

>[!NOTE] 
> A törlési lépésekhez prémium szintű Azure AD-licenc szükséges.

Bizonyos esetekben előfordulhat, hogy el szeretné távolítani a kiszolgálót a figyelésből. A következő lépésekkel tudnia kell, hogyan távolíthat el egy kiszolgálót a Azure AD Connect Health szolgáltatásból.

A kiszolgálók törlésekor ügyeljen a következőkre:

* Ez a művelet leállítja az adott kiszolgálóról származó további adatok gyűjtését. A rendszer eltávolítja a kiszolgálót a figyelési szolgáltatásból. A művelet elvégzése után nem tudja megtekinteni a kiszolgáló új riasztásait, figyelési vagy használati elemzési adatait.
* Ez a művelet nem távolítja el az állapotfigyelő ügynököt a kiszolgálóról. Ha nem távolítja el az állapotfigyelő ügynököt a lépés végrehajtása előtt, akkor a kiszolgálón található állapotfigyelő ügynökhöz kapcsolódó hibák jelenhetnek meg.
* Ez a művelet nem törli a kiszolgálóról gyűjtött adatokat. Az adatok törlődnek az Azure adatmegőrzési szabályzatának megfelelően.
* Miután elvégezte a műveletet, ha újra meg szeretné kezdeni ugyanazt a kiszolgálót, el kell távolítania és újra kell telepítenie az állapotfigyelő ügynököt ezen a kiszolgálón.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Kiszolgáló törlése a Azure AD Connect Health szolgáltatásból

>[!NOTE] 
> A törlési lépésekhez prémium szintű Azure AD-licenc szükséges.

Azure AD Connect Health a Active Directory összevonási szolgáltatások (AD FS) (AD FS) és a Azure AD Connect (Sync) számára:

1. Az eltávolítandó kiszolgálónév kiválasztásával nyissa meg a **kiszolgáló** panelt a **kiszolgáló lista** paneljén.
2. A **kiszolgáló** paneljén, a műveleti sávon kattintson a **Törlés** elemre.
![Képernyőkép a Azure AD Connect Health delete Serverről](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Erősítse meg a kiszolgáló nevének beírásával a megerősítő mezőben.
4. Kattintson a **Törlés** gombra.

Azure Active Directory Domain Services Azure AD Connect Health:

1. Nyissa meg a **tartományvezérlők** irányítópultot.
2. Válassza ki az eltávolítandó tartományvezérlőt.
3. A műveleti sávon kattintson a **Kijelöltek törlése** elemre.
4. Erősítse meg a kiszolgáló törlésére irányuló műveletet.
5. Kattintson a **Törlés** gombra.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Szolgáltatási példány törlése Azure AD Connect Health szolgáltatásból
Bizonyos esetekben érdemes lehet eltávolítani egy szolgáltatási példányt. A következő lépésekkel tudnia kell, hogyan távolíthat el egy szolgáltatási példányt a Azure AD Connect Health szolgáltatásból.

A szolgáltatási példányok törlésekor vegye figyelembe a következőket:

* Ez a művelet eltávolítja az aktuális szolgáltatási példányt a figyelési szolgáltatásból.
* Ez a művelet nem távolítja el és nem távolítja el az állapotfigyelő ügynököt a szolgáltatási példány részeként figyelt kiszolgálókról. Ha nem távolítja el az állapotfigyelő ügynököt a lépés végrehajtása előtt, akkor a kiszolgálókon található állapotfigyelő ügynökhöz kapcsolódó hibák jelenhetnek meg.
* A szolgáltatási példány összes adatait az Azure adatmegőrzési szabályzatának megfelelően törli a rendszer.
* Ha elvégezte a műveletet, a szolgáltatás figyelésének megkezdéséhez távolítsa el, majd telepítse újra az állapotfigyelő ügynököt az összes kiszolgálón. Miután elvégezte a műveletet, ha újra meg szeretné kezdeni ugyanazt a kiszolgálót, távolítsa el, telepítse újra, majd regisztrálja az állapotfigyelő ügynököt az adott kiszolgálón.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Szolgáltatási példány törlése a Azure AD Connect Health szolgáltatásból
1. Nyissa meg **a szolgáltatás** panelt a **szolgáltatások listájának** paneljén az eltávolítani kívánt szolgáltatás-azonosító (Farm neve) kiválasztásával. 
2. A **szolgáltatás** panelen, a műveleti sávon kattintson a **Törlés** elemre. 
![Képernyőkép a Azure AD Connect Health delete szolgáltatásról](./media/how-to-connect-health-operations/DeleteServer.png)
3. Erősítse meg a szolgáltatás nevének beírásával a megerősítő mezőben (például: sts.contoso.com).
4. Kattintson a **Törlés** gombra.
   <br><br>

[//]: # (A RBAC szakasz kezdete)
## <a name="manage-access-with-azure-rbac"></a>Hozzáférés kezelése az Azure RBAC
A Azure AD Connect Health [Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) hozzáférést biztosít a felhasználók és csoportok számára a globális rendszergazdáktól eltérő csoportokhoz. Az Azure RBAC szerepköröket rendel hozzá a kívánt felhasználókhoz és csoportokhoz, és lehetővé teszi a címtárban lévő globális rendszergazdák korlátozását.

### <a name="roles"></a>Szerepkörök
Azure AD Connect Health a következő beépített szerepköröket támogatja:

| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |A tulajdonosok *kezelhetik a hozzáférést* (például hozzárendelhet egy szerepkört egy felhasználóhoz vagy csoportjához), *megtekintheti az összes információt* (például a riasztások megtekintése) a portálról, és *módosíthatja a beállításokat* (például az e-mailes értesítéseket) a Azure ad Connect Healthon belül. <br>Alapértelmezés szerint az Azure AD globális rendszergazdái ehhez a szerepkörhöz vannak rendelve, és ez nem módosítható. |
| Közreműködő |A közreműködők megtekinthetik az *összes információt* (például a riasztások megtekintése) a portálról, és *módosíthatják a beállításokat* (például az e-mailes értesítéseket) a Azure ad Connect Healthon belül. |
| Olvasó |Az olvasók *megtekinthetik az összes információt* (például a riasztások megtekintése) a portálon Azure ad Connect Healthon belül. |

Az összes többi szerepkör (például a felhasználói hozzáférés-rendszergazdák vagy a DevTest Labs-felhasználók) nem érinti a Azure AD Connect Healthon belüli hozzáférést, még akkor is, ha a szerepkörök elérhetők a portálon.

### <a name="access-scope"></a>Hozzáférési hatókör
A Azure AD Connect Health két szinten támogatja a hozzáférések kezelését:

* **Minden szolgáltatási példány**: ez az ajánlott elérési út a legtöbb esetben. Az összes szolgáltatási példány (például egy AD FS Farm) hozzáférését szabályozza a Azure AD Connect Health által figyelt összes szerepkör esetében.
* **Service-példány**: bizonyos esetekben előfordulhat, hogy a hozzáférést a szerepkör típusa vagy a szolgáltatási példány alapján kell elkülöníteni. Ebben az esetben a hozzáférést a szolgáltatás példányának szintjén kezelheti.  

Az engedély akkor adható meg, ha egy végfelhasználó hozzáfér a címtár vagy a szolgáltatás példányának szintjéhez.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Felhasználók vagy csoportok hozzáférésének engedélyezése Azure AD Connect Health
A következő lépések bemutatják, hogyan engedélyezhető a hozzáférés.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. lépés: válassza ki a megfelelő hozzáférési hatókört
Ha engedélyezni szeretné, hogy a felhasználói hozzáférés a Azure AD Connect Health *összes szolgáltatási példányának* szintjén megtörténjen, nyissa meg Azure ad Connect Health fő paneljét.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. lépés: felhasználók és csoportok hozzáadása és szerepkörök társítása
1. A **Konfigurálás** szakaszban kattintson a **felhasználók** elemre.<br>
   ![Képernyőkép a Azure AD Connect Health Resource Sidebarról](./media/how-to-connect-health-operations/startRBAC.png)
2. Válassza a **Hozzáadás** lehetőséget.
3. A **szerepkör kiválasztása** panelen válasszon ki egy szerepkört (például **tulajdonos**).<br>
   ![Képernyőkép a Azure AD Connect Health és az Azure RBAC konfigurálási menüjéről](./media/how-to-connect-health-operations/RBAC_add.png)
4. Írja be a célként megadott felhasználó vagy csoport nevét vagy azonosítóját. Egyszerre csak egy vagy több felhasználót vagy csoportot választhat ki. Kattintson a **Kiválasztás** elemre.
   ![Képernyőkép a Azure AD Connect Health és az Azure-szerepkörök listájáról](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Válassza az **OK** lehetőséget.<br>
6. A szerepkör-hozzárendelés befejezése után a felhasználók és csoportok megjelennek a listában.<br>
   ![Képernyőkép a Azure AD Connect Health és az Azure RBAC és az új Kiemelt felhasználókról](./media/how-to-connect-health-operations/RBAC_user_list.png)

Most a felsorolt felhasználók és csoportok hozzáférnek a hozzájuk rendelt szerepköröknek megfelelően.

> [!NOTE]
> * A globális rendszergazdák mindig teljes hozzáféréssel rendelkeznek az összes művelethez, de a globális rendszergazdai fiókok nem jelennek meg az előző listában.
> * A felhasználók meghívása funkció nem támogatott Azure AD Connect Healthon belül.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. lépés: a panel helyének megosztása felhasználókkal vagy csoportokkal
1. Az engedélyek kiosztása után a felhasználó a következő lépésekkel [](https://aka.ms/aadconnecthealth)férhet hozzá Azure ad Connect Healthhoz.
2. A panelen a felhasználó rögzítheti a panelt vagy annak különböző részeit az irányítópulton. Egyszerűen kattintson a **rögzítés az irányítópulton** ikonra.<br>
   ![Képernyőkép a Azure AD Connect Health és az Azure RBAC PIN-kódjáról, a PIN ikon kiemelésével](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> A hozzárendelt olvasó szerepkörrel rendelkező felhasználók nem tudnak Azure AD Connect Health bővítményt beolvasni az Azure piactéren. A felhasználó nem tudja végrehajtani a szükséges "létrehozás" műveletet. A felhasználó továbbra is elérheti a panelt az előző hivatkozásra kattintva. A későbbi használat érdekében a felhasználó rögzítheti a panelt az irányítópulton.
>
>

### <a name="remove-users-or-groups"></a>Felhasználók vagy csoportok eltávolítása
Eltávolíthatja a Azure AD Connect Health és az Azure RBAC felvett felhasználókat vagy csoportokat. Egyszerűen kattintson a jobb gombbal a felhasználóra vagy csoportra, majd válassza az **Eltávolítás** lehetőséget.<br>
![Képernyőkép a Azure AD Connect Healthről és az Azure RBAC a Kiemelt eltávolítással](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC szakasz vége)

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health ügynök telepítése](how-to-connect-health-agent-install.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Szinkronizálás Azure AD Connect Health használata](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
