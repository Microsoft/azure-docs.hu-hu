---
title: Rendszergazdai engedély a LinkedIn-fiókok kapcsolataihoz – Azure AD | Microsoft Docs
description: A cikk azt ismerteti, hogyan engedélyezhető vagy tiltható le a LinkedIn integrációs fiók kapcsolatai a Microsoft-alkalmazásokban Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ffa937f91af7078b28331a655f99397d3821def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545893"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>A LinkedIn-fiókok kapcsolatainak integrálása Azure Active Directory

Lehetővé teheti, hogy a szervezet felhasználói a LinkedIn-kapcsolataikat bizonyos Microsoft-alkalmazásokon belül elérhessék. A felhasználók nem oszthatnak meg semmilyen adatmegosztást, amíg a felhasználók nem csatlakoznak a fiókjához. A szervezet integrálható a Azure Active Directory (Azure AD) [felügyeleti központba](https://aad.portal.azure.com).

> [!IMPORTANT]
> A LinkedIn-fiók kapcsolatainak beállítása jelenleg az Azure AD-szervezeteknél zajlik. Ha a szervezet számára bekerül, alapértelmezés szerint engedélyezve van.
>
> Kivételek
>
> * A beállítás nem érhető el olyan ügyfelek számára, akik az Egyesült Államok kormánya, Microsoft Cloud Németország vagy az Azure Microsoft 365 és a 21Vianet által üzemeltetett Microsoft Cloudt használják.
> * A beállítás alapértelmezés szerint ki van kapcsolva a Németországban kiépített Azure AD-szervezetek esetében. Vegye figyelembe, hogy a beállítás nem érhető el a Microsoft Cloud Németországot használó ügyfelek számára.
> * A beállítás alapértelmezés szerint ki van kapcsolva a Franciaországban kiépített szervezeteknek.
>
> Ha a LinkedIn-fiókok kapcsolatai engedélyezve vannak a szervezet számára, a fiókok kapcsolatai a felhasználók által a nevükben elérhető vállalati adatokhoz való hozzáférésük után működnek. A felhasználói beleegyező beállításokkal kapcsolatos további információkért lásd: [felhasználó hozzáférésének eltávolítása egy alkalmazáshoz](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>A LinkedIn-fiókok kapcsolatainak engedélyezése a Azure Portalban

A LinkedIn-fiókok kapcsolatai csak azokra a felhasználókra engedélyezhetők, akik számára hozzáférést szeretne elérni, a teljes szervezetből csak a szervezet kiválasztott felhasználói számára.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure ad-szervezet globális rendszergazdája.
1. Válassza a **Felhasználók** lehetőséget.
1. A **felhasználók** lapon válassza a **felhasználói beállítások** lehetőséget.
1. A **LinkedIn-fiókok kapcsolatai** területen engedélyezze a felhasználók számára, hogy a LinkedIn-kapcsolataik elérését néhány Microsoft-alkalmazáson belül hozzáférjenek a fiókjához. A felhasználók nem oszthatnak meg semmilyen adatmegosztást, amíg a felhasználók nem csatlakoznak a fiókjához.

    * Az **Igen** lehetőség kiválasztásával engedélyezheti a szolgáltatást a szervezet összes felhasználója számára
    * Válassza a **kijelölt csoport** lehetőséget, hogy a szolgáltatás csak a szervezet kiválasztott felhasználói csoportjának engedélyezze a szolgáltatást
    * A **nem** gombra kattintva visszavonhatja a szervezet összes felhasználójának beleegyezikét

    ![A LinkedIn-fiókok kapcsolatainak integrálása a szervezetbe](./media/linkedin-integration/linkedin-integration.png)

1. Ha elkészült, válassza a **Mentés** lehetőséget a beállítások mentéséhez.

> [!Important]
> A LinkedIn-integráció nincs teljesen engedélyezve a felhasználók számára, amíg nem engedélyezik a fiókjaik összekapcsolását. A felhasználói fiókok kapcsolatainak engedélyezésekor a rendszer nem osztja meg az összes adatmegosztást.

### <a name="assign-selected-users-with-a-group"></a>Kijelölt felhasználók társítása csoporttal

Kiváltottuk a "kiválasztott" lehetőséget, amely meghatározza a felhasználók egy csoportjának kiválasztására szolgáló lehetőséget, így lehetővé teheti a LinkedIn és a Microsoft-fiókok egyetlen csoporthoz való összekapcsolását számos egyéni felhasználó helyett. Ha nincs engedélyezve a LinkedIn-fiókok kapcsolatai a kiválasztott egyéni felhasználók számára, semmit nem kell tennie. Ha korábban engedélyezte a LinkedIn-fiókok kapcsolatait a kiválasztott egyéni felhasználók számára, tegye a következőket:

1. Az egyes felhasználók aktuális listájának beolvasása
1. A jelenleg engedélyezett egyéni felhasználók áthelyezése egy csoportba
1. Az Azure AD felügyeleti központban a LinkedIn Account Connections beállításban az előzőből kiválasztott csoportból válassza ki a csoportot.

> [!NOTE]
> Még ha nem helyezi át a jelenleg kijelölt felhasználókat egy csoportba, továbbra is láthatják a LinkedIn-adatokat a Microsoft-alkalmazásokban.

### <a name="move-currently-selected-users-to-a-group"></a>Aktuálisan kijelölt felhasználók áthelyezése egy csoportba

1. Hozzon létre egy CSV-fájlt a LinkedIn-fiók kapcsolataihoz kiválasztott felhasználókról.
1. Jelentkezzen be Microsoft 365ba a rendszergazdai fiókjával.
1. Indítsa el a PowerShellt.
1. Telepítse az Azure AD-modult a futtatásával `Install-Module AzureAD`
1. Futtassa a következő parancsfájlt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Ha az Azure AD felügyeleti központban lévő LinkedIn Account Connections (LinkedIn-fiók kapcsolatainak engedélyezése) beállításban a második lépésből származó csoportot kívánja használni, tekintse meg a következő témakört: [Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>A LinkedIn-fiókok kapcsolatainak engedélyezése a Csoportházirend használatával

1. Töltse le az [Office 2016 felügyeleti sablon fájljait (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Bontsa ki az **ADMX** -fájlokat, és másolja őket a központi tárolóba.
1. Nyissa meg a Csoportházirend kezelése eszközt.
1. Hozzon létre egy csoportházirend objektumot a következő beállítással: **felhasználói konfiguráció**  >  **Felügyeleti sablonok**  >  **Microsoft Office 2016**  >  **egyebek**  >  **az Office-alkalmazások LinkedIn szolgáltatásainak megjelenítése**.
1. Válassza az **engedélyezve** vagy a **Letiltva** lehetőséget.
  
   Állapot | Hatás
   ------ | ------
   **Engedélyezve** | Engedélyezve van az Office 2016-beállítások az Office- **alkalmazásokban beállítás a LinkedIn funkcióinak megjelenítése** lehetőség. A szervezet felhasználói a LinkedIn funkcióit használhatják az Office 2016-alkalmazásaikban.
   **Disabled** | A **LinkedIn funkcióinak megjelenítése** az Office-alkalmazások Office 2016-beállításokban beállítás le van tiltva, és a végfelhasználók nem változtathatják meg ezt a beállítást. A szervezet felhasználói nem használhatják a LinkedIn szolgáltatásait az Office 2016-alkalmazásokban.

Ez a csoportházirend csak az Office 2016-alkalmazásokat érinti a helyi számítógépeken. Ha a felhasználók az Office 2016-alkalmazásokban letiltják a LinkedIn szolgáltatást, továbbra is megjelenhetnek a LinkedIn szolgáltatásai Microsoft 365.

## <a name="next-steps"></a>Következő lépések

* [Felhasználói beleegyezett és adatmegosztás a LinkedIn szolgáltatásban](linkedin-user-consent.md)

* [A LinkedIn információi és funkciói a Microsoft-alkalmazásokban](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn súgó központ](https://www.linkedin.com/help/linkedin)

* [A Azure Portal aktuális LinkedIn-integrációs beállításának megtekintése](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)