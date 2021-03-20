---
title: Windows rendszerű virtuális asztali problémák diagnosztizálása – Azure
description: A Windows rendszerű virtuális asztali diagnosztika szolgáltatás használata a problémák diagnosztizálásához.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91279858"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>A Windows rendszerű virtuális asztali problémák azonosítása és diagnosztizálása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

A Windows rendszerű virtuális asztali szolgáltatás diagnosztikai szolgáltatást biztosít, amely lehetővé teszi, hogy a rendszergazda egyetlen felületen azonosítsa a problémákat. Ha többet szeretne megtudni a Windowsos virtuális asztal diagnosztikai képességeiről, tekintse meg [a log Analytics használata a diagnosztikai szolgáltatáshoz](diagnostics-log-analytics.md)című témakört.

Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás a Windows virtuális asztal része. A Windows rendszerű virtuális asztali kapcsolattal kapcsolatos problémák akkor fordulnak elő, ha a végfelhasználó hálózati kapcsolati problémákba ütközik.

## <a name="common-error-scenarios"></a>Gyakori hibaforgatókönyvek

A WVDErrors tábla minden tevékenységtípus esetében nyomon követi a hibákat. A "ServiceError" nevű oszlop egy "true" vagy "false" jelölésű további jelzőt biztosít. Ez a jelző azt jelzi, hogy a hiba kapcsolódik-e a szolgáltatáshoz.

* Ha az érték "true" (igaz), akkor előfordulhat, hogy a szolgáltatási csapat már megvizsgálta ezt a problémát. Ha ez hatással van a felhasználói élményre, és nagy számú alkalommal jelenik meg, javasoljuk, hogy küldjön be egy támogatási jegyet a Windows rendszerű virtuális asztalra.
* Ha az érték "false" (hamis), akkor lehet, hogy helytelen a konfiguráció, amelyet saját maga javíthat. A hibaüzenetből megtudhatja, hol kezdjen el.

A következő táblázat azokat a gyakori hibákat sorolja fel, amelyeket a rendszergazdák futtathatnak.

>[!NOTE]
>Ez a lista a leggyakoribb hibákat tartalmazza, és rendszeresen frissül. Annak érdekében, hogy a legfrissebb információk legyenek naprakészek, ügyeljen rá, hogy havonta legalább egyszer térjen vissza a cikkre.

## <a name="management-errors"></a>Kezelési hibák

|Hibaüzenet|Javasolt megoldás|
|---|---|
|Nem sikerült létrehozni a regisztrációs kulcsot |Nem sikerült létrehozni a regisztrációs jogkivonatot. Próbálja újból létrehozni a rövidebb lejárati idővel (1 óra és 1 hónap között). |
|Nem sikerült törölni a regisztrációs kulcsot|Nem sikerült törölni a regisztrációs jogkivonatot. Próbálkozzon újra a törléssel. Ha továbbra sem működik, a PowerShell használatával ellenőrizze, hogy a jogkivonat még mindig van-e. Ha ott van, törölje a PowerShell-lel.|
|Nem sikerült módosítani a munkamenet-gazdagép kiürítési módját |Nem sikerült módosítani a kiürítési módot a virtuális gépen. Győződjön meg arról, hogy a virtuális gép állapota. Ha a virtuális gép nem érhető el, a kiürítési mód nem módosítható.|
|Nem sikerült leválasztani a felhasználói munkameneteket |Nem sikerült leválasztani a felhasználót a virtuális gépről. Győződjön meg arról, hogy a virtuális gép állapota. Ha a virtuális gép nem érhető el, a felhasználói munkamenet nem választható le. Ha a virtuális gép elérhető, tekintse meg a felhasználói munkamenet állapotát, és ellenőrizze, hogy megszakadt-e a kapcsolat. |
|Nem sikerült kijelentkezni az összes felhasználó (ka) t a munkamenet-gazdagépen belül |Nem lehetett aláírni a felhasználókat a virtuális gépről. Győződjön meg arról, hogy a virtuális gép állapota. Ha nem érhető el, a felhasználóknak nem lehet kijelentkezniük. Ellenőrizze, hogy van-e már kijelentkezve a felhasználói munkamenet állapota. A PowerShell használatával kényszerítheti a kijelentkezést. |
|Nem sikerült a felhasználó hozzárendelésének megszüntetése az alkalmazás csoportjából|Nem lehetett visszavonni egy alkalmazás csoportjának közzétételét a felhasználó számára. Ellenőrizze, hogy elérhető-e a felhasználó az Azure AD-ben. Ellenőrizze, hogy a felhasználó tagja-e egy olyan felhasználói csoportnak, amelyre az alkalmazáscsoport közzé lett téve. |
|Hiba történt az elérhető helyszínek beolvasásakor |A gazdagép-készlet létrehozása varázslóban használt virtuális gép helyének keresése. Ha a rendszerkép nem érhető el ezen a helyen, adjon hozzá egy képet az adott helyen, vagy válasszon másik virtuálisgép-helyet. |

### <a name="connection-error-codes"></a>Hálózati hibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|– 2147467259|ConnectionFailedAdTrustedRelationshipFailure|A munkamenet-gazdagép nem megfelelően van csatlakoztatva a Active Directoryhoz.|
|– 2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|A kapcsolatok sikertelenek voltak, mert a munkamenet-gazdagép nem érhető el. Keresse meg a munkamenet-gazdagép állapotát.|
|– 2146233088|ConnectionFailedClientDisconnect|Ha gyakran látja ezt a hibát, győződjön meg arról, hogy a felhasználó számítógépe csatlakozik a hálózathoz.|
|– 2146233088|ConnectionFailedNoHealthyRdshAvailable|Az a munkamenet, amelyhez a gazdagép felhasználója megpróbált csatlakozni, nem kifogástalan állapotú. A virtuális gép hibakeresése.|
|– 2146233088|ConnectionFailedUserNotAuthorized|A felhasználónak nincs engedélye a közzétett alkalmazáshoz vagy asztalhoz való hozzáféréshez. A hiba akkor jelenhet meg, ha a rendszergazda eltávolította a közzétett erőforrásokat. Kérje meg a felhasználót, hogy frissítse a hírcsatornát a Távoli asztal alkalmazásban.|
|2|FileNotFound|A felhasználó által elérni próbált alkalmazás vagy helytelenül van telepítve, vagy helytelen elérési útra van beállítva.<br><br>Amikor új alkalmazásokat tesz közzé, miközben a felhasználó aktív munkamenettel rendelkezik, a felhasználó nem fog tudni hozzáférni az alkalmazáshoz. A munkamenetet le kell állítani, és újra kell indítani ahhoz, hogy a felhasználó hozzáférhessen az alkalmazáshoz. |
|3|InvalidCredentials|A felhasználó által megadott Felhasználónév vagy jelszó nem felel meg a meglévő felhasználóneveknek vagy jelszavaknak. Tekintse át az elírásokhoz tartozó hitelesítő adatokat, és próbálkozzon újra.|
|8|ConnectionBroken|Az ügyfél és az átjáró vagy a kiszolgáló közötti kapcsolat megszakadt. Nincs szükség beavatkozásra, kivéve, ha váratlanul történik.|
|14|UnexpectedNetworkDisconnect|A hálózattal létesített kapcsolódás megszakadt. Kérje meg a felhasználót, hogy kapcsolódjon újra.|
|24|ReverseConnectFailed|A gazdagép virtuális gépe nem rendelkezik közvetlen RD-átjárói vonallal. Győződjön meg arról, hogy az átjáró IP-címe oldható fel.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Hiba: nem lehet felhasználói hozzárendeléseket felvenni egy alkalmazás-csoportba

Miután hozzárendelt egy felhasználót egy alkalmazás csoportjához, a Azure Portal egy figyelmeztetést jelenít meg, amely szerint a "munkamenet vége" vagy a "hitelesítési problémák – kiterjesztés Microsoft_Azure_WVD." üzenet jelenik meg. A hozzárendelési lap nem töltődik be, és ezt követően a lapok nem töltődnek le a Azure Portalon (például Azure Monitor, Log Analytics, Service Health stb.).

**OK:** Probléma merült fel a feltételes hozzáférési szabályzattal kapcsolatban. A Azure Portal a SharePoint Online-tól függő Microsoft Graph-token beszerzését kísérli meg. Az ügyfél "Microsoft Office 365 adattárolási használati feltételei" nevű feltételes hozzáférési szabályzattal rendelkezik, amely megköveteli, hogy a felhasználók elfogadják a használati feltételeket az adattárolás eléréséhez. Azonban még nem jelentkezett be, így a Azure Portal nem tudja lekérni a jogkivonatot.

**Javítás:** Mielőtt bejelentkeznek a Azure Portalba, a rendszergazdának először be kell jelentkeznie a SharePointba, és el kell fogadnia a használati feltételeket. Ezután be kell jelentkezniük a Azure Portalba, például a normális kerékvágásba.

## <a name="next-steps"></a>Következő lépések

A Windows rendszerű virtuális asztali szerepkörökkel kapcsolatos további információkért lásd: [Windows rendszerű virtuális asztali környezet](environment-setup.md).

A Windowsos virtuális asztalhoz elérhető PowerShell-parancsmagok listájának megtekintéséhez tekintse meg a [PowerShell-referenciát](/powershell/windows-virtual-desktop/overview).
