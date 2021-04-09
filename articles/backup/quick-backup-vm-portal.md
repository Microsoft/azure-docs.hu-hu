---
title: Rövid útmutató – virtuális gép biztonsági mentése a Azure Portal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Recovery Services-tárolót, hogyan engedélyezheti a védelmet egy Azure-beli virtuális gépen, és hogyan készíthet biztonsági másolatot a virtuális gépről a Azure Portal.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/12/2020
ms.custom: mvc
ms.openlocfilehash: cd0d4928d5cd0eb15f4f1372a0a3417fc85d13bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328775"
---
# <a name="back-up-a-virtual-machine-in-azure"></a>Virtuális gép biztonsági mentése az Azure-ban

Az Azure biztonsági másolatok létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Azure biztonsági másolatok, valamint az összes kapcsolódó erőforrás létrehozásához és konfigurálásához. Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolható helyreállítási pontokat hoz létre. Ez a cikk a virtuális gépek (VM-ek) az Azure Portal használatával való biztonsági mentését mutatja be részletesen.

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure Portal használatával](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="select-a-vm-to-back-up"></a>A virtuális gép kiválasztása a biztonsági mentéshez

Hozzon létre egy egyszerű ütemezett napi biztonsági mentést egy Recovery Services-tárolóba.

1. A bal oldali menüben válassza a **Virtuális gépek** elemet.
2. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni. Ha a virtuális gépet a gyors üzembe helyezés példaparancsai használatával hozta létre, a virtuális gép *myVM* néven szerepel a *myResourceGroup* erőforráscsoportban.
3. A **Műveletek** szakaszban válassza a **Biztonsági mentés** lehetőséget. Megnyílik a **Biztonsági mentés engedélyezése** ablak.

## <a name="enable-backup-on-a-vm"></a>Biztonsági mentés engedélyezése egy virtuális gépen

A Recovery Services-tároló egy logikai tároló, amely az egyes védett erőforrások, például az Azure-beli virtuális gépek biztonsági másolatainak adatait tárolja. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

1. Válassza ki az **Új létrehozása** elemet, és adja meg az új tároló nevét – például *myRecoveryServicesVault*.
2. Ha még nem lenne kiválasztva, válassza a **Meglévő használata** beállítást, majd válassza ki a virtuális gép erőforráscsoportját a legördülő menüből.

    ![Virtuális gép biztonsági mentésének engedélyezése az Azure Portalon](./media/quick-backup-vm-portal/enable-backup.png)

    A tároló alapértelmezés szerint georedundáns tárolásra van beállítva. Az adatok további védelme érdekében ez a tárolási redundancia biztosítja, hogy a biztonsági mentési adatok egy olyan másodlagos Azure-régióba replikálódnak, amely több száz kilométerre van az elsődleges régiótól.

    Házirendek létrehozásával és használatával határozhatja meg, hogy a rendszer mikor futtassa a biztonsági mentési feladatokat, és meddig tárolja a helyreállítási pontokat. Az alapértelmezett védelmi házirend naponta egyszer futtat biztonsági mentési feladatot, és 30 napig őrzi meg a helyreállítási pontokat. Ezekkel az alapértelmezett értékekkel gyorsan biztosíthatja virtuális gépe védelmét.

3. Az alapértelmezett biztonsági mentés házirend értékeinek elfogadásához válassza a **Biztonsági mentés engedélyezése** lehetőséget.

A Recovery Services-tároló létrehozása igénybe vehet pár másodpercet.

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A biztonsági mentést most is elindíthatja, ahelyett, hogy megvárná, amíg az alapértelmezett házirend az ütemezett időben futtatja a feladatot. Ez az első biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot. Az ezt a kezdeti biztonsági mentést követő további biztonsági mentési feladatok növekményes helyreállítási pontokat hoznak létre. A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

1. A virtuális gép **Biztonsági mentés** ablakában válassza a **Biztonsági mentés** gombot.

    ![Virtuális gép azonnali biztonsági mentésének végrehajtása az Azure Portalon](./media/quick-backup-vm-portal/backup-now.png)

2. A 30 napos biztonságimásolat-megőrzési házirend elfogadásához hagyja változatlanul az alapértelmezett dátumot a **Biztonsági másolat megőrzése eddig:** területen. A feladat indításához válassza a **Biztonsági mentés** elemet.

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A virtuális gép **Biztonsági mentés** ablakában a biztonsági mentés állapota és az elkészült helyreállítási pontok száma látható. Amint a virtuális gép biztonsági mentési feladata befejeződött, megjelennek a **Legutóbbi biztonsági mentés időpontja**, a **Legutóbbi visszaállítási pont** és a **Legrégebbi visszaállítási pont** adatai az **Áttekintés** ablak jobb oldalán.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Ha többé már nincs szükség rá, a virtuális gép védelmét bármikor leállíthatja, eltávolíthatja a helyreállítási pontokat és a Recovery Services-tárolót, majd törölheti az erőforráscsoportot és a társított virtuális gép erőforrásait.

Ha folytatja a biztonsági mentési oktatóanyagot, amely leírja, hogyan lehet visszaállítani a virtuális gép adatait, ugorja át a szakasz lépéseit, és folytassa a [következő lépésekkel](#next-steps).

1. Válassza ki a virtuális gép **Biztonsági mentés** beállítását.

2. Válassza a **biztonsági mentés leállítása** lehetőséget.

    ![Virtuális gép biztonsági mentésének leállítása az Azure Portalon](./media/quick-backup-vm-portal/stop-backup.png)

3. Válassza az **Biztonsági másolat adatainak törlése** lehetőséget a legördülő menüből.

4. A **Biztonságimásolat-elem nevének megadása** párbeszédpanelen adja meg a virtuális gép nevét – például *myVM*. Válassza a **biztonsági mentés leállítása** lehetőséget.

    Miután a virtuális gép biztonsági mentése leállt, és a helyreállítási pontok is ellettek távolítva, törölheti az erőforráscsoportot. Ha egy meglévő virtuális gépet használt, megtarthatja az erőforráscsoportot és a virtuális gépet.

5. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget.
6. Válassza ki az erőforráscsoportot a listáról. Ha a virtuális gépet a gyors üzembe helyezés példaparancsai használatával hozta létre, az erőforráscsoport neve *myResourceGroup*.
7. Válassza az **Erőforráscsoport törlése** elemet. A megerősítéshez adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

    ![Az erőforráscsoport törlése az Azure Portal használatával](./media/quick-backup-vm-portal/delete-resource-group-from-portal.png)

## <a name="next-steps"></a>Következő lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot. Ha bővebb információra van szüksége az Azure Backup és a Recovery Services szolgáltatásokkal kapcsolatban, lépjen tovább a következő oktatóanyagokra.

> [!div class="nextstepaction"]
> [Biztonsági mentés készítése több Azure-beli virtuális gépről](./tutorial-backup-vm-at-scale.md)
