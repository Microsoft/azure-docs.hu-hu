---
title: Windows rendszerű virtuális gépeken Azure Files megosztás létrehozása és használata
description: Hozzon létre és használjon Azure Files megosztást a Azure Portal. Csatlakoztassa egy Windows rendszerű virtuális géphez, csatlakozzon a Fájlmegosztáshoz, és töltsön fel egy fájlt a Fájlmegosztásba.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718242"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Rövid útmutató: Virtuálisgép-megosztás Azure Files kezelése Windows rendszerű virtuális gépekkel

A cikk a megosztások létrehozásának és használatának alapvető lépéseit Azure Files be. Ebben a rövid útmutatóban a hangsúly a szolgáltatás Azure Files beállításán van, hogy át tudja tapasztalni a szolgáltatás működését. Ha részletesebb útmutatásra van szüksége az Azure-fájlmegosztások saját környezetben történő létrehozásához és használatára, tekintse meg az Azure-fájlmegosztások Windows rendszeren való [használatát.](storage-how-to-use-files-windows.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben a rövid útmutatóban a következő elemeket állíthatja be:

- Egy Azure Storage-fiók és egy Azure-fájlmegosztás
- Windows Server 2016 Datacenter rendszerű virtuális gép

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Mielőtt használhat egy Azure-fájlmegosztást, létre kell hoznia egy Azure-tárfiókot. Egy általános célú v2-tárfiók hozzáférést biztosít az összes Azure Storage-szolgáltatáshoz, például a blobokhoz, fájlokhoz, üzenetsorokhoz és táblákhoz. A rövid útmutató egy általános célú v2-tárfiókot hoz létre, de a tárfiókok bármely típusának létrehozási lépései hasonlóak. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Utána a fájlmegosztás létrehozása következik.

1. Ha az Azure Storage-fiók üzembe helyezése befejeződött, válassza az **Erőforrás ugrás lehetőséget.**
1. A **tárfiók panelen** válassza a Fájlmegosztások lehetőséget.

    ![Válassza a Fájlmegosztások lehetőséget.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Válassza **a + Fájlmegosztás lehetőséget.**

    ![Új fájlmegosztás létrehozásához válassza a + fájlmegosztás lehetőséget.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Adja az új fájlmegosztásnak a *qsfileshare* nevet,  adja meg az "1" kvótát, hagyja bejelölve a Tranzakció optimalizálva beállítást, majd válassza a Létrehozás **lehetőséget.** A kvóta legfeljebb 5 TiB lehet (100 TiB, engedélyezett nagy fájlmegosztásokkal), de ehhez a rövid útmutatóhoz csak 1 GiB-ra van szükség.
1. Hozzon létre egy *új, qsTestFile nevű txt-fájlt* a helyi gépen.
1. Válassza ki az új fájlmegosztást, majd a fájlmegosztás helyén válassza a Feltöltés **lehetőséget.**

    ![Töltsön fel egy fájlt.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Keresse meg azt a helyet, ahol a .txt fájlt létrehozta> *válassza* aqsTestFile.txt> **feltöltése lehetőséget.**

Eddig létrehozott egy Azure Storage-fiókot és egy fájlmegosztást, benne egy fájllal az Azure-ban. A következő lépés az Azure-beli virtuális gép létrehozása a Windows Server 2016 Datacenterrel, amely a helyszíni kiszolgálót képviseli ebben a rövid útmutatóban.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

1. Ezután bontsa ki a menüt a portál bal oldalán, és válassza ki az **Erőforrás létrehozása** lehetőséget az Azure portál bal felső sarkában.
1. Az erőforrások fölötti keresőmezőben keresse meg **és Azure Marketplace** **a Windows Server 2016 Datacenter et.**
1. Az Alapvető **beállítások lap** Project **details (Projekt részletei) lapján** válassza ki az ehhez a rövid útmutatóhoz létrehozott erőforráscsoportot.

   ![A portál panelen adja meg a virtuális gép alapvető adatait.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. A **Példány részletei területen nevezze** el a virtuális gépet *qsVM néven.*
1. Hagyja bejelölve az alapértelmezett beállításokat a **Régió**, **Rendelkezésre állási beállítások**, **Kép** és **Méret** mezőkben.
1. A **Rendszergazdai fiók területen** adjon meg egy **felhasználónevet,** és adjon meg egy **jelszót** a virtuális géphez.
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget. Az új virtuális gép létrehozása eltarthat pár percig.

1. Miután a virtuális gép üzembe helyezése befejeződött, válassza az **Ugrás az erőforráshoz lehetőséget.**

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Most pedig csatlakoznunk kell a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. A **virtuális gép** tulajdonságok lapján válassza a Csatlakozás lehetőséget.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. A Csatlakozás **virtuális géphez lapon** tartsa meg az alapértelmezett beállításokat az **IP-cím** szerinti csatlakozáshoz a *3389-es* **portszámon** keresztül, és válassza az **RDP-fájl letöltése lehetőséget.**
1. Nyissa meg a letöltött RDP-fájlt, és **amikor** a rendszer kéri, válassza a Csatlakozás lehetőséget.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\username formátumban,* ahol a felhasználónév a virtuális géphez létrehozott virtuálisgép-rendszergazdai &lt; &gt; felhasználónév. Adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK gombra.**

   ![További lehetőségek](./media/storage-files-quick-create-use-windows/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A **kapcsolat létrehozásához** **válassza** az Igen vagy a Folytatás lehetőséget.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Azure-fájlmegosztás leképezása Windows-meghajtóra

1. A Azure Portal nyissa meg a *qsfileshare* fájlmegosztást, és válassza a **Csatlakozás lehetőséget.**
1. Válasszon ki egy meghajtóbetűjelet, majd másolja ki a második mező tartalmát, és illessze be a **Jegyzettömbbe.**

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Képernyőkép a Jegyzettömbbe másolni és beillesztenie kell mező tartalmáról." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. A virtuális gépen nyissa meg a **PowerShellt,** és illessze be a **Jegyzettömb** tartalmát, majd nyomja le az Enter billentyűt a parancs futtatásához. Leképezi a meghajtót.

## <a name="create-a-share-snapshot"></a>Megosztás-pillanatkép létrehozása

Most, hogy leképezte a meghajtót, létrehozhat egy pillanatképet.

1. A portálon lépjen a fájlmegosztáshoz, válassza a **Pillanatképek,** majd a **+ Pillanatkép hozzáadása lehetőséget.**

   ![A műveletek szakaszban válassza a pillanatképek lehetőséget, majd a Pillanatkép hozzáadása lehetőséget.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. A virtuális gépen nyissa meg a *qstestfile.txt* és írja be a "this file has been modified" (Ez a fájl módosítva lett) > Save and close the file (A fájl mentése és bezárása) gombra.
1. Hozzon létre egy másik pillanatképet.

## <a name="browse-a-share-snapshot"></a>Megosztási pillanatkép tallózása

1. A fájlmegosztáson válassza a **Pillanatképek lehetőséget.**
1. A **Pillanatképek panelen** válassza ki a lista első pillanatképét.

   ![Kiválasztott pillanatkép az időbélyegek listájában](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Nyissa meg ezt a pillanatképet, és válassza a *qsTestFile.txt.*

## <a name="restore-from-a-snapshot"></a>Visszaállítás pillanatképből

1. A fájlmegosztási pillanatkép panelen kattintson a jobb gombbal a *qsTestFile* fájlra, és válassza a **Visszaállítás** gombot.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Képernyőkép a pillanatkép panelről, a qstestfile van kiválasztva, a visszaállítás ki van emelve.":::

1. Válassza **az Eredeti fájl felülírása lehetőséget.**

   ![A visszaállítás előugró ablakának képernyőképe, az eredeti fájl felülírása elem kijelölve.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. A virtuális gépen nyissa meg a fájlt. A módosítatlan verzió vissza lett állva.

## <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése

1. A fájlmegosztáson válassza a **Pillanatképek lehetőséget.**
1. A **Pillanatképek panelen** válassza ki a lista utolsó pillanatképét, majd válassza a **Törlés lehetőséget.**

   ![Képernyőkép a pillanatképek panelről, az utolsó kijelölt pillanatképről, a törlés gomb kiemelésével.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Megosztási pillanatkép használata Windows rendszeren

A helyszíni VSS-pillanatképekkel ellentétben a csatlakoztatott Azure-fájlmegosztás pillanatképeit az Előző verziók lapon is megtekintheti.

1. A Fájlkezelő keresse meg a csatlakoztatott megosztást.

   ![Csatlakoztatott megosztás a Fájlkezelő](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Válassza *qsTestFile.txt,* > kattintson a jobb gombbal, és válassza a **menü Tulajdonságok** parancsát.

   ![Kijelölt könyvtár helyi menüje](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához.

1. A **pillanatkép megnyitásához** válassza a Megnyitás lehetőséget.

   ![Előző verziók lap](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról

1. Válassza a **Visszaállítás lehetőséget.** Ez a művelet rekurzívan másolja a teljes könyvtár tartalmát az eredeti helyre a megosztási pillanatkép létrehozásakor.

   ![Visszaállítás gomb a figyelmeztető üzenetben](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Ha a fájl nem változott, akkor a fájl korábbi verziója nem lesz látható, mert a fájl verziója megegyezik a pillanatkép verziójával. Ez konzisztens a Windows-fájlkiszolgálók esetén.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás használata a Windows rendszeren](storage-how-to-use-files-windows.md)
