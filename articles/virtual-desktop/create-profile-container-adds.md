---
title: FSLogix-profil tárolójának létrehozása Azure Files Active Directory Domain Services – Azure
description: Ez a cikk azt ismerteti, hogyan hozható létre FSLogix-profil tároló Azure Files és Azure Active Directory Domain Services használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 04/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 59e2fc1f528040515398e51d359840f6ef1bbefc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255800"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Profil tároló létrehozása Azure Files és Azure AD DS

Ez a cikk bemutatja, hogyan hozhat létre egy FSLogix-profil tárolót Azure Files és Azure Active Directory Domain Services (AD DS) használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már beállított egy Azure AD DS-példányt. Ha még nem rendelkezik ilyennel, kövesse az [alapszintű felügyelt tartomány létrehozása](../active-directory-domain-services/tutorial-create-instance.md) című részben leírtakat, majd térjen vissza ide.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS-rendszergazdák hozzáadása

További rendszergazdák hozzáadásához hozzon létre egy új felhasználót, és adja meg nekik az engedélyeket.

Rendszergazda hozzáadása:

1. Válassza a **Azure Active Directory** lehetőséget az oldalsávon, majd válassza a **minden felhasználó** lehetőséget, majd válassza az **új felhasználó** lehetőséget.

2.  Adja meg a felhasználói adatokat a mezőkben.

3. A képernyő bal oldalán található Azure Active Directory ablaktáblán válassza a **csoportok** lehetőséget.

4. Válassza a **HRE DC-rendszergazdák** csoportot.

5. A bal oldali ablaktáblán válassza a **tagok** lehetőséget, majd válassza a **Tagok hozzáadása** elemet a fő ablaktáblán. Ez megjeleníti az Azure AD-ben elérhető összes felhasználó listáját. Válassza ki az imént létrehozott felhasználói profil nevét.

## <a name="set-up-an-azure-storage-account"></a>Azure Storage-fiók beállítása

Itt az ideje, hogy engedélyezi az Azure AD DS a hitelesítést a Server Message Block (SMB) protokollon keresztül.

A hitelesítés engedélyezése:

1. Ha még nem tette meg, állítson be és helyezzen üzembe egy általános célú v2-es Azure Storage-fiókot az [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)című témakör utasításait követve.

2. Ha befejezte a fiók beállítását, válassza **az Ugrás az erőforráshoz** lehetőséget.

3. Válassza a **konfiguráció** elemet a képernyő bal oldalán, majd engedélyezze a **Azure Files Azure Active Directory hitelesítését** a fő ablaktáblán. Amikor elkészült, válassza a **Mentés** lehetőséget.

4. Válassza az **Áttekintés** lehetőséget a képernyő bal oldalán, majd a fő ablaktáblán válassza a **fájlok** lehetőséget.

5. Válassza a **fájlmegosztás** lehetőséget, és írja be a **nevet** és a **kvótát** a képernyő jobb oldalán megjelenő mezőkbe.

## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek kiosztása identitáshoz

Más felhasználóknak hozzáférési engedélyekre van szükségük a fájlmegosztás eléréséhez. Ehhez minden felhasználóhoz hozzá kell rendelnie egy szerepkört a megfelelő hozzáférési engedélyekkel.

Felhasználói hozzáférési engedélyek kiosztása:

1. A Azure Portal nyissa meg az [Azure Storage-fiók beállítása](#set-up-an-azure-storage-account)című részében létrehozott fájlmegosztást.

2. Válassza a **Access Control (iam)** lehetőséget.

3. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.

4. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a megfelelő beépített szerepkört a szerepkör listából. A megfelelő engedélyek beszerzéséhez legalább a **Storage file-ADATsmb-megosztás közreműködőjét** kell választania a fiókhoz.

5. A **hozzáférésének hozzárendeléséhez** válassza ki **Azure Active Directory felhasználót, csoportot vagy egyszerű szolgáltatásnevet**.

6. Válassza ki a cél Azure Active Directory identitás nevét vagy e-mail-címét.

7. Kattintson a **Mentés** gombra.

## <a name="get-the-storage-account-access-key"></a>A Storage-fiók elérési kulcsának beolvasása

Ezután be kell szereznie a Storage-fiókhoz tartozó hozzáférési kulcsot.

A Storage-fiók elérési kulcsának beszerzése:

1. A Azure Portal oldalsávon válassza a **Storage-fiókok** lehetőséget.

2. A Storage-fiókok listájából válassza ki azt a fiókot, amelyhez engedélyezve van az Azure AD DS, és hozza létre az egyéni szerepköröket a fenti lépésekben.

3. A **Beállítások** területen válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kulcsot a **key1**.

4. Lépjen a **Virtual Machines** lapra, és keresse meg a gazdagép-készlet részévé váló virtuális gépeket.

5. Válassza ki a virtuális gép (VM) nevét **Virtual Machines (adVM)** alatt, és válassza a **kapcsolat** lehetőséget.

    Ezzel letölt egy RDP-fájlt, amely lehetővé teszi, hogy a saját hitelesítő adataival jelentkezzen be a virtuális gépre.

    > [!div class="mx-imgBorder"]
    > ![A virtuális gép kapcsolódása ablak RDP lapjának képernyőképe.](media/rdp-tab.png)

6. Amikor bejelentkezett a virtuális gépre, futtassa a parancssort rendszergazdaként.

7. Futtassa az alábbi parancsot:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Cserélje le a `<desired-drive-letter>` betűt a kívánt meghajtóbetűjelre (például: `y:` ).
    - Cserélje le az összes példányát a `<storage-account-name>` korábban megadott Storage-fiók nevére.
    - Cserélje le a helyére a `<share-name>` korábban létrehozott megosztás nevét.
    - Cserélje le `<storage-account-key>` a elemet az Azure Storage-fiók kulcsára.

    Például:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile
     ```

8. A következő parancsok futtatásával engedélyezheti, hogy a Windows rendszerű virtuális asztali felhasználók saját profilt hozzanak létre, miközben blokkolja a más felhasználóktól származó profilok tárolóhoz való hozzáférést.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Cserélje le a `<mounted-drive-letter>` betűt arra a meghajtóra, amelyet a meghajtó leképezéséhez használt.
    - A helyére írja be annak a `<user-email>` felhasználónak vagy Active Directory csoportnak az egyszerű felhasználónevét, amely a megosztáshoz való hozzáférést igénylő felhasználókat tartalmazza.

    Például:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Profiltároló létrehozása

Most, hogy a profilok készen állnak, hozzon létre egy FSLogix-profil tárolót.

FSLogix-profil tárolójának konfigurálása:

1. Jelentkezzen be a cikk elején konfigurált munkamenet-gazda virtuális gépre, majd [töltse le és telepítse a FSLogix-ügynököt](/fslogix/install-ht/).

2. Bontsa ki a letöltött FSLogix-ügynök fájlját   >  , és nyissa meg az x64-es **kiadásokat**, majd nyissa meg **FSLogixAppsSetup.exe**.

3. A telepítő elindítása után válassza az Elfogadom **a licencfeltételeket lehetőséget.** Ha van ilyen, adjon meg egy új kulcsot.

4. Válassza a **Telepítés** gombot.

5. Nyissa meg a **C meghajtót**, majd lépjen a **Program Files**  >  **FSLogix**  >  **alkalmazások** elemre, és győződjön meg arról, hogy a FSLogix-ügynök megfelelően van telepítve.

     >[!NOTE]
     > Ha több virtuális gép van a gazdagépen, az egyes virtuális gépekhez az 1 – 5. lépést kell megismételni.

6. Futtassa a **Beállításszerkesztőt** (Regedit) rendszergazdaként.

7. Navigáljon a **számítógép**  >  **HKEY_LOCAL_MACHINE**  >  **szoftver**  >  **FSLogix**, kattintson a jobb gombbal a **FSLogix** elemre, válassza az **új**, majd a **kulcs** elemet.

8. Hozzon létre egy új, **profilok** nevű kulcsot.

9.  Kattintson a jobb gombbal a **profilok** elemre, válassza az **új** lehetőséget, majd válassza a **DWORD (32 bites) értéket.** Nevezze el az **engedélyezett** értéket, és állítsa az **adatértéket** **1-re**.

    > [!div class="mx-imgBorder"]
    > ![A profilok kulcs képernyőképe. A REG_DWORD fájl ki van emelve, és az adatérték értéke 1.](media/dword-value.png)

10. Kattintson a jobb gombbal a **profilok** elemre, válassza az **új**, majd a **többkarakterláncos érték** elemet. Nevezze el az érték **VHDLocations** , és adja meg a Azure Files-megosztás URI-ját `\\fsprofile.file.core.windows.net\share` adatértékként.

    > [!div class="mx-imgBorder"]
    > ![A VHDLocations fájlt bemutató profilok kulcs képernyőképe. Az adatértéke a Azure Files-megosztás URI-JÁT jeleníti meg.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Felhasználók társítása egy munkamenet-gazdagéphez

Most hozzá kell rendelnie a felhasználókat a munkamenet-gazdagéphez.

Felhasználók kiosztása:

1. Futtassa a Windows PowerShellt rendszergazdaként, majd futtassa a következő parancsmagot a Windows rendszerű virtuális asztalra való bejelentkezéshez a PowerShell használatával:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Ha a rendszer a hitelesítő adatok megadását kéri, adja meg ugyanazt a felhasználót, aki a Windows rendszerű virtuális asztali bérlőn TenantCreator, RDS tulajdonosi vagy RDS-közreműködői szerepkört kapott.

2. A következő parancsmagok futtatásával rendelje hozzá a felhasználót a távoli asztali csoporthoz:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    A korábbi parancsmagokhoz hasonlóan a megfelelő értékeket cserélje le a, a és a kifejezésre `<your-wvd-tenant>` `<wvd-pool>` `<user-principal>` .

    Például:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Ellenőrizze, hogy a profil működik-e

Most mindössze annyit kell tennie, hogy a létrehozott profil létezik, és a kívánt módon működik.

A profil ellenőrzése:

1. Nyisson meg egy böngészőt, és nyissa meg [a Windows rendszerű virtuális asztali webes ügyfélprogramot](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Jelentkezzen be az Távoli asztal csoporthoz rendelt felhasználói fiókkal.

3. A felhasználói munkamenet létrehozása után nyissa meg a Azure Portal, és jelentkezzen be egy rendszergazdai fiókkal.

4. Az oldalsávon válassza a **Storage-fiókok** lehetőséget.

5. Válassza ki azt a Storage-fiókot, amelyet a munkamenet-gazdagéphez tartozó fájlmegosztásként konfigurált, és amelyen engedélyezve van az Azure AD DS.

6. Válassza ki a **fájlok** ikont, majd bontsa ki a megosztást.

    Ha minden megfelelően van beállítva, a következőhöz hasonló nevű **könyvtárat** kell megjelennie: `<user SID>-<username>` .

## <a name="next-steps"></a>Következő lépések

Ha alternatív módszereket keres a FSLogix-profilok létrehozásához, tekintse meg a következő cikkeket:

- [Hozzon létre egy fájlmegosztási tárolót egy gazdagéphez a fájlmegosztás használatával](create-host-pools-user-profile.md).
- [FSLogix-profil tárolójának létrehozása a Azure NetApp Files használatával](create-fslogix-profile-container.md)

A [FSlogix-FSlogix és az Azure Files](fslogix-containers-azure-files.md)-ban az Azure Files-tárolókkal kapcsolatos fogalmakról részletesebb információkat talál.
