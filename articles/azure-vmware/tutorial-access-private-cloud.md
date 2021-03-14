---
title: Oktatóanyag – hozzáférés saját felhőhöz
description: Ismerje meg, hogyan férhet hozzá egy Azure VMware-megoldás saját felhőhöz
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f689a0c706b6427497c80dabb01579ace161d1e2
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462249"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás privát felhőhöz való hozzáférése

Az Azure VMware-megoldás nem teszi lehetővé, hogy a helyszíni vCenter kezelhesse a saját felhőjét. Az Azure VMware Solution vCenter-példányhoz egy Jump Box használatával kell csatlakoznia. 

Ebben az oktatóanyagban létre fog hozni egy Jump Box-t az [előző oktatóanyagban](tutorial-configure-networking.md) létrehozott erőforráscsoporthoz, és be kell jelentkeznie az Azure VMware megoldás vCenter. Ez a Jump Box egy Windows rendszerű virtuális gép (VM) ugyanazon a virtuális hálózaton, amelyet Ön hozott létre.  Hozzáférést biztosít mind a vCenter, mind a NSX-kezelőhöz. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows rendszerű virtuális gép létrehozása az Azure VMware megoldás vCenter való hozzáféréshez
> * Bejelentkezés a vCenter erről a virtuális gépről

## <a name="create-a-new-windows-virtual-machine"></a>Új Windows rendszerű virtuális gép létrehozása

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Kapcsolódás a saját felhő helyi vCenter

1. A Jump (ugrás) mezőben jelentkezzen be a vSphere-ügyfélre a VMware vCenter SSO használatával egy Felhőbeli rendszergazdai felhasználónévvel, és ellenőrizze, hogy a felhasználói felület megjelenik-e sikeresen.

1. A Azure Portal válassza ki saját felhőjét, majd az identitás **kezelése** lehetőséget  >  . 

   A Private Cloud vCenter és a NSX-T Manager kijelző URL-címei és felhasználói hitelesítő adatai.

   >[!TIP]
   >Válassza az **új jelszó létrehozása** lehetőséget új VCENTER és NSX-T jelszavak létrehozásához.

   :::image type="content" source="media/tutorial-access-private-cloud/generate-vcenter-nsxt-passwords.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true" lightbox="media/tutorial-access-private-cloud/generate-vcenter-nsxt-passwords.png":::

1. Navigáljon az előző lépésben létrehozott virtuális GÉPRE, és kapcsolódjon a virtuális géphez. 

   Ha segítségre van szüksége a virtuális GÉPHEZ való csatlakozáshoz, a részletekért lásd: [Csatlakozás virtuális géphez](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

1. A Windows rendszerű virtuális gépen nyisson meg egy böngészőt, és navigáljon a vCenter és a NSX-T jászol URL-címeihez két lapon. 

1. A vCenter lapon adja meg az `cloudadmin@vmcp.local` előző lépésben használt felhasználói hitelesítő adatokat.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Jelentkezzen be a saját Felhőbeli vCenter." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portál." border="true":::

1. A böngésző második lapján jelentkezzen be a NSX-T Managerbe.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="A böngésző második lapján jelentkezzen be a NSX-T Managerbe." border="true":::



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A vCenter való kapcsolódáshoz használandó Windows rendszerű virtuális gép létrehozása
> * Bejelentkezés a vCenter a virtuális gépről

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre virtuális hálózatot a saját felhőalapú fürtök helyi felügyeletének beállításához.

> [!div class="nextstepaction"]
> [Virtual Network létrehozása](tutorial-configure-networking.md)


