---
title: FSLogix-profil tárolók NetApp Windows virtuális asztal – Azure
description: FSLogix-profil tároló létrehozása a Windows rendszerű virtuális asztali Azure NetApp Files használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016828"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Profil tároló létrehozása Azure NetApp Files és AD DS

Javasoljuk, hogy használja a FSLogix-profilok tárolóit felhasználói profil megoldásként a [Windows rendszerű virtuális asztali szolgáltatáshoz](overview.md). A FSLogix-profil tárolói egy teljes felhasználói profilt tárolnak egyetlen tárolóban, és a nem állandó távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban is barangoló profilokat terveztek. Amikor bejelentkezik, a tároló dinamikusan csatlakozik a számítástechnikai környezethez egy helyileg támogatott virtuális merevlemez (VHD) és egy Hyper-V virtuális merevlemez (VHDX) használatával. Ezek a speciális szűrő-illesztőprogram-technológiák lehetővé teszik, hogy a felhasználói profil azonnal elérhető legyen, és pontosan a helyi felhasználói profilhoz hasonlóan jelenjen meg a rendszeren. A FSLogix-profilok tárolókkal kapcsolatos további tudnivalókért tekintse meg a [FSLogix-profilok és az Azure Files](fslogix-containers-azure-files.md)című témakört.

A FSLogix-profil tárolókat a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)segítségével hozhatja létre, amely egy könnyen használható Azure natív platform-szolgáltatás, amely segít az ügyfeleknek gyorsan és megbízhatóan kiépíteni a nagyvállalati szintű SMB-köteteket a Windows rendszerű virtuális asztali környezetekben. További információ a Azure NetApp Filesről: [Mi az Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

Ebből az útmutatóból megtudhatja, hogyan állíthat be egy Azure NetApp Files fiókot, és hogyan hozhat létre FSLogix-profilok tárolókat a Windows virtuális asztalon.

Ez a cikk azt feltételezi, hogy már rendelkezik a Windows rendszerű virtuális asztali környezetben egy vagy több bérlőbe beállított és csoportosított [gazdagépekkel](create-host-pools-azure-marketplace.md) . A bérlők beállításával kapcsolatos további információkért lásd: [bérlő létrehozása a Windows Virtual Desktopban](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) és [a technikai Közösség blogbejegyzése](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Az útmutatóban szereplő utasítások kifejezetten a Windows rendszerű virtuális asztali felhasználók számára készültek. Ha további általános útmutatást szeretne arról, hogyan kell beállítani Azure NetApp Files és létrehozni a FSLogix-profilok tárolóit a Windows virtuális asztalon kívül, tekintse [meg a Azure NetApp Files beállítása és az NFS-kötet létrehozása](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)című rövid útmutatót.

>[!NOTE]
>Ez a cikk nem fedi le az Azure NetApp Files-megosztáshoz való hozzáférés biztosításának ajánlott eljárásait.

>[!NOTE]
>Ha összehasonlító anyagot keres az Azure-beli különböző FSLogix-profilok tárolási lehetőségeivel kapcsolatban, tekintse meg a [FSLogix-profilok tárolási lehetőségei](store-fslogix-profile.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy FSLogix-profilt lehessen létrehozni egy gazdagéphez, a következőket kell tennie:

- Windows rendszerű virtuális asztal beállítása és konfigurálása
- Windows rendszerű virtuális asztali címkészlet kiépítése
- [Azure NetApp Files-előfizetés engedélyezése](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>A Azure NetApp Files fiók beállítása

Első lépésként be kell állítania egy Azure NetApp Files fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Győződjön meg arról, hogy a fiók közreműködői vagy rendszergazdai jogosultságokkal rendelkezik.

2. Válassza a keresősáv jobb oldalán található **Azure Cloud Shell ikont** a Azure Cloud Shell megnyitásához.

3. Azure Cloud Shell megnyitása után válassza a **PowerShell** lehetőséget.

4. Ha első alkalommal használja a Azure Cloud Shellt, hozzon létre egy Storage-fiókot ugyanabban az előfizetésben, és tartsa meg a Azure NetApp Files és a Windows rendszerű virtuális asztalt.

   > [!div class="mx-imgBorder"]
   > ![A Storage-fiók ablak az ablak alján található tároló létrehozása gombbal piros színnel kiemelve.](media/create-storage-button.png)

5. Azure Cloud Shell betöltése után futtassa a következő két parancsmagot.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Az ablak bal oldalán válassza a **minden szolgáltatás** lehetőséget. Adja meg **Azure NetApp Files** a menü tetején megjelenő keresőmezőbe.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel a "Azure NetApp Files" kifejezést a minden szolgáltatás keresőmezőbe. A keresési eredmények a Azure NetApp Files erőforrást jelenítik meg.](media/azure-netapp-files-search-box.png)


7. Válassza a **Azure NetApp Files** lehetőséget a keresési eredmények között, majd válassza a **Létrehozás** lehetőséget.

8. Kattintson a **Hozzáadás** gombra.
9. Amikor megnyílik az **új NetApp-fiók** lap, adja meg a következő értékeket:

    - A **név** mezőben adja meg a NetApp-fiók nevét.
    - Az **előfizetés** mezőben válassza ki a legördülő menüből a 4. lépésben beállított Storage-fiók előfizetését.
    - Az **erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot a legördülő menüből, vagy hozzon létre egy újat az **új létrehozása** lehetőség kiválasztásával.
    - A **hely** mezőben válassza ki a NetApp-fiók régióját a legördülő menüből. Ennek a régiónak meg kell egyeznie a munkamenet-gazda virtuális gépekkel.

   >[!NOTE]
   >A Azure NetApp Files jelenleg nem támogatja a kötetek régiók közötti csatlakoztatását.

10. Ha elkészült, válassza a **Létrehozás** lehetőséget a NetApp-fiók létrehozásához.

## <a name="create-a-capacity-pool"></a>Kapacitási készlet létrehozása

Következő lépésként hozzon létre egy új kapacitási készletet:

1. Lépjen a Azure NetApp Files menüre, és válassza ki az új fiókot.
2. A fiók menüben válassza a **Kapacitási készletek** lehetőséget a Storage szolgáltatás területen.
3. Válassza a **készlet hozzáadása** lehetőséget.
4. Amikor megnyílik az **új kapacitási készlet** lap, adja meg a következő értékeket:

    - A **név** mezőben adja meg az új kapacitási készlet nevét.
    - A **szolgáltatási szint** mezőben válassza ki a kívánt értéket a legördülő menüből. A legtöbb környezethez **prémium szintű támogatást** ajánlunk.
       >[!NOTE]
       >A Premium beállítás a prémium szintű szolgáltatási szint számára elérhető minimális átviteli sebességet biztosítja, amely 256 MBps. Előfordulhat, hogy ezt az átviteli sebességet kell módosítania éles környezetben. A végső átviteli sebesség az [átviteli sebesség korlátaiban](../azure-netapp-files/azure-netapp-files-service-levels.md)ismertetett kapcsolaton alapul.
    - A **méret (TiB)** mezőben adja meg az igényeinek leginkább megfelelő kapacitási készlet méretét. A minimális méret 4 TiB.

5. Amikor végzett, válassza az **OK** gombot.

## <a name="join-an-active-directory-connection"></a>Csatlakozás Active Directory-csatlakozáshoz

Ezután csatlakoznia kell egy Active Directory-csatlakozáshoz.

1. Válassza a lap bal oldalán található menü **Active Directory kapcsolatok** elemét, majd kattintson a JOIN ( **Csatlakozás** ) gombra a **Csatlakozás Active Directory** oldal megnyitásához.

   > [!div class="mx-imgBorder"]
   > ![A csatlakozás Active Directory kapcsolatok menüjének képernyőképe.](media/active-directory-connections-menu.png)

2. A csatlakozás **Active Directory** lapon adja meg a következő értékeket a kapcsolódáshoz:

    - Az **elsődleges DNS** esetében adja meg a környezetben található DNS-kiszolgáló IP-címét, amely képes a tartománynév feloldására.
    - A **tartomány** mezőben adja meg a teljes tartománynevet (FQDN).
    - Az **SMB-kiszolgáló (számítógépfiók) előtagjaként** adja meg azt a karakterláncot, amelyet hozzá szeretne fűzni a számítógépfiók nevéhez.
    - A **Felhasználónév** mezőbe írja be annak a fióknak a nevét, amely a tartományhoz való csatlakozáshoz szükséges engedélyekkel rendelkezik.
    - A **jelszó** mezőben adja meg a fiók jelszavát.

## <a name="create-a-new-volume"></a>Új kötet létrehozása

Ezután létre kell hoznia egy új kötetet.

1. Válassza a **kötetek** lehetőséget, majd kattintson a **kötet hozzáadása** lehetőségre.

2. Amikor megnyílik a **kötet létrehozása** lap, adja meg a következő értékeket:

    - A **kötet neve** mezőben adja meg az új kötet nevét.
    - A **Kapacitási készlet** területen válassza ki az imént létrehozott kapacitás-készletet a legördülő menüből.
    - A **kvóta (GIB)** mezőben adja meg a környezetének megfelelő kötet méretét.
    - A **Virtual Network (virtuális hálózat**) területen válasszon ki egy meglévő virtuális hálózatot, amely a tartományvezérlőhöz kapcsolódik a legördülő menüből.
    - Az **alhálózat** területen válassza az **új létrehozása** lehetőséget. Ne feledje, hogy ez az alhálózat Azure NetApp Files lesz delegálva.

3.  Válassza a **Tovább: \> \> protokoll** lehetőséget a protokoll lap megnyitásához és a mennyiségi hozzáférési paraméterek konfigurálásához.

## <a name="configure-volume-access-parameters"></a>Mennyiségi hozzáférési paraméterek konfigurálása

A kötet létrehozása után konfigurálja a kötet-hozzáférési paramétereket.

1.  Válassza az **SMB** lehetőséget a protokoll típusaként.
2.  A **Active Directory** legördülő menüben a konfiguráció területen válassza ki ugyanazt a könyvtárat, amelyhez eredetileg kapcsolódott a [Csatlakozás egy Active Directory-kapcsolathoz](create-fslogix-profile-container.md#join-an-active-directory-connection). Ne feledje, hogy az előfizetés legfeljebb egy Active Directory.
3.  A **megosztás neve** szövegmezőbe írja be a munkamenet-gazda készlet és a felhasználók által használt megosztás nevét.

4.  Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. Ekkor megnyílik az érvényesítési oldal. A kötet sikeres ellenőrzése után válassza a **Létrehozás** lehetőséget.

5.  Ezen a ponton az új kötet üzembe helyezése megkezdődik. Az üzembe helyezés befejeztével használhatja a Azure NetApp Files megosztást.

6.  A csatlakoztatási útvonal megtekintéséhez válassza az **Ugrás az erőforráshoz** lehetőséget, és keresse meg az Áttekintés lapon.

    > [!div class="mx-imgBorder"]
    > ![Az áttekintő képernyő képernyőképe egy piros nyíllal, amely a csatlakoztatási útvonalra mutat.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>A FSLogix konfigurálása a munkamenet-gazdagépen futó virtuális gépeken (VM)

Ez a szakasz a [címkészlet egy fájlmegosztási használatával történő létrehozásán](create-host-pools-user-profile.md)alapul.

1. [Töltse le a FSLogix Agent. zip fájlt](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) , miközben továbbra is távoli állapotban van a munkamenet-gazda virtuális gépen.

2. Bontsa ki a letöltött fájlt.

3. A fájlban lépjen az x64-   >  **kiadások** és a **FSLogixAppsSetup.exe** futtatása elemre. Ekkor megnyílik a telepítési menü.

4.  Ha van termékkulcs, írja be a termékkulcsot szövegmezőbe.

5. Jelölje be az Elfogadom **a licencfeltételeket lehetőség** melletti jelölőnégyzetet.

6. Válassza a **Telepítés** gombot.

7. Navigáljon a **C: \\ Program Files \\ FSLogix \\ apps** elemre, és erősítse meg az ügynök telepítését.

8. A Start menüben futtassa a **Regedit parancsot** rendszergazdaként.

9. Navigáljon a **számítógép \\ HKEY_LOCAL_MACHINE \\ szoftver \\ FSLogix**.

10. Hozzon létre egy **profilok** nevű kulcsot.

11.  Hozzon létre egy **megnevezett értéket** egy **REG_DWORD** Type értékkel egy **1** értékre állítva.

12. Hozzon létre egy **VHDLocations** nevű értéket egy **többkarakterláncos** típussal, és állítsa az adatértékét az Azure NetApp Files-megosztás URI-ja számára.

13. Hozzon létre egy **DeleteLocalProfileWhenVHDShouldApply** nevű értéket az 1 DWORD értékkel, hogy elkerülje a bejelentkezés előtt a meglévő helyi profilokkal kapcsolatos problémákat.

     >[!WARNING]
     >Legyen körültekintő a DeleteLocalProfileWhenVHDShouldApply érték létrehozásakor. Ha a FSLogix-profilok rendszer határozza meg, hogy a felhasználó rendelkezik-e FSLogix-profillal, de már létezik helyi profil, akkor a profil tároló véglegesen törli a helyi profilt. A felhasználó ezután bejelentkezik az új FSLogix-profilba.

## <a name="assign-users-to-session-host"></a>Felhasználók társítása a munkamenet-gazdagéphez

1. Nyissa meg rendszergazdaként a **POWERSHELL ISE** -t, és jelentkezzen be a Windows rendszerű virtuális asztalra.

2. Futtassa a következő parancsmagokat:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Ha a rendszer a hitelesítő adatok megadását kéri, adja meg a bérlői létrehozó vagy az RDS-tulajdonos/RDS közreműködő szerepkörrel rendelkező felhasználó hitelesítő adatait a Windows rendszerű virtuális asztali bérlőn.

4. Futtassa a következő parancsmagokat egy felhasználó Távoli asztal csoporthoz való hozzárendeléséhez:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Győződjön meg arról, hogy a felhasználók el tudják érni az Azure NetApp-fájlmegosztást

1. Nyissa meg az Internet böngészőt, és lépjen a következőre: <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Jelentkezzen be az Távoli asztal csoporthoz rendelt felhasználó hitelesítő adataival.

3. Miután létrehozta a felhasználói munkamenetet, jelentkezzen be a Azure Portalba egy rendszergazdai fiókkal.

4. Nyissa meg **Azure NetApp Files**, válassza ki a Azure NetApp Files-fiókot, majd válassza a **kötetek** lehetőséget. A kötetek menü megnyitása után válassza ki a megfelelő kötetet.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel a Azure Portal korábban beállított NetApp-fiókról a kötetek gomb kiválasztásával.](media/netapp-account.png)

5. Lépjen az **Áttekintés** lapra, és ellenőrizze, hogy a FSLogix-profil tárolója használ-e helyet.

6. Közvetlenül kapcsolódhat a gazdagép bármely virtuálisgép-részéhez Távoli asztal használatával, és megnyithatja a **fájlkezelőt.** Ezután navigáljon a **csatlakoztatási útvonalhoz** (a következő példában a csatlakoztatási útvonal a \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ ANF-Vol).

   Ebben a mappában léteznie kell egy profilt tartalmazó VHD-nek (vagy VHDX), amely az alábbi példában láthatóhoz hasonló.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a mappa tartalmáról a csatlakoztatási útvonalon. A belül egy "Profile_ssbb" nevű VHD-fájl.](media/mount-path-folder.png)

## <a name="next-steps"></a>Következő lépések

A felhasználói profilok megosztásának beállításához használhatja a FSLogix-profilok tárolóit. Ha meg szeretné tudni, hogyan hozhat létre felhasználói profilokat az új tárolókkal, tekintse meg a következőt: [fájlmegosztás használatával létrehozott profil tárolója](create-host-pools-user-profile.md).

Létrehozhat egy Azure Files fájlmegosztást is, amely a FSLogix-profilját tárolja a alkalmazásban. További információ: [Azure Files fájlmegosztás létrehozása tartományvezérlővel](create-file-share.md).