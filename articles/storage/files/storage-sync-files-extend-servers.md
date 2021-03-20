---
title: Oktatóanyag – Windows-fájlkiszolgálók kiterjesztése a Azure File Syncsal | Microsoft Docs
description: Megtudhatja, hogyan terjesztheti ki a Windows-fájlkiszolgálók Azure File Syncokkal való bővítését az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329456"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával

A cikk bemutatja a Windows Server tárolási kapacitásának a Azure File Sync használatával történő kiterjesztésének alapvető lépéseit. Bár az oktatóanyag a Windows Servert Azure-beli virtuális gépekként (VM) is tartalmazza, ezt a folyamatot általában a helyszíni kiszolgálókon végezze el. A Azure File Sync üzembe helyezésére vonatkozó utasításokat az [üzembe helyezési Azure file Sync](storage-sync-files-deployment-guide.md) cikkben találja.

> [!div class="checklist"]
> * A Társzinkronizálási szolgáltatás üzembe helyezése
> * A Windows Server előkészítése az Azure File Sync használatára
> * Az Azure File Sync-ügynök telepítése
> * A Windows Server regisztrálása a Storage Sync szolgáltatással
> * Szinkronizálási csoport és felhő végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben az oktatóanyagban a következőket kell elvégeznie, mielőtt üzembe helyezi Azure File Sync:

- Azure Storage-fiók és-fájlmegosztás létrehozása
- Windows Server 2016 Datacenter rendszerű virtuális gép beállítása
- A Windows Server rendszerű virtuális gép előkészítése Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Mappa és .txt fájl létrehozása

A helyi számítógépen hozzon létre egy új, _FilesToSync_ nevű mappát, és adjon hozzá egy _mytestdoc.txt_ nevű szöveges fájlt. Az oktatóanyag későbbi részében fel kell töltenie a fájlt a fájlmegosztásba.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Egy Azure Storage-fiók üzembe helyezése után létre kell hoznia egy fájlmegosztást.

1. A Azure Portal válassza az **Ugrás erőforráshoz** lehetőséget.
1. Válassza a **fájlok** elemet a Storage-fiók ablaktáblán.

    ![Fájlok kiválasztása](./media/storage-sync-files-extend-servers/click-files.png)

1. Válassza a **+ fájlmegosztás** lehetőséget.

    ![Válassza a fájlmegosztás hozzáadása gombot](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az új fájlmegosztás _afsfileshare_. Adja meg az "1" értéket a **kvótához**, majd válassza a **Létrehozás** lehetőséget. A kvóta legfeljebb 5 TiB lehet, de ebben az oktatóanyagban csak 1 GB-ra van szükségünk.

    ![Adjon meg nevet és kvótát az új fájlmegosztás számára](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Válassza ki az új fájlmegosztást. A fájlmegosztás helyén válassza a **feltöltés** lehetőséget.

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Keresse meg a _FilesToSync_ mappát, ahová a. txt fájlt létrehozta, válassza a _mytestdoc.txt_ lehetőséget, majd válassza a **feltöltés** lehetőséget.

    ![Fájlmegosztás tallózása](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton létrehozta a Storage-fiókot és egy fájlmegosztást egy fájllal. Ezután telepítsen egy Azure-beli virtuális gépet a Windows Server 2016 Datacenter használatával, amely a helyszíni kiszolgálót képviseli ebben az oktatóanyagban.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és adatlemez csatolása

1. Lépjen a Azure Portalra, és bontsa ki a bal oldali menüt. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Az **Azure Marketplace** -erőforrások listája fölötti keresőmezőbe keresse meg a **Windows Server 2016 Datacenter** kifejezést, és válassza ki a kívánt eredményt. Válassza a **Létrehozás** lehetőséget.
1. Nyissa meg az **alapok** lapot. A **Project Details (projekt részletei**) területen válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Adja meg a virtuális gép alapszintű adatait a portál paneljén](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **példány részletei** területen adja meg a virtuális gép nevét. Használja például a _myVM_.
1. Ne módosítsa a **régió**, a **rendelkezésre állási beállítások**, a **képek** és a **méret** alapértelmezett beállításait.
1. A **Rendszergazdai fiók** területen adjon meg egy **Felhasználónevet** és egy **Jelszót** a virtuális géphez.
1. A **bejövő portszabályok** területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd az **RDP (3389)** és a **http** elemet a legördülő menüből.

1. A virtuális gép létrehozása előtt el kell készíteni az adatlemezt.

   1. Válassza a **Tovább: lemezek** lehetőséget.

      ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. A **lemezek** lap **lemez beállításai** területén hagyja meg az alapértelmezett értékeket.
   1. Az **ADATlemezek** területen válassza az **új lemez létrehozása és csatolása** lehetőséget.

   1. Használja az alapértelmezett beállításokat a **méret (GIB)** kivételével, amelyet az OKTATÓANYAG **1 GB** -ra válthat.

      ![Adatlemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Válassza az **OK** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   Az **értesítések** ikont kiválasztva megtekintheti a **telepítési folyamatot**. Egy új virtuális gép létrehozása eltarthat néhány percig.

1. A virtuális gép üzembe helyezésének befejezése után válassza **az Ugrás erőforráshoz** lehetőséget.

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Ezután kapcsolódjon a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. A Azure Portal a virtuális gép tulajdonságai lapon válassza a **kapcsolat** lehetőséget.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. A **Csatlakozás virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat, hogy az 3389-as porton keresztül kapcsolódjon az **IP-címek** között. Válassza az **RDP-fájl letöltése** lehetőséget.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és válassza a **Kapcsolódás** lehetőséget, amikor a rendszer kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet a *localhost\username* mezőbe, adja meg a virtuális gép számára létrehozott jelszót, majd kattintson **az OK gombra**.

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás létrehozásához.

### <a name="prepare-the-windows-server"></a>A Windows Server előkészítése

A Windows Server 2016 Datacenter kiszolgálóra tiltsa le az Internet Explorer fokozott biztonsági beállításokat. Ez a lépés csak a kiszolgáló kezdeti regisztrációjakor szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

A Windows Server 2016 Datacenter rendszerű virtuális gépen a Kiszolgálókezelő automatikusan megnyílik.  Ha a Kiszolgálókezelő alapértelmezés szerint nem nyílik meg, keresse meg a Start menüben.

1. A **Kiszolgálókezelőben** válassza a **helyi kiszolgáló** lehetőséget.

   ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. A **Tulajdonságok** ablaktáblán válassza ki az **Internet Explorer fokozott biztonsági beállításainak** hivatkozását.  

    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az **Internet Explorer fokozott biztonsági beállítások** párbeszédpanelen válassza a **Kikapcsolva** lehetőséget a **Rendszergazdák** és a **Felhasználók** elemekre.

    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Most már az adatlemezt is hozzáadhatja a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. Miközben továbbra is a **Windows Server 2016 Datacenter** VM-ben van, válassza a **fájlok és tárolási szolgáltatások**  >  **kötetek**  >  **lemez** lehetőséget.

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal a **MSFT virtuális lemez** nevű 1 GB-os lemezre, és válassza az **új kötet** lehetőséget.
1. Végezze el a varázsló lépéseit. Használja az alapértelmezett beállításokat, és jegyezze fel a hozzárendelt meghajtóbetűjelet.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza a **Bezárás** gombot.

   Ezen a ponton a lemez online állapotba került és létrehozott rajta egy kötetet. A Windows Server rendszerű virtuális gépen nyissa meg a fájlkezelőt, és erősítse meg a közelmúltban hozzáadott adatlemez jelenlétét.

1. A virtuális gép fájlkezelőben bontsa ki **ezt a számítógépet** , és nyissa meg az új meghajtót. Ebben a példában az F: meghajtóról van szó.
1. Kattintson a jobb gombbal, és válassza az **új**  >  **mappa** lehetőséget. A mappa neve legyen _FilesToSync_.
1. Nyissa meg a **FilesToSync** mappát.
1. Kattintson a jobb gombbal, és válassza az **új**  >  **szöveges dokumentum** lehetőséget. A szövegfájl neve legyen _MyTestFile_.

    ![Új szöveges fájl hozzáadása](media/storage-sync-files-extend-servers/new-file.png)

1. A **fájlkezelő** és a **Kiszolgálókezelő** bezárásához.

### <a name="download-the-azure-powershell-module"></a>A Azure PowerShell modul letöltése

Ezután a Windows Server 2016 Datacenter VM-ben telepítse a Azure PowerShell modult a kiszolgálóra.

1. Nyisson meg egy rendszergazda jogú PowerShell-ablakot a virtuális gépen.
1. Futtassa az alábbi parancsot:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Ha a 2.8.5.201-nál régebbi NuGet-verzióval rendelkezik, a rendszer felkéri a NuGet legújabb verziójának letöltésére és telepítésére.

   Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. Amikor először használja a PSGallery, a következő üzenet jelenik meg:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. A telepítés folytatásához válaszoljon az **Igen** vagy **az Igen értékre** .

Az `Az` modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Ezen a ponton beállította a környezetét az oktatóanyaghoz. Készen áll a Storage Sync szolgáltatás üzembe helyezésére.

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Azure File Sync telepítéséhez először helyezzen el egy **Storage Sync Service** -erőforrást egy erőforráscsoporthoz a kiválasztott előfizetéshez. A Storage Sync szolgáltatás örökli a hozzáférési engedélyeket az előfizetéséhez és az erőforráscsoporthoz.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget, majd keresse meg a **Azure file Sync**.
1. Válassza ki az **Azure File Sync** elemet a keresési eredmények közül.
1. Válassza ki **Létrehozás** elemet a **Társzinkronizálás üzembe helyezése** lap megnyitásához.

   ![Társzinkronizálás üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Név** | A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).<br><br>A _afssyncservice02_ használata ehhez az oktatóanyaghoz. |
   | **Előfizetés** | Az oktatóanyaghoz használt Azure-előfizetés. |
   | **Erőforráscsoport** | A Storage Sync szolgáltatást tartalmazó erőforráscsoport.<br><br>A _afsresgroup101918_ használata ehhez az oktatóanyaghoz. |
   | **Hely** | USA keleti régiója |

1. Ha elkészült, válassza ki a **Létrehozás** elemet a **Társzinkronizálási szolgáltatás** üzembe helyezéséhez.
1. Válassza az **értesítések** lapot > **Ugrás az erőforráshoz** lehetőségre.

## <a name="install-the-agent"></a>Az ügynök telepítése

Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással.

1. A **Windows Server 2016 Datacenter rendszerű** virtuális gépen nyissa meg az **Internet Explorert**.
1. Nyissa meg a [Microsoft Download Centert](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le a **Azure file Sync ügynök** szakaszhoz, és válassza a **Letöltés** lehetőséget.

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a **StorageSyncAgent_V3_WS2016.EXE** jelölőnégyzetét, majd válassza a **tovább** lehetőséget.

   ![Ügynök kiválasztása](media/storage-sync-files-extend-servers/select-agent.png)

1. Válassza az **Engedélyezés egyszeri**  >  **Futtatás**  >  **Megnyitás** lehetőséget.
1. Ha még nem tette meg, zárja be a PowerShell-ablakot.
1. Fogadja el az alapértelmezett beállításokat a **Társzinkronizálási ügynök telepítő varázslóban**.
1. Válassza a **Telepítés** gombot.
1. Válassza a **Befejezés** gombot.

Sikeresen üzembe helyezte az Azure Sync szolgáltatást és telepítette az ügynököt a Windows Server 2016 Datacenter virtuális gépre. Most regisztrálnia kell a virtuális gépet a Társzinkronizálási szolgáltatásra.

## <a name="register-windows-server"></a>A Windows Server regisztrálása

A Windows Server Storage Sync szolgáltatással való regisztrálása megbízhatósági kapcsolatot létesít a kiszolgáló (vagy fürt) és a Storage Sync szolgáltatás között. A kiszolgálók csak egy Storage Sync szolgáltatásban regisztrálhatók. Szinkronizálható más kiszolgálókkal és az adott Storage Sync szolgáltatáshoz társított Azure-fájlmegosztás használatával.

A kiszolgáló regisztrációjának felhasználói felületének automatikusan meg kell nyílnia a Azure File Sync ügynök telepítése után. Ha nem, akkor manuálisan is megnyithatja a fájl helye: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Amikor megnyílik a kiszolgáló regisztrálása KEZELŐFELÜLET a virtuális gépen, kattintson az **OK gombra**.
1. A kezdéshez válassza **a bejelentkezés** lehetőséget.
1. Jelentkezzen be az Azure-fiókja hitelesítő adataival, és válassza a **Bejelentkezés** lehetőséget.
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | Érték | Leírás |
   | ----- | ----- |
   | **Azure-előfizetés** | Az előfizetés, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | A Storage Sync szolgáltatást tartalmazó erőforráscsoport. A _afsresgroup101918_ használata ehhez az oktatóanyaghoz. |
   | **Társzinkronizálási szolgáltatás** | A Storage Sync szolgáltatás neve. A _afssyncservice02_ használata ehhez az oktatóanyaghoz. |

1. A kiszolgáló regisztrációjának befejezéséhez válassza a **regisztráció** lehetőséget.
1. A regisztrációs folyamat részeként meg kell adnia egy további bejelentkezést. Jelentkezzen be, és kattintson a **Tovább gombra**.
1. Válassza az **OK** lehetőséget.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása

A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoportoknak egyetlen felhőalapú végpontot kell tartalmazniuk, amely egy Azure-fájlmegosztást jelöl. Egy szinkronizálási csoportnak egy vagy több kiszolgálói végpontot is tartalmaznia kell. A kiszolgálói végpont egy elérési utat jelöl egy regisztrált kiszolgálón. Szinkronizálási csoport létrehozása:

1. A [Azure Portal](https://portal.azure.com/)válassza a **+ szinkronizálás csoport** lehetőséget a Storage Sync szolgáltatásból. A *afssyncservice02* használata ehhez az oktatóanyaghoz.

   ![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. A következő információk megadásával hozzon létre egy szinkronizálási csoportot egy felhőalapú végponttal:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név. A *afssyncgroup* használata ehhez az oktatóanyaghoz.|
   | **Előfizetés** | Az előfizetés, ahova a Társzinkronizálási szolgáltatást telepítette ehhez az oktatóanyaghoz. |
   | **Storage-fiók** | Válassza a **Storage-fiók kiválasztása** lehetőséget. A megjelenő panelen válassza ki azt a Storage-fiókot, amelyhez a létrehozott Azure-fájlmegosztás tartozik. A *afsstoracct101918* használata ehhez az oktatóanyaghoz. |
   | **Azure-fájlmegosztás** | A létrehozott Azure-fájlmegosztás neve. A *afsfileshare* használata ehhez az oktatóanyaghoz. |

1. Válassza a **Létrehozás** lehetőséget.

Ha kiválasztja a szinkronizálási csoportot, akkor láthatja, hogy most már rendelkezik egy **felhőbeli végponttal**.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele

A kiszolgálói végpont a regisztrált kiszolgáló egy adott helyét jelöli. Például egy mappa egy kiszolgáló kötetén. Kiszolgálói végpont hozzáadása:

1. Válassza ki az újonnan létrehozott szinkronizálási csoportot, majd válassza a **kiszolgáló-végpont hozzáadása** elemet.

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. A kiszolgálói végpont **hozzáadása** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

   | Érték | Leírás |
   | ----- | ----- |
   | **Regisztrált kiszolgáló** | A létrehozott kiszolgáló neve. A *afsvm101918* használata ehhez az oktatóanyaghoz. |
   | **Elérési út** | A létrehozott meghajtó Windows Server-elérési útja. A *f:\filestosync* használata ebben az oktatóanyagban. |
   | **Felhőbeli rétegzés** | Hagyja letiltott állapotban az oktatóanyaghoz. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyjuk. |

1. Válassza a **Létrehozás** lehetőséget.

A fájlok az Azure-fájlmegosztások és a Windows Server között most már szinkronizálva vannak.

![Az Azure Storage szinkronizálása sikeresen megtörtént](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné az oktatóanyagban létrehozott erőforrásokat, először távolítsa el a végpontokat a Storage Sync szolgáltatásból. Ezután szüntesse meg a kiszolgáló regisztrációját a Storage Sync szolgáltatásban, távolítsa el a szinkronizálási csoportokat, és törölje a szinkronizálási szolgáltatást.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte az alapvető lépéseket, amelyekkel kiterjesztheti a Windows Server tárolási kapacitását Azure File Sync használatával. A Azure File Sync központi telepítésének megtervezése alaposabban megtekinthető:

> [!div class="nextstepaction"]
> [Azure File Sync üzembe helyezésének megtervezése](./storage-sync-files-planning.md)
