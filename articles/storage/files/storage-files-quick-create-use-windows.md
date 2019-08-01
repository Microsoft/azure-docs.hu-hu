---
title: Azure rövid útmutató – Azure Files megosztás létrehozása és használata Windows rendszerű virtuális gépeken | Microsoft Docs
description: Ebben a rövid útmutatóban egy Azure Files-megosztást állít be a Azure Portal, és csatlakoztatva van egy Windows rendszerű virtuális géphez. Kapcsolódjon a fájlmegosztás fájlhoz, töltse fel a fájlokat a megosztásba. Ezután pillanatképet készít a fájlmegosztás fájlról, módosítja a fájlokat a megosztásban, és visszaállítja a fájlmegosztás korábbi pillanatképét.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 626a6a5e0449d267b67653c2f63303226dd0c99d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699751"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Gyors útmutató: Azure Files megosztás létrehozása és kezelése Windows rendszerű virtuális gépekkel

A cikk a Azure Files-megosztás létrehozásának és használatának alapvető lépéseit mutatja be. Ebben a rövid útmutatóban a hangsúly a Azure Files-megosztás gyors beállításán alapul, így a szolgáltatás működése is megtapasztalható. Ha részletesebb útmutatásra van szüksége az Azure-fájlmegosztás saját környezetben történő létrehozásához és használatához, tekintse meg az [Azure-fájlmegosztás használata a Windowsban](storage-how-to-use-files-windows.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben a rövid útmutatóban a következő elemeket állíthatja be:

- Egy Azure Storage-fiók és egy Azure-fájlmegosztás
- Egy Windows Server 2016 Datacenter VM

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az Azure-fájlmegosztás használata előtt létre kell hoznia egy Azure Storage-fiókot. Az általános célú v2-alapú Storage-fiókok hozzáférést biztosítanak az összes Azure Storage-szolgáltatáshoz: Blobok, fájlok, várólisták és táblák. A rövid útmutató egy általános célú v2-es Storage-fiókot hoz létre, de a bármilyen típusú Storage-fiók létrehozásának lépései hasonlóak. A tárfiókok korlátlan számú megosztást tartalmazhatnak, Egy megosztás korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Utána a fájlmegosztás létrehozása következik.

1. Ha az Azure Storage-fiók üzembe helyezése befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.
1. Válassza a **fájlok** elemet a Storage-fiók ablaktáblán.

    ![Fájlok kiválasztása](./media/storage-files-quick-create-use-windows/click-files.png)

1. Válassza a **+ fájlmegosztás**lehetőséget.

    ![Válassza a fájlmegosztás hozzáadása gombot](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nevezze el az új fájlmegosztás *qsfileshare* > adja meg az "1" értéket a kvótához > válassza a **Létrehozás**lehetőséget. A kvóta legfeljebb 5 TiB lehet, de ehhez a rövid útmutatóhoz csak 1 GiB szükséges.
1. Hozzon létre egy új, *qsTestFile* nevű txt-fájlt a helyi gépen.
1. Válassza ki az új fájlmegosztást, majd a fájlmegosztás helyén válassza a **feltöltés**lehetőséget.

    ![Fájl feltöltése](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Tallózással keresse meg azt a helyet, ahová a. txt fájlt létrehozta > válassza a *qsTestFile. txt* > a **feltöltés**lehetőséget.

Eddig létrehozott egy Azure Storage-fiókot és egy fájlmegosztást egyetlen fájllal az Azure-ban. Ezután hozza létre az Azure-beli virtuális gépet a Windows Server 2016 Datacenter használatával, hogy az ebben a rövid útmutatóban a helyszíni kiszolgálót képviseljék.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

1. Ezután bontsa ki a menüt a portál bal oldalán, és válassza ki az **Erőforrás létrehozása** lehetőséget az Azure portál bal felső sarkában.
1. Az **Azure Marketplace**-erőforrások fölött lévő keresési mezőben keressen a **Windows Server 2016 Datacenter** elemre, majd válassza a **Létrehozás** lehetőséget.
1. Az **alapvető beállítások** lap **projekt részletei**területén válassza ki az ehhez a rövid útmutatóhoz létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. A **példány részletei**területen nevezze el a virtuális gép *qsVM*.
1. Hagyja bejelölve az alapértelmezett beállításokat a **Régió**, **Rendelkezésre állási beállítások**, **Kép** és **Méret** mezőkben.
1. A **rendszergazdai fiók**területen adja hozzá a VMadmin **nevet** , és adjon meg egy **jelszót** a virtuális géphez.
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra. Az új virtuális gép létrehozása eltarthat pár percig.

1. Miután a virtuális gép üzembe helyezése befejeződött, válassza **az Ugrás az erőforráshoz**lehetőséget.

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Most pedig csatlakoznunk kell a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. A virtuális gép tulajdonságai lapon válassza a **kapcsolat** lehetőséget.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. A **Csatlakozás virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat, hogy az **IP-cím** használatával csatlakozzon az *3389* portszámon keresztül, és válassza az **RDP-fájl letöltése**lehetőséget.
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **Kapcsolódás** lehetőséget, amikor a rendszer kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\username*- &lt;ként&gt; , ahol a Felhasználónév a virtuális géphez létrehozott virtuális gép rendszergazdai felhasználóneve. Adja meg a virtuális géphez létrehozott jelszót, majd kattintson **az OK gombra**.

   ![További lehetőségek](./media/storage-files-quick-create-use-windows/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás létrehozásához.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Az Azure-fájlmegosztás leképezése Windows-meghajtóra

1. A Azure Portal navigáljon a *qsfileshare* fájlmegosztás, és válassza a **Kapcsolódás**lehetőséget.
1. Másolja a második mező tartalmát, és illessze be a **Jegyzettömbben**.

   ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. A virtuális gépen nyissa meg a fájlkezelőt, és válassza ki **ezt a számítógépet** az ablakban. Ez a kijelölés megváltoztatja a menüszalagon elérhető menüket. A **számítógép** menüben válassza a **hálózati meghajtó csatlakoztatása**lehetőséget.
1. Válassza ki a meghajtó betűjelét, és írja be az UNC-útvonalat. Ha követte az ebben a rövid útmutatóban szereplő elnevezési javaslatokat, másolja  *\\a qsstorageacct. file. Core. Windows. net\qsfileshare fájlt* a **Jegyzettömbből**.

   Győződjön meg arról, hogy mindkét jelölőnégyzet be van jelölve.

   ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Válassza a **Finish** (Befejezés) elemet.
1. A **Windows rendszerbiztonság** párbeszédpanelen:

   - A Jegyzettömbből másolja ki a Storage-fiók nevét a előtagértéke az AZURE-ba, és illessze be a **Windows rendszerbiztonság** párbeszédpanelen a Felhasználónév mezőbe. Ha követte a jelen rövid útmutatóban szereplő elnevezési javaslatokat, másolja a *AZURE\qsstorageacct*.
   - A Jegyzettömbből másolja ki a Storage-fiók kulcsát, és illessze be a **Windows rendszerbiztonság** párbeszédpanelen a jelszót.

      ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Megosztási pillanatkép létrehozása

Most, hogy leképezte a meghajtót, létrehozhat egy pillanatképet.

1. A portálon navigáljon a fájlmegosztás fölé, és válassza a **pillanatkép létrehozása**lehetőséget.

   ![Pillanatkép létrehozása](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. A virtuális gépen nyissa meg a *qstestfile. txt* fájlt, és írja be a következőt: "a fájl módosítva" > mentse és zárjuk be a fájlt.
1. Hozzon létre egy másik pillanatképet.

## <a name="browse-a-share-snapshot"></a>Megosztási pillanatkép tallózása

1. A fájlmegosztás lapon válassza a **Pillanatképek megtekintése**elemet.
1. A **fájlmegosztás** -Pillanatképek panelen válassza ki az első pillanatképet a listában.

   ![Az időbélyegek listájában kiválasztott pillanatkép](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Az adott pillanatkép ablaktábláján válassza a *qsTestFile. txt*elemet.

## <a name="restore-from-a-snapshot"></a>Visszaállítás pillanatképből

1. A fájlmegosztás pillanatképe panelen kattintson a jobb gombbal a *qsTestFile*, és válassza a **visszaállítás** gombot.
1. Válassza az **eredeti fájl felülírása**lehetőséget.

   ![Letöltés és visszaállítás gomb](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. A virtuális gépen nyissa meg a fájlt. A módosítatlan verzió visszaállítása megtörtént.

## <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése

1. A fájlmegosztás lapon válassza a **Pillanatképek megtekintése**elemet.
1. A **fájlmegosztás** -Pillanatképek panelen válassza ki a listában az utolsó pillanatképet, majd kattintson a **Törlés**gombra.

   ![Törlés gomb](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Megosztási pillanatkép használata a Windowsban

A helyszíni VSS-pillanatképekhez hasonlóan a csatlakoztatott Azure-fájlmegosztás pillanatképeit is megtekintheti az előző verziók lapon.

1. A Fájlkezelőben keresse meg a csatlakoztatott megosztást.

   ![Csatlakoztatott megosztás a Fájlkezelőben](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Válassza ki a *qsTestFile. txt fájlt* , és > kattintson a jobb gombbal, és válassza a **Tulajdonságok** lehetőséget a menüből.

   ![Kijelölt könyvtár helyi menüje](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához.

1. A pillanatkép megnyitásához kattintson a **Megnyitás** gombra.

   ![Előző verziók lap](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról

1. Válassza a **visszaállítás**lehetőséget. Ez a művelet a teljes könyvtár tartalmát rekurzív módon másolja át az eredeti helyre a megosztási pillanatkép létrehozásának időpontjában.

   ![Visszaállítás gomb figyelmeztető üzenetben](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás használata Windows rendszeren](storage-how-to-use-files-windows.md)
