---
title: Windows Virtual Desktop MSIX-alkalmazás csatolása portál – Azure
description: MSIX-alkalmazás csatolásának beállítása Windows Virtual Desktop a Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0d7598e332539b8203d55bbcb1cf497811c32540
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366555"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>MSIX-alkalmazás csatolásának beállítása az Azure Portal használatával

Ez a cikk bemutatja, hogyan állíthatja be az MSIX-alkalmazás csatolását egy Windows Virtual Desktop környezetben.

## <a name="requirements"></a>Követelmények

>[!IMPORTANT]
>Mielőtt hozzá kezd, töltse ki és [](https://aka.ms/enablemsixappattach) küldje el ezt az űrlapot, hogy engedélyezze az MSIX-alkalmazás csatolását az előfizetésben. Ha nem rendelkezik jóváhagyott kérelemvel, az MSIX-alkalmazás csatolása nem fog működni. A kérelmek jóváhagyása a munkanapok során akár 24 órát is igénybe vehet. E-mailt fog kapni, ha a kérést elfogadták és befejezték.

Az MSIX-alkalmazás csatolásának konfiguráláshoz a következőre van szükség:

- Egy működő Windows Virtual Desktop üzembe helyezéshez. A (klasszikus) Windows Virtual Desktop bérlők üzembe helyezéséről a Bérlő [létrehozása a](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)Windows Virtual Desktop. A gazdagépkészletek Windows Virtual Desktop telepítésével kapcsolatos Azure Resource Manager lásd: Gazdagépkészlet létrehozása [a Azure Portal.](./create-host-pools-azure-marketplace.md)
- Egy Windows Virtual Desktop gazdagépkészlet legalább egy aktív munkamenetgazdával.
- Ennek a gazdagépkészletnek az érvényesítési környezetben kell lennie. 
- Az MSIX csomagolási eszköz.
- MsIX-csomagba csomagolt alkalmazás, amely egy fájlmegosztásba feltöltött MSIX-rendszerképbe van kibontva.
- Egy fájlmegosztás a Windows Virtual Desktop, ahol az MSIX-csomagot tárolni fogja.
- Annak a fájlmegosztásnak, ahová az MSIX-rendszerképet feltöltötte, elérhetőnek kell lennie a gazdagépkészletben lévő összes virtuális gép (VM) számára is. A felhasználóknak csak olvasási engedélyekkel kell rendelkezniük a rendszerkép eléréséhez.
- Ha a tanúsítvány nem megbízható nyilvánosan, kövesse a Tanúsítványok [telepítése útmutatót.](app-attach.md#install-certificates)

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Az MSIX-alkalmazás csatolása alkalmazások automatikus frissítésének kikapcsolása

Mielőtt hozzá kezd, le kell tiltania az MSIX alkalmazáshoz csatoló alkalmazások automatikus frissítéseit. Az automatikus frissítések letiltásához a következő parancsokat kell futtatnia egy rendszergazda jogú parancssorban:

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

## <a name="configure-the-msix-app-attach-management-interface"></a>Az MSIX-alkalmazás csatolásának felügyeleti felületének konfigurálása

Ezután le kell töltenie és konfigurálnia kell az MSIX alkalmazás csatolásának felügyeleti felületét a Azure Portal.

A felügyeleti felület beállítása:

1. [Nyissa meg a Azure Portal.](https://portal.azure.com)
2. Ha a rendszer rákérdez, hogy megbízhatónak tartja-e a bővítményt, válassza az **Allow (Engedélyezése) lehetőséget.**

      > [!div class="mx-imgBorder"]
      > ![A nem megbízható bővítmények ablak képernyőképe. Az "Allow" piros színnel van kiemelve.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>MSIX-rendszerkép hozzáadása a gazdagépkészlethez

Ezután hozzá kell adni az MSIX-rendszerképet a gazdagépkészlethez.

Az MSIX-rendszerkép hozzáadása:

1. Nyissa meg az Azure Portalt.

2. Írja **Windows Virtual Desktop** a keresősávba, majd válassza ki a szolgáltatás nevét.

      > [!div class="mx-imgBorder"]
      > ![Képernyőkép egy felhasználóról, aki a keresősáv legördülő menüjében a "Windows Virtual Desktop" lehetőséget Azure Portal. A "Windows Virtual Desktop" piros színnel van kiemelve.](media/find-and-select.png)

3. Válassza ki azt a gazdagépkészletet, ahová az MSIX-alkalmazásokat el tervezi tenni.

4. Válassza **az MSIX-csomagok** lehetőséget az adatrács megnyitásához, amely a gazdagépkészlethez jelenleg hozzáadott összes **MSIX-csomagot** tartalmazza.

5. Válassza **a + Hozzáadás** lehetőséget az **MSIX-csomag hozzáadása lap megnyitásához.**

6. Az **MSIX-csomag hozzáadása lapon** adja meg a következő értékeket:

      - Az **MSIX-lemezkép elérési útjaként** adjon meg egy érvényes UNC elérési utat, amely a fájlmegosztás MSIX-lemezképére mutat. (Például: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Ha végzett, válassza a **Hozzáadás** lehetőséget az MSIX-tároló leellenőrzéshez, és ellenőrizze, hogy az elérési út érvényes-e.

      - Az **MSIX-csomag mezőben** válassza ki a megfelelő MSIX-csomagnevet a legördülő menüből. Ez a menü csak akkor lesz kitöltve, ha érvényes képútvonalat adott meg az **MSIX-lemezkép elérési útján.**

      - **Csomagalkalmazások esetén** győződjön meg arról, hogy a lista tartalmazza az összes olyan MSIX-alkalmazást, amely elérhetővé szeretne tenni az MSIX-csomagban található felhasználók számára.

      - Ha azt szeretné, hogy **a** csomag felhasználóbarátabb legyen a felhasználói környezetben, adjon meg egy Megjelenítendő nevet.

      - Győződjön meg **arról, hogy** a Verzió a megfelelő verziószámmal rendelkezik.

      - Válassza ki **a használni** kívánt Regisztrációs típust. Az, hogy melyiket használja, az az igényeitől függ:

    - **Az igény szerinti regisztráció** elhalasztja az MSIX-alkalmazás teljes regisztrációját, amíg a felhasználó el nem kezdi az alkalmazást. Javasoljuk, hogy ezt a regisztrációs típust használja.

    - **A bejelentkezés blokkolása** csak akkor regisztrál, amikor a felhasználó bejelentkezik. Ez a típus nem ajánlott, mert a felhasználók számára hosszabb bejelentkezési időt is igénybe vehet.

7.  A **State (Állam)** mezőben válassza ki az előnyben részesített állapotot.
    -  Az **Aktív állapot** lehetővé teszi, hogy a felhasználók kommunikálják a csomagot.
    -  Az **Inaktív** állapot azt Windows Virtual Desktop, hogy a rendszer figyelmen kívül hagyja a csomagot, és nem kézbesíti azt a felhasználóknak.

8. Ha végzett, válassza a Hozzáadás **lehetőséget.**

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-alkalmazások közzététele alkalmazáscsoportban

Ezután közzé kell tennie az alkalmazásokat a csomagban. Ezt az asztali és a távoli alkalmazáscsoportok esetében is meg kell tenni.

Ha már rendelkezik MSIX-rendszerképpel, ugorjon az MSIX-alkalmazások közzététele [egy alkalmazáscsoportban részhez.](#publish-msix-apps-to-an-app-group) Ha örökölt alkalmazásokat szeretne tesztelni, kövesse az [MSIX-csomag](/windows/msix/packaging-tool/create-app-package-msi-vm/) létrehozása asztali telepítőből virtuális gépen útmutatásait az örökölt alkalmazás MSIX-csomagká való átalakításához.

Az alkalmazások közzététele:

1. A Windows Virtual Desktop erőforrás-szolgáltatónál válassza az **Alkalmazáscsoportok** lapot.

2. Válassza ki azt az alkalmazáscsoportot, amelybe az alkalmazásokat közzé szeretné tenni.

   >[!NOTE]
   >Az MSIX-alkalmazások az MSIX-alkalmazás távoli alkalmazáscsoportokhoz és asztali alkalmazáscsoportokhoz való csatolásával is kézbesíthatók

3. Az alkalmazáscsoportba való beszúrás után válassza az **Alkalmazások** lapot. Az **Alkalmazások rács** megjeleníti az alkalmazáscsoporton belüli összes meglévő alkalmazást.

4. Válassza **a + Hozzáadás** lehetőséget az Alkalmazás hozzáadása lap **megnyitásához.**

      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a felhasználó + Hozzáadás lehetőségről az Alkalmazás hozzáadása lap megnyitásához](media/select-add.png)

5. Az **Alkalmazás forrásaként** válassza ki az alkalmazás forrását.
    - Asztali alkalmazáscsoport használata esetén válassza az **MSIX-csomag lehetőséget.**
      
      > [!div class="mx-imgBorder"]
      > ![Képernyőkép egy ügyfélről, aki kiválasztja az MSIX-csomagot az alkalmazásforrás legördülő menüjéből. Az MSIX-csomag piros színnel van kiemelve.](media/select-source.png)
    
    - Ha távoli alkalmazáscsoportot használ, válasszon az alábbi lehetőségek közül:
        
        - Start menü
        - Alkalmazás elérési útja
        - MSIX-csomag

    - Az **Alkalmazás neve alatt** adjon meg egy leíró nevet az alkalmazásnak.

    A következő választható funkciókat is konfigurálhatja:
   
    - A **Megjelenítendő név alatt** adjon meg egy új nevet a csomagnak, amit a felhasználók látni fognak.

    - A **Leírás mezőben** adja meg az alkalmazáscsomag rövid leírását.

    - Ha távoli alkalmazáscsoportot használ, a következő beállításokat is konfigurálhatja:

        - **Ikon elérési útja**
        - **Ikonindex**
        - **Show in web feed (Megjelenítése a webes hírcsatornában)**

6. Amikor elkészült, válassza a **Mentés** lehetőséget.

>[!NOTE]
>Ha egy felhasználó távoli alkalmazáscsoporthoz és asztali alkalmazáscsoporthoz van rendelve ugyanattól a gazdagépkészlettől, az asztali alkalmazáscsoport megjelenik a hírcsatornában.

## <a name="assign-a-user-to-an-app-group"></a>Felhasználó hozzárendelése alkalmazáscsoporthoz

Miután MSIX-alkalmazásokat adott hozzá egy alkalmazáscsoporthoz, hozzáférést kell ad a felhasználóknak. A hozzáférést úgy rendelheti hozzá, hogy felhasználókat vagy felhasználói csoportokat ad hozzá egy közzétett MSIX-alkalmazásokat használó alkalmazáscsoporthoz. Kövesse az Alkalmazáscsoportok kezelése [a](manage-app-groups.md) Azure Portal útmutatását a felhasználók alkalmazáscsoporthoz való hozzárendeléséhez.

>[!NOTE]
>Előfordulhat, hogy az MSIX-alkalmazás távoli alkalmazások csatolása eltűnik a hírcsatornából, amikor a nyilvános előzetes verzióban teszteli a távoli alkalmazásokat. Az alkalmazások nem jelennek meg, mert a kiértékelési környezetben használt gazdagépkészletet egy RD-közvetítő szolgálja ki az éles környezetben. Mivel az éles környezetben a távoli asztali közvetítő nem regisztrálja a távoli alkalmazásokat csatoló MSIX-alkalmazás jelenlétét, az alkalmazások nem jelennek meg a hírcsatornában.

## <a name="change-msix-package-state"></a>MSIX-csomag állapotának módosítása

Ezután az MSIX-csomag állapotát Aktív vagy  **Inaktív** állapotra kell módosítania attól függően, hogy mit szeretne tenni a csomaggal. Az aktív csomagok olyan csomagok, amelyek a felhasználók számára elérhetőek a közzététel után. Az inaktív csomagokat a Windows Virtual Desktop figyelmen kívül hagyja, így a felhasználók nem léphetnek interakcióba a belső alkalmazásokkal.

### <a name="change-state-with-the-applications-list"></a>Állapot módosítása az Alkalmazások listával

A csomag állapotának módosítása az Alkalmazások listával:

1. A gazdagépkészletben válassza az **MSIX-csomagok lehetőséget.** A gazdagépkészletben megjelenik az összes meglévő MSIX-csomag listája.

2. Jelölje ki azokat az MSIX-csomagokat, amelyeknek módosítania kell az állapotokat, majd válassza **az Állapot módosítása lehetőséget.**

### <a name="change-state-with-update-package"></a>Állapot módosítása frissítési csomaggal

A csomag állapotának módosítása egy frissítési csomaggal:

1. A gazdagépkészletben válassza az **MSIX-csomagok lehetőséget.** A gazdagépkészletben megjelenik az összes meglévő MSIX-csomag listája.

2. Válassza ki annak a csomagnak a nevét, amelynek állapotát módosítani szeretné az MSIX-csomagok listájából. Ezzel megnyitja a **Csomag frissítése lapot.**

3. Váltsa az **Állapot kapcsolót** **Inaktív** vagy **Aktív** állapotra, majd válassza a Mentés **lehetőséget.**

## <a name="change-msix-package-registration-type"></a>MSIX-csomagregisztráció típusának módosítása

A csomag regisztrációs típusának módosítása:

1. Válassza az **MSIX-csomagok lehetőséget.** A gazdagépkészletben megjelenik az összes meglévő MSIX-csomag listája.

2. Válassza **a Csomag neve lehetőséget** az **MSIX-csomagok rácsban.** Ezzel megnyitja a panelt a csomag frissítéséhez.

3. Igény szerint válthat **a Regisztráció típusa** között az Igény **szerinti/Bejelentkezés blokkolási** gombbal, majd válassza a Mentés **lehetőséget.**

## <a name="remove-an-msix-package"></a>MSIX-csomag eltávolítása

MSIX-csomag eltávolítása a gazdagépkészletből:

1. Válassza az **MSIX-csomagok lehetőséget.**  A gazdagépkészletben megjelenik az összes meglévő MSIX-csomag listája.

2. Kattintson a törölni kívánt csomag nevének jobb oldalán található három pontra, majd válassza az Eltávolítás **lehetőséget.**

## <a name="remove-msix-apps"></a>MSIX-alkalmazások eltávolítása

Egyes MSIX-alkalmazások eltávolítása a csomagból:

1. A gazdagépkészletben válassza az **Alkalmazáscsoportok lehetőséget.**

2. Válassza ki azt az alkalmazáscsoportot, amelyből el szeretné távolítani az MSIX-alkalmazásokat.

3. Nyissa meg **az Alkalmazások** lapot.

4. Válassza ki az eltávolítani kívánt alkalmazást, majd válassza az Eltávolítás **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

A funkcióval kapcsolatos kérdéseit a [TechCo Windows Virtual Desktop fel.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

Visszajelzést is küldhet a Windows Virtual Desktop a [Windows Virtual Desktop visszajelzési központban.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Íme néhány további cikk, amelyek hasznosak lehetnek:

- [MSIX-alkalmazás csatolása – szószedet](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
