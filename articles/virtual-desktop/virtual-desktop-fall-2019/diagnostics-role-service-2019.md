---
title: Windows rendszerű virtuális asztali (klasszikus) problémák diagnosztizálása – Azure
description: A Windows rendszerű virtuális asztali (klasszikus) diagnosztika funkció használata a problémák diagnosztizálásához.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7e652f04b42b132e7c1307503b1764dda7b2036b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009341"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Problémák azonosítása és diagnosztizálása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../diagnostics-role-service.md).

A Windows rendszerű virtuális asztali szolgáltatás diagnosztikai szolgáltatást biztosít, amely lehetővé teszi, hogy a rendszergazda egyetlen felületen azonosítsa a problémákat. A Windows rendszerű virtuális asztali szerepkörök diagnosztikai tevékenységet naplóznak, amikor egy felhasználó kommunikál a rendszerrel. Minden napló olyan releváns információkat tartalmaz, mint például a tranzakcióban részt vevő Windows virtuális asztali szerepkörök, a hibaüzenetek, a bérlői adatok és a felhasználói adatok. A diagnosztikai tevékenységeket mind a végfelhasználói, mind a rendszergazdai műveletek hozzák létre, és három fő gyűjtőbe sorolhatók:

* Hírcsatorna-előfizetési tevékenységek: a végfelhasználó elindítja ezeket a tevékenységeket, amikor megpróbálnak csatlakozni a hírcsatornához Microsoft Távoli asztal alkalmazásokon keresztül.
* Kapcsolódási tevékenységek: a végfelhasználó elindítja ezeket a tevékenységeket, amikor megpróbálnak csatlakozni egy asztali vagy RemoteApp-hoz Microsoft Távoli asztal alkalmazáson keresztül.
* Felügyeleti tevékenységek: a rendszergazda elindítja ezeket a tevékenységeket, amikor felügyeleti műveleteket végez a rendszeren, például a gazdagépek létrehozása, a felhasználók hozzárendelése az alkalmazás-csoportokhoz és a szerepkör-hozzárendelések létrehozása.

Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás a Windows virtuális asztal része. A Windows rendszerű virtuális asztali kapcsolattal kapcsolatos problémák akkor fordulnak elő, ha a végfelhasználó hálózati kapcsolati problémákba ütközik.

Első lépésként [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Problémák diagnosztizálása a PowerShell-lel

A Windows rendszerű virtuális asztali diagnosztika csak egy PowerShell-parancsmagot használ, de számos opcionális paramétert tartalmaz, amelyek segítenek a problémák szűkítéséhez és elkülönítésében. A következő részben azokat a parancsmagokat sorolja fel, amelyeket a problémák diagnosztizálásához futtathat. A legtöbb szűrő együtt is alkalmazható. A zárójelben felsorolt értékeket (például `<tenantName>` ) az adott helyzetre érvényes értékekkel kell helyettesíteni.

>[!IMPORTANT]
>A diagnosztikai funkció az egyfelhasználós hibaelhárításhoz használható. A PowerShellt használó összes lekérdezésnek tartalmaznia kell a *-username* vagy a *-tevékenységazonosító* paramétert. A figyelési képességekhez használja a Log Analytics. A diagnosztikai adatok munkaterületre való küldésével kapcsolatos további információkért tekintse meg [a log Analytics használata a diagnosztikai szolgáltatáshoz](diagnostics-log-analytics-2019.md) című témakört.

### <a name="filter-diagnostic-activities-by-user"></a>Diagnosztikai tevékenységek szűrése felhasználó szerint

A **-username** paraméter a megadott felhasználó által kezdeményezett diagnosztikai tevékenységek listáját adja vissza, ahogy az az alábbi példában is látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

A **-username** paraméter más nem kötelező szűrési paraméterekkel is kombinálható.

### <a name="filter-diagnostic-activities-by-time"></a>Diagnosztikai tevékenységek szűrése idő szerint

A visszaadott diagnosztikai tevékenységek listáját a **-kezdő** és a **-** záró paraméterekkel szűrheti. A **-** Kezdődátum paraméter egy megadott dátumtól kezdődő diagnosztikai tevékenységek listáját fogja visszaadni, ahogy az az alábbi példában is látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

A **-befejezési** paramétert hozzáadhatja egy parancsmaghoz a **-kezdő** paraméterrel egy adott időszak megadásához, amelynek eredményét meg szeretné kapni. Az alábbi példában szereplő parancsmag a diagnosztikai tevékenységek listáját augusztus 1-től és augusztus 10-én fogja visszaadni.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

A **-kezdő** és **a** záró paraméter is kombinálható más opcionális szűrési paraméterekkel.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Diagnosztikai tevékenységek szűrése tevékenység típusa szerint

A diagnosztikai tevékenységeket tevékenység típusa szerint is szűrheti a **-activityType** paraméterrel. A következő parancsmag a végfelhasználói kapcsolatok listáját fogja visszaadni:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

A következő parancsmag a rendszergazdai felügyeleti feladatok listáját fogja visszaadni:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

A **Get-RdsDiagnosticActivities** parancsmag jelenleg nem támogatja a hírcsatorna activityType való megadását.

### <a name="filter-diagnostic-activities-by-outcome"></a>Diagnosztikai tevékenységek szűrése eredmény alapján

A visszaadott diagnosztikai tevékenységek listáját a **-eredmény** paraméterrel szűrheti eredményként. A következő példa parancsmag a sikeres diagnosztikai tevékenységek listáját fogja visszaadni.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

A következő példa parancsmag a sikertelen diagnosztikai tevékenységek listáját fogja visszaadni.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

A **-végeredmény** paraméter más opcionális szűrési paraméterekkel is kombinálható.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Adott diagnosztikai tevékenység lekérése tevékenység-azonosító alapján

A **-tevékenységazonosító** paraméter adott diagnosztikai tevékenységet ad vissza, ha létezik, ahogy az az alábbi példában is látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Hibás tevékenységhez tartozó hibaüzenetek megtekintése tevékenység azonosítója szerint

A sikertelen tevékenységhez tartozó hibaüzenetek megtekintéséhez a **-Detailed** paraméterrel kell futtatnia a parancsmagot. A hibák listáját a **Select-Object** parancsmag futtatásával tekintheti meg.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Részletes diagnosztikai tevékenységek beolvasása

A **-Detailed** paraméter további részleteket tartalmaz a visszaadott diagnosztikai tevékenységekről. Az egyes tevékenységek formátuma a tevékenység típusától függően változik. A **-Detailed** paraméter bármely **Get-RdsDiagnosticActivities** lekérdezéshez hozzáadható, ahogy az az alábbi példában is látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Gyakori hibaforgatókönyvek

A hibákra vonatkozó forgatókönyvek a szolgáltatás belső és külső Windowsos virtuális asztalára vannak kategorizálva.

* Belső probléma: azokat a forgatókönyveket határozza meg, amelyeket nem lehet enyhíteni a bérlői rendszergazda, és támogatási problémaként meg kell oldani. Ha visszajelzést ad a [Windows rendszerű virtuális asztali technikai Közösségen](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)keresztül, adja meg a tevékenység azonosítóját és a probléma előfordulásának hozzávetőleges időkeretét.
* Külső probléma: olyan forgatókönyvekhez kapcsolódik, amelyeket a rendszergazda enyhítheti. Ezek a Windows rendszerű virtuális asztali gépeken kívül vannak.

A következő táblázat azokat a gyakori hibákat sorolja fel, amelyeket a rendszergazdák futtathatnak.

>[!NOTE]
>Ez a lista a leggyakoribb hibákat tartalmazza, és rendszeresen frissül. Annak érdekében, hogy a legfrissebb információk legyenek naprakészek, ügyeljen rá, hogy havonta legalább egyszer térjen vissza a cikkre.

### <a name="external-management-error-codes"></a>Külső felügyeleti hibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|A felhasználó nem tagja Azure Active Directorynak. A hozzáadásához kövesse a [Active Directory felügyeleti központ](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) útmutatását.|
|3|UnauthorizedAccess|A rendszergazdai PowerShell-parancsmag futtatására megkísérelt felhasználó nem jogosult erre vagy a Felhasználónév elírására.|
|1000|TenantNotFound|A megadott bérlő neve nem egyezik a meglévő bérlők nevével. Tekintse át a bérlő nevét az elírásokhoz, és próbálkozzon újra.|
|1006|TenantCannotBeRemovedHasSessionHostPools|A bérlőt nem lehet törölni, amíg objektumokat tartalmaz. Először törölje a munkamenet-gazdagép készleteit, majd próbálkozzon újra.|
|2000|HostPoolNotFound|A megadott címkészlet neve nem felel meg a meglévő gazdagép-készleteknek. Tekintse át a gazdagép-készlet nevét az elírásokhoz, és próbálkozzon újra.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|A gazdagépek nem törölhetők, amíg objektumokat tartalmaz. Először távolítsa el az összes alkalmazás-csoportot a gazdagép-készletből.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Először távolítsa el az összes munkamenet-gazdagépet a munkamenet-gazdagép készletének törlése előtt.|
|5001|SessionHostNotFound|Előfordulhat, hogy a lekérdezett munkamenet-gazdagép offline állapotban van. Győződjön meg arról, hogy a gazdagép készletének állapota.|
|5008|SessionHostUserSessionsExist |A kívánt felügyeleti tevékenység végrehajtása előtt ki kell jelentkeznie a munkamenet-gazdagépen lévő összes felhasználóra.|
|6000|AppGroupNotFound|Az alkalmazáscsoport megadott neve nem felel meg egyetlen meglévő alkalmazás-csoportnak sem. Tekintse át az alkalmazás csoportjának nevét az elírásokhoz, és próbálkozzon újra.|
|6022|RemoteAppNotFound|A megadott RemoteApp-név nem felel meg a RemoteApp-nak. Tekintse át a RemoteApp nevét az elírásokhoz, és próbálkozzon újra.|
|6010|PublishedItemsExist|A közzétenni kívánt erőforrás neve megegyezik egy már létező erőforrás nevével. Módosítsa az erőforrás nevét, és próbálkozzon újra.|
|7002|NameNotValidWhiteSpace|Ne használjon szóközt a névben.|
|8000|InvalidAuthorizationRoleScope|A megadott szerepkör neve nem egyezik a meglévő szerepkörök nevével. Tekintse át az elírásokhoz tartozó szerepkör nevét, és próbálkozzon újra. |
|8001|UserNotFound |A megadott Felhasználónév nem egyezik a meglévő felhasználónevek nevével. Tekintse át az elírások nevét, és próbálkozzon újra.|
|8005|UserNotFoundInAAD |A megadott Felhasználónév nem egyezik a meglévő felhasználónevek nevével. Tekintse át az elírások nevét, és próbálkozzon újra.|
|8008|TenantConsentRequired|Kövesse az [itt](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) található utasításokat a bérlői engedély megadásához.|

### <a name="external-connection-error-codes"></a>Külső kapcsolatok hibakódai

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|– 2147467259|ConnectionFailedAdErrorNoSuchMember|A felhasználó nem tagja Active Directorynak. A hozzáadásához kövesse a [Active Directory felügyeleti központ](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) útmutatását.|
|– 2147467259|ConnectionFailedAdTrustedRelationshipFailure|A munkamenet-gazdagép nem megfelelően van csatlakoztatva a Active Directoryhoz.|
|– 2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|A kapcsolatok sikertelenek voltak, mert a munkamenet-gazdagép nem érhető el. Keresse meg a munkamenet-gazdagép állapotát.|
|– 2146233088|ConnectionFailedClientDisconnect|Ha gyakran látja ezt a hibát, győződjön meg arról, hogy a felhasználó számítógépe csatlakozik a hálózathoz.|
|– 2146233088|ConnectionFailedNoHealthyRdshAvailable|Az a munkamenet, amelyhez a gazdagép felhasználója megpróbált csatlakozni, nem kifogástalan állapotú. A virtuális gép hibakeresése.|
|– 2146233088|ConnectionFailedUserNotAuthorized|A felhasználónak nincs engedélye a közzétett alkalmazáshoz vagy asztalhoz való hozzáféréshez. A hiba akkor jelenhet meg, ha a rendszergazda eltávolította a közzétett erőforrásokat. Kérje meg a felhasználót, hogy frissítse a hírcsatornát a Távoli asztal alkalmazásban.|
|2|FileNotFound|A felhasználó által elérni próbált alkalmazás vagy helytelenül van telepítve, vagy helytelen elérési útra van beállítva.|
|3|InvalidCredentials|A felhasználó által megadott Felhasználónév vagy jelszó nem felel meg a meglévő felhasználóneveknek vagy jelszavaknak. Tekintse át az elírásokhoz tartozó hitelesítő adatokat, és próbálkozzon újra.|
|8|ConnectionBroken|Az ügyfél és az átjáró vagy a kiszolgáló közötti kapcsolat megszakadt. Nincs szükség beavatkozásra, kivéve, ha váratlanul történik.|
|14|UnexpectedNetworkDisconnect|A hálózattal létesített kapcsolódás megszakadt. Kérje meg a felhasználót, hogy kapcsolódjon újra.|
|24|ReverseConnectFailed|A gazdagép virtuális gépe nem rendelkezik közvetlen RD-átjárói vonallal. Győződjön meg arról, hogy az átjáró IP-címe oldható fel.|
|1322|ConnectionFailedNoMappingOfSIDinAD|A felhasználó nem tagja Active Directorynak. A hozzáadásához kövesse a [Active Directory felügyeleti központ](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) útmutatását.|

## <a name="next-steps"></a>Következő lépések

A Windows rendszerű virtuális asztali szerepkörökkel kapcsolatos további információkért lásd: [Windows rendszerű virtuális asztali környezet](environment-setup-2019.md).

A Windowsos virtuális asztalhoz elérhető PowerShell-parancsmagok listájának megtekintéséhez tekintse meg a [PowerShell-referenciát](/powershell/windows-virtual-desktop/overview).
