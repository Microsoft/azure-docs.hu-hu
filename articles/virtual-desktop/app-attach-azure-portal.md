---
title: Windows rendszerű virtuális asztali MSIX-alkalmazás csatlakoztatása a portál előzetes verziójához – Azure
description: A Windows rendszerű virtuális asztali MSIX-alkalmazás csatlakoztatása a Azure Portal használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c775d81b88c891d6d8ea0a4597b4fa4fee29c86a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737545"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>MSIX-alkalmazás csatolásának beállítása az Azure Portal használatával

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebből a cikkből megtudhatja, hogyan állíthatja be a MSIX-alkalmazás csatolását (előzetes verzió) egy Windows rendszerű virtuális asztali környezetben.

## <a name="requirements"></a>Követelmények

>[!IMPORTANT]
>Mielőtt elkezdené, győződjön meg róla, hogy kitölti és beküldi [ezt az űrlapot](https://aka.ms/enablemsixappattach) , hogy engedélyezze a MSIX-alkalmazás csatolását az előfizetésében. Ha nem rendelkezik jóváhagyott kéréssel, a MSIX-alkalmazás csatolása nem fog működni. A kérések jóváhagyása akár 24 óráig is eltarthat a munkaidőn belül. A kérés elfogadását és befejezését követően e-mailt fog kapni.

A MSIX-alkalmazás csatolásának konfigurálásához a következők szükségesek:

- Működő Windowsos virtuális asztali telepítés. A Windows rendszerű virtuális asztali gépek (klasszikus) központi telepítésének megismeréséhez tekintse meg [a bérlő létrehozása a Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)című témakört. A Windows rendszerű virtuális asztali Azure Resource Manager integrációs szolgáltatással való üzembe helyezésével kapcsolatos további információkért lásd: [gazdagép-készlet létrehozása a Azure Portal](./create-host-pools-azure-marketplace.md).
- Egy Windows rendszerű virtuális asztali címkészlet, amely legalább egy aktív munkamenet-gazdagépet futtat.
- Ennek az alkalmazáskészletnek az ellenőrzési környezetben kell lennie. 
- A MSIX-csomagoló eszköz.
- Egy MSIX csomagolt alkalmazás kibővült egy fájlmegosztásba feltöltött MSIX-rendszerképbe.
- Egy fájlmegosztás a Windows rendszerű virtuális asztali környezetben, ahol a MSIX-csomagot tárolja a rendszer.
- A MSIX-rendszerkép feltöltésének helyét a gazdagépen lévő összes virtuális gép (VM) számára is elérhetővé kell tenni. A felhasználóknak csak olvasási jogosultsággal kell rendelkezniük a rendszerkép eléréséhez.
- Ha a tanúsítvány nem nyilvánosan megbízható, kövesse a [tanúsítványok telepítése](app-attach.md#install-certificates)című témakör utasításait.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Az automatikus frissítések kikapcsolása a MSIX alkalmazáshoz

Az első lépések előtt le kell tiltania az MSIX alkalmazáshoz tartozó alkalmazások automatikus frissítését. Az automatikus frissítések letiltásához futtatnia kell a következő parancsokat egy rendszergazda jogú parancssorban:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Javasoljuk, hogy a Hyper-V engedélyezése után indítsa újra a virtuális gépet.

## <a name="configure-the-msix-app-attach-management-interface"></a>A MSIX alkalmazás csatolása felügyeleti felület konfigurálása

Ezután le kell töltenie és konfigurálnia kell a MSIX alkalmazás csatolása felügyeleti felületet a Azure Portalhoz.

A felügyeleti felület beállítása:

1. [Nyissa meg a Azure Portal](https://portal.azure.com).
2. Ha a bővítmény megbízhatóságának megadását kérő üzenet jelenik meg, válassza az **Engedélyezés lehetőséget**.

      > [!div class="mx-imgBorder"]
      > ![A nem megbízható bővítmények ablakának képernyőképe. Az "engedélyezés" piros színnel van kiemelve.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>MSIX-rendszerkép hozzáadása a gazdagéphez

Ezután fel kell vennie a MSIX-rendszerképet a gazdagép-készletbe.

A MSIX-rendszerkép hozzáadása:

1. Nyissa meg az Azure Portalt.

2. Adja meg a **Windows rendszerű virtuális asztalt** a keresési sávban, majd válassza ki a szolgáltatás nevét.

      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a felhasználóról a Azure Portal a keresősáv legördülő menüjéből válassza a "Windows virtuális asztal" lehetőséget. A "Windows virtuális asztal" vörös színnel van kiemelve.](media/find-and-select.png)

3. Válassza ki azt a gazdagépet, ahol a MSIX-alkalmazásokat el szeretné helyezni.

4. Válassza az **MSIX-csomagok** lehetőséget, hogy az adatrácsot a gazdagéphez aktuálisan hozzáadott összes MSIX- **csomaggal** nyissa meg.

5. Válassza a **+ Hozzáadás** lehetőséget a **MSIX-csomag hozzáadása** lap megnyitásához.

6. A **MSIX-csomag hozzáadása** lapon adja meg a következő értékeket:

      - A **MSIX rendszerkép elérési útja** mezőben adjon meg egy érvényes UNC elérési utat, amely a fájlmegosztás MSIX képére mutat. (Például: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Ha elkészült, a **Hozzáadás** gombra kattintva kiválaszthatja a MSIX-tárolót, hogy ellenőrizze, érvényes-e az elérési út.

      - A **MSIX csomag** esetében válassza ki a megfelelő MSIX-csomag nevét a legördülő menüből. Ez a menü csak akkor lesz feltöltve, ha érvényes képelérési útvonalat adott meg a **MSIX-rendszerkép elérési útjában**.

      - **Csomagbeli alkalmazások** esetén győződjön meg arról, hogy a lista tartalmazza az összes olyan MSIX alkalmazást, amelyet a MSIX-csomagban lévő felhasználók számára elérhetővé szeretne tenni.

      - Szükség esetén megadhat egy **megjelenítendő nevet** is, ha azt szeretné, hogy a csomag jobban felhasználóbarát legyen a felhasználói környezetben.

      - Győződjön meg arról, hogy a **verziószáma** megfelelő verziószámmal rendelkezik.

      - Válassza ki a használni kívánt **regisztrációs típust** . Az Ön által használt igények a következőktől függenek:

    - Az **igény szerinti regisztráció** elhalasztja a MSIX alkalmazás teljes regisztrációját, amíg a felhasználó el nem indítja az alkalmazást. Ez a regisztrációs típus, amelyet ajánlott használni.

    - A **Bejelentkezés blokkolásának naplózása** csak akkor történik meg, amikor a felhasználó bejelentkezik. Ezt a típust nem javasoljuk, mert a felhasználók számára már több bejelentkezési időt is igénybe vehet.

7.  Az **állapot** beállításnál válassza ki az előnyben részesített állapotot.
    -  Az **aktív** állapot lehetővé teszi, hogy a felhasználók kommunikálhatnak a csomaggal.
    -  Az **inaktív** állapot azt eredményezi, hogy a Windows virtuális asztal figyelmen kívül hagyja a csomagot, és nem továbbítja azt a felhasználóknak.

8. Ha elkészült, válassza a **Hozzáadás** lehetőséget.

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-alkalmazások közzététele az alkalmazás-csoportokban

Ezután közzé kell tennie az alkalmazásokat a csomagba. Ezt az asztali és a távoli alkalmazás-csoportok esetében is el kell végeznie.

Ha már rendelkezik MSIX-lemezképpel, ugorjon az [MSIX-alkalmazások közzététele az alkalmazás-csoportba](#publish-msix-apps-to-an-app-group)című lépésre. Ha az örökölt alkalmazásokat szeretné tesztelni, kövesse az [MSIX-csomag létrehozása egy virtuális gépen asztali telepítőből](/windows/msix/packaging-tool/create-app-package-msi-vm/) című témakör utasításait, hogy az örökölt alkalmazást egy MSIX-csomagba alakítsa át.

Az alkalmazások közzététele:

1. A Windows rendszerű virtuális asztali erőforrás-szolgáltató területen válassza az **alkalmazások csoportok** fület.

2. Válassza ki azt az erőforráscsoportot, amelyen közzé szeretné tenni az alkalmazásokat.

   >[!NOTE]
   >A MSIX alkalmazások a MSIX alkalmazással is továbbíthatók távoli alkalmazás-és asztali alkalmazás-csoportokhoz

3. Ha már az alkalmazás csoportban van, válassza az **alkalmazások** fület. Az **alkalmazások** rács az összes meglévő alkalmazást megjeleníti az alkalmazás csoporton belül.

4. Válassza a **+ Hozzáadás** lehetőséget az **alkalmazás hozzáadása** lap megnyitásához.

      > [!div class="mx-imgBorder"]
      > ![A felhasználó és a Hozzáadás gombra kattintva megnyílik az alkalmazás hozzáadása lap.](media/select-add.png)

5. Az **alkalmazás forrásához** válassza ki az alkalmazás forrását.
    - Ha asztali alkalmazás csoportot használ, válassza a MSIX- **csomag** lehetőséget.
      
      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a MSIX csomag kiválasztásáról az alkalmazás forrása legördülő menüből. A MSIX-csomag piros színnel van kiemelve.](media/select-source.png)
    
    - Ha távoli alkalmazás-csoportot használ, válasszon a következő lehetőségek közül:
        
        - Start menü
        - Alkalmazás elérési útja
        - MSIX-csomag

    - Az **alkalmazás neve** mezőben adjon meg egy leíró nevet az alkalmazás számára.

    A következő választható szolgáltatásokat is konfigurálhatja:
   
    - A **megjelenítendő név** mezőben adjon meg egy új nevet a csomag számára, amelyet a felhasználók látni fognak.

    - A **Leírás** mezőben adja meg az alkalmazáscsomag rövid leírását.

    - Ha távoli alkalmazást használ, ezeket a beállításokat is megadhatja:

        - **Ikon elérési útja**
        - **Ikon indexe**
        - **Megjelenítés a webes hírcsatornában**

6. Amikor elkészült, válassza a **Mentés** lehetőséget.

>[!NOTE]
>Ha egy felhasználó a távoli alkalmazáscsoport és az asztali alkalmazáscsoport ugyanahhoz a készlethez van rendelve, akkor az asztali alkalmazáscsoport megjelenik a hírcsatornában.

## <a name="assign-a-user-to-an-app-group"></a>Felhasználó társítása egy alkalmazás-csoporthoz

Miután MSIX-alkalmazásokat rendelt egy alkalmazás-csoporthoz, hozzáférést kell biztosítania a felhasználóknak. A hozzáférést a közzétett MSIX-alkalmazásokkal rendelkező alkalmazáscsoport felhasználói vagy felhasználói csoportjainak hozzáadásával rendelheti hozzá. A felhasználók egy alkalmazás-csoporthoz való hozzárendeléséhez kövesse az [alkalmazás-csoportok kezelése a Azure Portal](manage-app-groups.md) segítségével című témakör útmutatását.

>[!NOTE]
>A távoli alkalmazások a nyilvános előzetes verzióban való tesztelésekor eltűnnek a MSIX, ha a távoli alkalmazások nem tudnak csatlakozni a hírcsatornához. Az alkalmazások nem jelennek meg, mert a kiértékelési környezetben használt alkalmazáskészletet az éles környezetben működő távoli asztali közvetítő kézbesíti. Mivel az éles környezetben található távoli asztali közvetítő nem regisztrálja a MSIX alkalmazás távoli alkalmazások csatolását, az alkalmazások nem jelennek meg a hírcsatornában.

## <a name="change-msix-package-state"></a>MSIX-csomag állapotának módosítása

Ezután módosítania kell a MSIX-csomag állapotát **aktív** vagy **inaktív** értékre attól függően, hogy mit szeretne tenni a csomaggal. Az aktív csomagok olyan csomagok, amelyeket a felhasználók a közzétételük után kezelhetnek. A Windows rendszerű virtuális asztal figyelmen kívül hagyja az inaktív csomagokat, így a felhasználók nem tudnak kommunikálni az alkalmazásokkal a rendszeren belül.

### <a name="change-state-with-the-applications-list"></a>Állapot módosítása az alkalmazások listájával

A csomag állapotának módosítása az alkalmazások listájával:

1. Nyissa meg a gazdagépet, és válassza a **MSIX-csomagok** lehetőséget. Ekkor meg kell jelennie a gazdagépen lévő összes meglévő MSIX-csomag listájának.

2. Válassza ki azokat a MSIX-csomagokat, amelyek állapotát módosítani szeretné, majd válassza az **Állapot módosítása** lehetőséget.

### <a name="change-state-with-update-package"></a>Állapot módosítása frissítési csomaggal

A csomag állapotának módosítása frissítési csomaggal:

1. Nyissa meg a gazdagépet, és válassza a **MSIX-csomagok** lehetőséget. Ekkor meg kell jelennie a gazdagépen lévő összes meglévő MSIX-csomag listájának.

2. Válassza ki annak a csomagnak a nevét, amelynek az állapotát módosítani szeretné a MSIX-csomag listából. Ekkor megnyílik a **frissítési csomag** lap.

3. Kapcsolja az **állapot** kapcsolót **inaktívra** vagy **aktívra**, majd válassza a **Mentés lehetőséget.**

## <a name="change-msix-package-registration-type"></a>MSIX-csomag regisztrációs típusának módosítása

A csomag regisztrációs típusának módosítása:

1. Válassza ki a **MSIX-csomagokat**. Ekkor meg kell jelennie a gazdagépen lévő összes meglévő MSIX-csomag listájának.

2. Válassza a **csomag neve** lehetőséget a **MSIX-csomagok rácsban** , ezzel megnyitja a panelt a csomag frissítéséhez.

3. Állítsa be a **regisztrációs típust** az **igény szerinti/bejelentkezési blokkolás** gomb használatával a kívánt módon, majd válassza a **Mentés lehetőséget.**

## <a name="remove-an-msix-package"></a>MSIX-csomag eltávolítása

MSIX-csomag eltávolítása az alkalmazáskészletből:

1. Válassza ki a **MSIX-csomagokat**.  Ekkor meg kell jelennie a gazdagépen lévő összes meglévő MSIX-csomag listájának.

2. Kattintson a jobb oldalon található három pontra a törölni kívánt csomag nevére, majd válassza az **Eltávolítás** lehetőséget.

## <a name="remove-msix-apps"></a>MSIX-alkalmazások eltávolítása

Az egyes MSIX-alkalmazások eltávolítása a csomagból:

1. Nyissa meg a gazdagépet, és válassza ki az **alkalmazáscsoport** elemet.

2. Válassza ki azt az erőforráscsoportot, amelyből el szeretné távolítani a MSIX-alkalmazásait.

3. Nyissa meg az **alkalmazások** lapot.

4. Válassza ki az eltávolítani kívánt alkalmazást, majd kattintson az **Eltávolítás** gombra.

## <a name="next-steps"></a>Következő lépések

Kérje meg a közösségi kérdéseket a szolgáltatással kapcsolatban a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.

Az alábbi cikkek hasznosnak bizonyulnak:

- [MSIX-alkalmazás szószedetének csatolása](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
