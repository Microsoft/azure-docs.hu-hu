---
title: Tartalomtár létrehozása virtuális gépek üzembe helyezéséhez a Azure VMware Solution
description: Hozzon létre egy tartalomtárat egy virtuális gép magánfelhőben Azure VMware Solution üzembe helyezéséhez.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b27d2682d8799bec6b09a08e5063359113b20a88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873882"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Tartalomtár létrehozása virtuális gépek üzembe helyezéséhez a Azure VMware Solution

A tartalomtár könyvtárelemek formájában tárolja és kezeli a tartalmakat. Egy könyvtárelem egy vagy több fájlból áll, amelyek a virtuális gépek üzembe helyezéséhez használhatók. 

Ebben a cikkben a tartalomtár létrehozásának folyamatán fogunk végigmenni.  Ezután végigveszünk egy virtuális gép üzembe helyezését a tartalomtárból származó ISO-rendszerkép használatával.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez NSX-T szegmensre (logikai kapcsoló) és felügyelt DHCP-szolgáltatásra van szükség.  További információért tekintse meg [a DHCP](manage-dhcp.md) kezelése a Azure VMware Solution cikkben.

## <a name="create-a-content-library"></a>Tartalomtár létrehozása

1. A helyszíni vSphere-ügyfélen válassza a Menü > **tartalomtárak lehetőséget.**

   ![Válassza a Menü -> tartalomtárak lehetőséget](./media/content-library/vsphere-menu-content-libraries.png)

1. Új **tartalomtár létrehozásához** kattintson a Hozzáadás gombra.

   ![Új tartalomtár létrehozásához kattintson a Hozzáadás gombra.](./media/content-library/create-new-content-library.png)

1. Adjon meg egy nevet, erősítse meg a vCenter-kiszolgáló IP-címét, majd válassza a **Tovább lehetőséget.**

   ![Adja meg a választott nevet és megjegyzéseket, majd válassza a Tovább lehetőséget.](./media/content-library/new-content-library-step1.png)

1. Válassza ki **a Helyi tartalomtárat, majd** kattintson a Tovább **gombra.**

   ![Ebben a példában egy helyi tartalomtárat hozunk létre, és kiválasztjuk a Tovább lehetőséget.](./media/content-library/new-content-library-step2.png)

1. Válassza ki a tartalomtárat tároló adattárat, majd kattintson a Tovább **gombra.**

   ![Válassza ki azt az adattárat, amely a tartalomtárat szeretné tartalmazni, majd kattintson a Tovább gombra.](./media/content-library/new-content-library-step3.png)

1. Tekintse át és ellenőrizze a tartalomtár beállításait, majd válassza a Befejezés **lehetőséget.**

   ![Ellenőrizze a beállításokat, majd válassza a Befejezés lehetőséget.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>ISO-lemezkép feltöltése a tartalomtárba

Most, hogy létrehozta a tartalomtárat, hozzáadhat egy ISO-lemezképet a virtuális gép magánfelhő-fürtön való üzembe helyezéséhez. 

1. A vSphere-ügyfélen válassza a Menü > **tartalomtárak lehetőséget.**

1. Kattintson a jobb gombbal az új ISO-lemezképhez használni kívánt tartalomtárra, majd válassza az **Elem importálása lehetőséget.**

1. Importálja a Forrás könyvtárelemét a következők egyikével, majd válassza az Importálás **lehetőséget:**
   1. Válassza az URL lehetőséget, és adjon meg egy URL-címet az ISO letöltéséhez.

   1. Válassza **a Helyi fájl** lehetőséget a helyi rendszerből való feltöltéshez.

   > [!TIP]
   > Nem kötelező, egyéni elemnevet és megjegyzéseket is definiálhat a Célhoz.

1. Nyissa meg a kódtárat, és válassza az **Egyéb típusok** lapot az ISO-fájl sikeres feltöltésének ellenőrzéséhez.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Virtuális gép üzembe helyezése magánfelhő-fürtön

1. A vSphere-ügyfélben válassza a **Menü > gazdagépek és fürtök lehetőséget.**

1. A bal oldali panelen bontsa ki a fát, és válasszon ki egy fürtöt.

1. Válassza **az Actions > New Virtual Machine (Új virtuális gép létrehozása) lehetőséget.**

1. A varázsló lépéseit végigveszve módosíthatja a kívánt beállításokat.

1. Válassza **az Új CD-/DVD> eszköz és > ISO-fájl lehetőséget.**

1. Válassza ki az előző szakaszban feltöltött ISO-fájlt, majd kattintson az **OK gombra.**

1. Jelölje be **a Csatlakozás** jelölőnégyzetet, hogy az ISO bekapcsolódáskor csatlakozzon.

1. Válassza **az Új hálózat > a Tallózás > legördülő menüt.**

1. Válassza ki **a logikai kapcsolót (szegmenst), majd** kattintson az OK **gombra.**

1. Módosítsa a többi hardverbeállítást, és válassza a **Tovább lehetőséget.**

1. Ellenőrizze a beállításokat, majd válassza a **Befejezés lehetőséget.**


## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan lehet tartalomtárat létrehozni a virtuális gépek Azure VMware Solution üzembe helyezéséhez, a következővel ismerkedhet meg:

- [Virtuális gépek számítási feladatainak áttelepítése magánfelhőbe](tutorial-deploy-vmware-hcx.md)
- [A virtuális gépek életciklus Azure VMware Solution kezelése](lifecycle-management-of-azure-vmware-solution-vms.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
