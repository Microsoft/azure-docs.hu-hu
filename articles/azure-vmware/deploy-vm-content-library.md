---
title: Tartalom-függvénytár létrehozása virtuális gépek üzembe helyezéséhez az Azure VMware-megoldásban
description: Hozzon létre egy kódtárat egy virtuális gép üzembe helyezéséhez egy Azure VMware-megoldás saját felhőben.
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 3a1b0a091dcdf9a225b4bf91ec83f542c2310285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575811"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Tartalom-függvénytár létrehozása virtuális gépek üzembe helyezéséhez az Azure VMware-megoldásban

A dokumentumtárakban a tartalmakat tár elemek formájában tárolja és kezeli. Egyetlen függvénytár-elem a virtuális gépek (VM-EK) üzembe helyezéséhez használt egy vagy több fájlból áll. 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy tartalom-függvénytárat.  Ezután bemutatjuk egy virtuális gép üzembe helyezését egy ISO-lemezkép használatával a tartalom könyvtárából.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy NSX-T logikai kapcsoló szegmens és egy felügyelt DHCP szolgáltatás szükséges.  További információ: a [DHCP kezelése az Azure VMware megoldásban](manage-dhcp.md) című cikk.

## <a name="create-a-content-library"></a>Tartalom könyvtárának létrehozása

1. A helyszíni vSphere-ügyfélen válassza a **menü > tartalomkezelők**lehetőséget.

   ![Válassza a menü – > a kódtárak lehetőséget.](./media/content-library/vsphere-menu-content-libraries.png)

1. Kattintson a **Hozzáadás** gombra egy új tartalomforrás létrehozásához.

   ![Kattintson a Hozzáadás gombra egy új tartalomforrás létrehozásához.](./media/content-library/create-new-content-library.png)

1. Adjon meg egy nevet, és erősítse meg a vCenter-kiszolgáló IP-címét, majd kattintson a **tovább**gombra.

   ![Adja meg a választott nevet és megjegyzéseket, majd válassza a tovább lehetőséget.](./media/content-library/new-content-library-step1.png)

1. Válassza ki a **helyi tartalom könyvtárat** , és kattintson a **Tovább gombra**.

   ![Ebben a példában egy helyi tartalom-függvénytárat fogunk létrehozni, válassza a tovább lehetőséget.](./media/content-library/new-content-library-step2.png)

1. Válassza ki a tartalmi könyvtárat tároló adattárt, majd kattintson a **tovább**gombra.

   ![Válassza ki a tartalom-függvénytárat tároló adattárt, majd kattintson a Next (tovább) gombra.](./media/content-library/new-content-library-step3.png)

1. Tekintse át és ellenőrizze a tartalom könyvtárának beállításait, majd kattintson a **Befejezés gombra**.

   ![Ellenőrizze a beállításokat, majd kattintson a Befejezés gombra.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>ISO-rendszerkép feltöltése a tartalom-tárba

Most, hogy létrehozta a kódtárat, hozzáadhat egy ISO-rendszerképet, amellyel üzembe helyezheti a virtuális gépet egy privát felhőalapú fürtön. 

1. A vSphere-ügyfélen válassza a **menü > tartalomkezelők**lehetőséget.

1. Kattintson a jobb gombbal az új ISO-hez használni kívánt könyvtárra, és válassza az **Importálás elemet**.

1. Importáljon egy függvénytár-elemet a forráshoz az alábbi módszerek egyikével, majd válassza az **Importálás**lehetőséget:
   1. Válassza ki az URL-címet, és adjon meg egy URL-címet az ISO letöltéséhez.

   1. Válassza ki a helyi rendszerből feltölteni kívánt **helyi fájlt** .

   > [!TIP]
   > Nem kötelező megadni a célhelyhez tartozó egyéni elem nevét és megjegyzéseit.

1. Nyissa meg a könyvtárat, és a **más típusok** lapon ellenőrizze, hogy sikeresen feltöltötte-e az ISO-t.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Virtuális gép üzembe helyezése privát felhőalapú fürtön

1. A vSphere-ügyfélben válassza a **menü > gazdagépek és fürtök**lehetőséget.

1. A bal oldali panelen bontsa ki a fát, és válasszon ki egy fürtöt.

1. Válassza a **műveletek > új virtuális gép**lehetőséget.

1. Lépjen a varázslóba, és módosítsa a kívánt beállításokat.

1. Válassza az **új CD/DVD-meghajtó > ügyféleszközök > a Content Library ISO-fájl**lehetőséget.

1. Válassza ki az előző szakaszban feltöltött ISO-t, majd kattintson az **OK gombra**.

1. Jelölje be a **Csatlakozás** jelölőnégyzetet, hogy az ISO csatlakoztatva legyen a bekapcsolási időben.

1. Válassza az **új hálózati > a legördülő > Tallózás lehetőséget**.

1. Válassza ki a **logikai kapcsolót (szegmens)** , majd kattintson **az OK gombra**.

1. Módosítsa az egyéb hardverbeállítások beállításait, és kattintson a **Tovább gombra**.

1. Ellenőrizze a beállításokat, majd kattintson a **Befejezés gombra**.


## <a name="next-steps"></a>Következő lépések

Ha hibrid felhőalapú bővítményt (HCX) kíván használni a virtuális gépek számítási feladatainak saját felhőbe való áttelepítéséhez, használja az [Azure VMware megoldás telepítése HCX](tutorial-deploy-vmware-hcx.md) .

<!-- LINKS - external-->

<!-- LINKS - internal -->
