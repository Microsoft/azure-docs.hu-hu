---
title: Oktatóanyag – Windows-fájlkiszolgálók kiterjesztése Azure File Sync | Microsoft Docs
description: Ismerje meg, hogyan bővítheti ki a Windows-fájlkiszolgálókat Azure File Sync a verziókkal, az elsőtől a végéig.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c71a0ddece95eb7d6a651da9c307912a1a24c800
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797491"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával

Ez a cikk bemutatja a Windows-kiszolgálók tárolási kapacitásának kibővítését a virtuális Azure File Sync. Bár az oktatóanyag a Windows Servert Azure-beli virtuális gépként (VM) is tartalmaz, ezt a folyamatot általában a helyszíni kiszolgálókon kell megtennie. A saját környezetében való üzembe helyezéshez Azure File Sync útmutatót a Deploy Azure File Sync (Üzembe helyezés a saját [Azure File Sync)](file-sync-deployment-guide.md) Azure File Sync találhat.

> [!div class="checklist"]
> * A Társzinkronizálási szolgáltatás üzembe helyezése
> * A Windows Server előkészítése az Azure File Sync használatára
> * Az Azure File Sync-ügynök telepítése
> * A Windows Server regisztrálása a Társzinkronizálási szolgáltatásban
> * Szinkronizálási csoport és felhő végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben az oktatóanyagban a következőket kell megtennie, mielőtt üzembe Azure File Sync:

- Azure Storage-fiók és fájlmegosztás létrehozása
- Windows Server 2016 Datacenter rendszerű virtuális gép beállítása
- A Windows Server rendszerű virtuális gép előkészítése Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Mappa és .txt fájl létrehozása

A helyi számítógépen hozzon létre egy új, _FilesToSync_ nevű mappát, és adjon hozzá egy _mytestdoc.txt_ nevű szöveges fájlt. Ezt a fájlt az oktatóanyag későbbi részét fogja feltölteni a fájlmegosztásba.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Miután üzembe helyezett egy Azure Storage-fiókot, létre kell hoznia egy fájlmegosztást.

1. A Azure Portal válassza az **Erőforráshoz ugrás lehetőséget.**
1. A **tárfiók** panelen válassza a Fájlok lehetőséget.

    ![Fájlok kiválasztása](./media/storage-sync-files-extend-servers/click-files.png)

1. Válassza **a + Fájlmegosztás lehetőséget.**

    ![Válassza a Fájlmegosztás hozzáadása gombot](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az új fájlmegosztást _afsfileshare néven._ Adja meg az "1" **kvótát,** majd válassza a **Létrehozás lehetőséget.** A kvóta legfeljebb 5 TiB lehet, de ebben az oktatóanyagban csak 1 GB-ra van szükségünk.

    ![Adjon meg nevet és kvótát az új fájlmegosztás számára](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Válassza ki az új fájlmegosztást. A fájlmegosztás helyén válassza a Feltöltés **lehetőséget.**

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Keresse meg azt a _FilesToSync_ mappát, ahol a .txt fájlt létrehozta, válassza a fájl _mytestdoc.txt_ **feltöltése lehetőséget.**

    ![Fájlmegosztás tallózása](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton létrehozott egy tárfiókot és egy fájlmegosztást, benne egy fájllal. Ezután üzembe helyez egy Azure-beli virtuális gépet a Windows Server 2016 Datacenterrel, amely a helyszíni kiszolgálót képviseli ebben az oktatóanyagban.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és adatlemez csatolása

1. A bal oldali menü Azure Portal és bontsa ki a menüt. Válassza **az Erőforrás létrehozása lehetőséget a** bal felső sarokban.
1. Az erőforrások listája fölötti **keresőmezőben** keressen rá Azure Marketplace **Windows Server 2016 Datacenter** kifejezésre, és válassza ki az eredmények között. Válassza a **Létrehozás lehetőséget.**
1. Ugrás az **Alapvető beállítások lapra.** A **Projekt részletei alatt** válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Adja meg a virtuális gép alapvető adatait a portál panelen](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **Példány részletei területen** adja meg a virtuális gép nevét. Használja például a _myVM-et._
1. Ne módosítsa a **Régió,** a Rendelkezésre állási **beállítások,** a **Rendszerkép** és a Méret alapértelmezett **beállításait.**
1. A **Rendszergazdai fiók** területen adjon meg egy **Felhasználónevet** és egy **Jelszót** a virtuális géphez.
1. A **Bejövőport-szabályok** alatt válassza a **Kijelölt** portok engedélyezése lehetőséget, majd a legördülő menüből válassza az **RDP (3389)** és **a HTTP** lehetőséget.

1. A virtuális gép létrehozása előtt el kell készíteni az adatlemezt.

   1. Válassza **a Tovább:Lemezek lehetőséget.**

      ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. A **Lemezek lap** Lemezbeállítások **lapján** hagyja meg az alapértelmezett értékeket.
   1. Az **ADATLEMEZEK alatt** válassza az Új lemez létrehozása **és csatolása lehetőséget.**

   1. Használja az alapértelmezett beállításokat a **Méret (GiB) kivételével,** amelyet ebben az oktatóanyagban **1 GB-ra** módosíthat.

      ![Adatlemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Válassza az **OK** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   Az Értesítések ikon **kiválasztásával** figyelheti az üzembe **helyezési folyamat előrehaladását.** Egy új virtuális gép létrehozása eltarthat néhány percig.

1. A virtuális gép üzembe helyezésének befejezése után válassza az **Erőforráshoz való ugrás lehetőséget.**

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Ezután csatlakozzon a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. A Azure Portal **válassza** a Csatlakozás lehetőséget a virtuális gép tulajdonságainak lapján.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. A Csatlakozás **virtuális géphez lapon** tartsa meg az alapértelmezett beállításokat az **IP-cím** szerinti csatlakozáshoz a 3389-es porton keresztül. Válassza az **RDP-fájl letöltése** lehetőséget.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és **amikor** a rendszer kéri, válassza a Csatlakozás lehetőséget.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\username formátumban,* adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK gombra.**

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A **kapcsolat létrehozásához** **válassza** az Igen vagy a Folytatás lehetőséget.

### <a name="prepare-the-windows-server"></a>A Windows-kiszolgáló előkészítése

A Windows Server 2016 Datacenter kiszolgálóra tiltsa le az Internet Explorer fokozott biztonsági beállításokat. Ez a lépés csak a kiszolgáló kezdeti regisztrációjakor szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

A Windows Server 2016 Datacenter virtuális gépen a Kiszolgálókezelő automatikusan megnyílik.  Ha a Kiszolgálókezelő nem nyílik meg alapértelmezés szerint, keresse meg a Start menüben.

1. A **Kiszolgálókezelőben** válassza a **Helyi kiszolgáló lehetőséget.**

   ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. A Tulajdonságok **panelen** válassza az **IE fokozott biztonsági beállításai hivatkozást.**  

    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az **Internet Explorer fokozott biztonsági beállítások** párbeszédpanelen válassza a **Kikapcsolva** lehetőséget a **Rendszergazdák** és a **Felhasználók** elemekre.

    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Most már az adatlemezt is hozzáadhatja a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. Miközben továbbra is a **Windows Server 2016 Datacenter** virtuális gépen van, válassza a **Fájlok és** tárolási szolgáltatások  >  **Kötetlemezek**  >  **lehetőséget.**

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal az Msft Virtual Disk nevű 1 GB-os **lemezre, és** válassza az **Új kötet lehetőséget.**
1. Végezze el a varázsló lépéseit. Használja az alapértelmezett beállításokat, és jegyezze fel a hozzárendelt meghajtóbetűjelet.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza a **Bezárás** gombot.

   Ezen a ponton a lemez online állapotba került és létrehozott rajta egy kötetet. Nyissa Fájlkezelő a windowsos virtuális gépen a közelmúltban hozzáadott adatlemez jelenlétének megerősítéséhez.

1. A Fájlkezelő területen bontsa ki az **Ez a számítógép** gombra, és nyissa meg az új meghajtót. Ebben a példában az F: meghajtóról van szó.
1. Kattintson a jobb gombbal, és válassza az **Új**  >  **mappa lehetőséget.** A mappa neve legyen _FilesToSync_.
1. Nyissa meg **a FilesToSync** mappát.
1. Kattintson a jobb gombbal, és válassza **az Új szöveges** dokumentum  >  **lehetőséget.** A szövegfájl neve legyen _MyTestFile_.

    ![Új szöveges fájl hozzáadása](media/storage-sync-files-extend-servers/new-file.png)

1. Zárja **be Fájlkezelő** **kiszolgálókezelőt.**

### <a name="download-the-azure-powershell-module&quot;></a>Az Azure PowerShell letöltése

Ezután a Windows Server 2016 Datacenter virtuális gépen telepítse a Azure PowerShell modult a kiszolgálóra.

1. A virtuális gépen nyisson meg egy emelt szintű PowerShell-ablakot.
1. Futtassa az alábbi parancsot:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Ha a NuGet 2.8.5.201-esnél régebbi verziója van, a rendszer felszólítja, hogy töltse le és telepítse a NuGet legújabb verzióját.

   Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. A PSGallery első használatakor a következő üzenet látható:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;N"):
   ```

1. A **telepítés folytatásához** **válasza** az Igen vagy Az összes válasz.

Az `Az` modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Ezen a ponton beállította a környezetet az oktatóanyaghoz. Készen áll a Társzinkronizálási szolgáltatás üzembe helyezésére.

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

A Azure File Sync üzembe helyezéséhez először helyezzen el egy **szinkronizálási** szolgáltatás erőforrást a kiválasztott előfizetés erőforráscsoportja számára. A storage sync service örökli a hozzáférési engedélyeket az előfizetéséből és az erőforráscsoportból.

1. A Azure Portal válassza az **Erőforrás létrehozása** lehetőséget, majd keressen rá a **Azure File Sync.**
1. Válassza ki az **Azure File Sync** elemet a keresési eredmények közül.
1. Válassza ki **Létrehozás** elemet a **Társzinkronizálás üzembe helyezése** lap megnyitásához.

   ![Társzinkronizálás üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Név** | A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).<br><br>Ehhez az oktatóanyaghoz használja az _afssyncservice02_ szolgáltatást. |
   | **Előfizetés** | Az oktatóanyaghoz használt Azure-előfizetés. |
   | **Erőforráscsoport** | A Társzinkronizálási szolgáltatást tartalmazó erőforráscsoport.<br><br>Ehhez _az oktatóanyaghoz használja az afsresgroup101918-at._ |
   | **Hely** | USA keleti régiója |

1. Ha elkészült, válassza ki a **Létrehozás** elemet a **Társzinkronizálási szolgáltatás** üzembe helyezéséhez.
1. Válassza az **Értesítések lapot,** > **erőforrás megnyitása lehetőséget.**

## <a name="install-the-agent"></a>Az ügynök telepítése

Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással.

1. A **Windows Server 2016 Datacenter virtuális gépen** nyissa meg a **Internet Explorer.**
1. Nyissa meg a [Microsoft Download Centert](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le a **Azure File Sync Agent (Ügynök) szakaszhoz, és** válassza a Download **(Letöltés) lehetőséget.**

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a jelölőnégyzetet a **StorageSyncAgent_V3_WS2016.EXE** majd kattintson a **Tovább gombra.**

   ![Ügynök kiválasztása](media/storage-sync-files-extend-servers/select-agent.png)

1. Válassza az Allow once Run Open **(Futtatás után**  >    >  **megnyitás) lehetőséget.**
1. Ha még nem tette meg, zárja be a PowerShell-ablakot.
1. Fogadja el az alapértelmezett beállításokat a **Társzinkronizálási ügynök telepítő varázslóban**.
1. Válassza a **Telepítés** gombot.
1. Válassza a **Befejezés** gombot.

Sikeresen üzembe helyezte az Azure Sync szolgáltatást és telepítette az ügynököt a Windows Server 2016 Datacenter virtuális gépre. Most regisztrálnia kell a virtuális gépet a Társzinkronizálási szolgáltatásra.

## <a name="register-windows-server"></a>A Windows Server regisztrálása

Ha regisztrálja a Windows-kiszolgálót egy Társzinkronizálási szolgáltatásban, megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a tárolószinkronizálási szolgáltatás között. Egy kiszolgáló csak egy Szinkronizálási szolgáltatásban regisztrálható. Képes szinkronizálni más kiszolgálókkal és Azure-fájlmegosztásokkal, amelyek ehhez a szinkronizálási szolgáltatáshoz vannak társítva.

A kiszolgálóregisztrációs ügynök telepítése után a kiszolgálóregisztrációs felhasználói felületnek automatikusan meg kell Azure File Sync meg. Ha nem, akkor manuálisan is megnyithatja a fájl helyéről: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Amikor megnyílik a Kiszolgálóregisztráció felhasználói felülete a virtuális gépen, kattintson az **OK gombra.**
1. A **kezdéshez válassza** a Bejelentkezés lehetőséget.
1. Jelentkezzen be az Azure-fiók hitelesítő adataival, és válassza **a Bejelentkezés lehetőséget.**
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | Érték | Leírás |
   | ----- | ----- |
   | **Azure-előfizetés** | Az előfizetés, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | A Társzinkronizálási szolgáltatást tartalmazó erőforráscsoport. Ehhez _az oktatóanyaghoz használja az afsresgroup101918-at._ |
   | **Társzinkronizálási szolgáltatás** | A Társzinkronizálási szolgáltatás neve. Ehhez az oktatóanyaghoz használja az _afssyncservice02-t._ |

1. A **kiszolgáló regisztrációjának** befejezéséhez válassza a Regisztráció lehetőséget.
1. A regisztrációs folyamat részeként a rendszer további bejelentkezést kér. Jelentkezzen be, és válassza a **Tovább lehetőséget.**
1. Válassza az **OK** lehetőséget.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása

A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoportnak egy felhőbeli végpontot kell tartalmaznia, amely egy Azure-fájlmegosztást képvisel. A szinkronizálási csoportnak egy vagy több kiszolgálóvégpontot is tartalmaznia kell. A kiszolgálói végpont egy elérési utat jelöl egy regisztrált kiszolgálón. Szinkronizálási csoport létrehozása:

1. A [tárfiókban Azure Portal](https://portal.azure.com/) **a Társzinkronizálási** szolgáltatásban válassza a + Szinkronizálás csoport lehetőséget. Ehhez az oktatóanyaghoz használja az *afssyncservice02* szolgáltatást.

   ![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Adja meg a következő adatokat egy szinkronizálási csoport felhővégponttal való létrehozásához:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név. Ebben *az oktatóanyagban használja az afssyncgroup* csoportot.|
   | **Előfizetés** | Az előfizetés, ahova a Társzinkronizálási szolgáltatást telepítette ehhez az oktatóanyaghoz. |
   | **Storage-fiók** | Válassza **a Tárfiók kiválasztása lehetőséget.** A megjelenő panelen válassza ki a létrehozott Azure-fájlmegosztást tartalmazó tárfiókot. Ehhez az oktatóanyaghoz használja az *afsstoracct101918-at.* |
   | **Azure-fájlmegosztás** | A létrehozott Azure-fájlmegosztás neve. Ehhez az oktatóanyaghoz használja az *afsfileshare* nevűt. |

1. Válassza a **Létrehozás** lehetőséget.

Ha kiválasztja a szinkronizálási csoportot, akkor láthatja, hogy most már rendelkezik egy **felhőbeli végponttal**.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele

A kiszolgálóvégpont egy adott helyet képvisel egy regisztrált kiszolgálón. Például egy mappa egy kiszolgálóköteten. Kiszolgálóvégpont hozzáadása:

1. Válassza ki az újonnan létrehozott szinkronizálási csoportot, majd válassza a **Kiszolgálói végpont hozzáadása lehetőséget.**

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. A **Kiszolgálói végpont hozzáadása panelen** adja meg a következő adatokat a kiszolgálóvégpont létrehozásához:

   | Érték | Leírás |
   | ----- | ----- |
   | **Regisztrált kiszolgáló** | A létrehozott kiszolgáló neve. Ehhez az oktatóanyaghoz használja az *afsvm101918-at.* |
   | **Elérési út** | A létrehozott meghajtó Windows Server-útvonala. Ebben *az oktatóanyagban használja az f:\filestosync* parancsot. |
   | **Felhőbeli rétegzés** | Hagyja letiltott állapotban az oktatóanyaghoz. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyjuk. |

1. Válassza a **Létrehozás** lehetőséget.

A fájlok az Azure-fájlmegosztások és a Windows Server között most már szinkronizálva vannak.

![Az Azure Storage szinkronizálása sikeresen megtörtént](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné az oktatóanyagban létrehozott erőforrásokat, először távolítsa el a végpontokat a storage sync szolgáltatásból. Ezután törölje a kiszolgáló regisztrációját a társzinkronizálási szolgáltatással, távolítsa el a szinkronizálási csoportokat, és törölje a szinkronizálási szolgáltatást.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Windows-kiszolgáló tárolási kapacitásának kibővítését ismertető alapvető lépéseket sajátíthatja el a Azure File Sync. Az üzembe helyezés megtervezésének alaposabb Azure File Sync lásd:

> [!div class="nextstepaction"]
> [Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
