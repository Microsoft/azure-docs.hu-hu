---
title: 'Oktatóanyag: Azure Bastion-gazdagép létrehozása: Windows virtuális gép: portál'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet, és hogyan csatlakozhat egy Windows rendszerű virtuális géphez.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 3b365e347802824e855797afb8c68e5249bf0adb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579618"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Oktatóanyag: a Bastion konfigurálása és a Windows rendszerű virtuális gépekhez való kapcsolódás böngésző használatával

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy virtuális géphez a böngészőben az Azure Bastion és a Azure Portal használatával. A Azure Portal a virtuális hálózatra helyezi a Bastion-t. A megerősített telepítés után a magánhálózati IP-címén keresztül csatlakozik egy virtuális géphez a Azure Portal használatával. A virtuális gépe nem igényel nyilvános IP-címet vagy speciális szoftvert. A szolgáltatás üzembe helyezését követően az RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. További információ az Azure Bastion-ről: [Mi az az Azure Bastion?](bastion-overview.md).

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Megerősített gazdagép létrehozása a VNet
> * Csatlakozás Windows rendszerű virtuális géphez

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Egy virtuális hálózat.
* Egy Windows rendszerű virtuális gép a virtuális hálózaton.
* A következő szükséges szerepkörök:
  * Olvasó szerepkör a virtuális gépen.
  * Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.
  * Olvasó szerepkör az Azure-beli megerősített erőforráson.

* Portok: a Windows rendszerű virtuális géphez való csatlakozáshoz a következő portokat kell megnyitnia a Windows rendszerű virtuális gépen:
  * Bejövő portok: RDP (3389)

 >[!NOTE]
 >Az Azure Bastion és az Azure saját DNS zónák használata jelenleg nem támogatott. Mielőtt elkezdené, győződjön meg arról, hogy a megerősített erőforrás üzembe helyezését megtervező virtuális hálózat nem kapcsolódik privát DNS-zónához.
 >

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt a megerősített objektum létrehozásához a VNet. Erre azért van szükség, hogy biztonságos kapcsolatokat hozzon létre egy virtuális géppel a VNet.

1. A **Kezdőlap** lapon válassza az **+ erőforrás létrehozása** lehetőséget.
1. Az **új** oldalon, a keresőmezőbe írja be a következőt: **Bastion**, majd válassza az **ENTER billentyűt** a keresési eredmények eléréséhez. A **megerősített** eredménynél ellenőrizze, hogy a közzétevő a Microsoft.
1. Válassza a **Létrehozás** lehetőséget.
1. A **bástya létrehozása** lapon állítson be egy új megerősített erőforrást.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Bastion-gazdagép létrehozása" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Előfizetés**: az új megerősített erőforrás létrehozásához használni kívánt Azure-előfizetés.
    * **Erőforráscsoport**: az az Azure-erőforráscsoport, amelyben a rendszer létrehozza az új megerősített erőforrást. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat.
    * **Name (név**): az új megerősített erőforrás neve.
    * **Régió**: az Azure nyilvános régiója, amelyhez az erőforrást létre kívánja hozni.
    * **Virtual Network (virtuális hálózat**): az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva. A folyamat során létrehozhat egy új virtuális hálózatot a portálon, vagy használhat egy meglévő virtuális hálózatot is. Ha meglévő virtuális hálózatot használ, ellenőrizze, hogy a meglévő virtuális hálózat rendelkezik-e elegendő szabad hellyel a megerősített alhálózat követelményeinek kielégítéséhez. Ha nem látja a virtuális hálózatot a legördülő listából, győződjön meg arról, hogy a megfelelő erőforráscsoportot választotta.
    * **Alhálózat**: Miután létrehozta vagy kiválasztta a virtuális hálózatot, megjelenik az alhálózat mező. A virtuális hálózat azon alhálózata, ahol az új megerősített állomás üzembe lesz helyezve. Az alhálózat a megerősített gazdagépre lesz kijelölve. Válassza az **alhálózat konfigurációjának kezelése** lehetőséget, és hozza létre az Azure megerősített alhálózatot. Válassza az **+ alhálózat** lehetőséget, és hozzon létre egy alhálózatot az alábbi irányelvek alapján:

         * Az alhálózatnak **AzureBastionSubnet** nevűnek kell lennie.
         * Az alhálózatnak legalább/27 vagy nagyobb méretűnek kell lennie.

      Nem kell további mezőket kitöltenie. Kattintson az **OK gombra** , majd az oldal tetején válassza ki a **bástya létrehozása** lehetőséget a megerősített konfiguráció laphoz való visszatéréshez.
    * **Nyilvános IP-cím**: a megerősített erőforrás nyilvános IP-címe, AMELYEN az RDP/SSH elérhető (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak. Ehhez az IP-címhez nem tartozik semmi ahhoz a virtuális géphez, amelyhez csatlakozni szeretne. Ez a megerősített gazdagép erőforrásának nyilvános IP-címe.
    * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve. Ebben az oktatóanyagban meghagyhatja az alapértelmezett értéket.
    * **Nyilvános IP-cím SKU**: Ez a beállítás alapértelmezés szerint a **standard** értékre van beállítva. Az Azure Bastion csak a standard nyilvános IP-SKU-t használja/támogatja.
    * **Hozzárendelés**: Ez a beállítás alapértelmezés szerint a **statikus** értékre van feltöltve.

1. Ha befejezte a beállítások megadását, válassza a **felülvizsgálat + létrehozás** lehetőséget. Ezzel érvényesíti az értékeket. Az ellenőrzés után létrehozhatja a megerősített erőforrást.
1. Válassza a **Létrehozás** lehetőséget.
1. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. A megerősített erőforrás létrehozása és üzembe helyezése körülbelül 5 percet vesz igénybe.

## <a name="connect-to-a-vm"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforrásokat a következő lépésekkel:

1. Adja meg az erőforráscsoport nevét a portál felső részén található **keresőmezőbe** . Ha az erőforráscsoport megjelenik a keresési eredmények között, válassza ki.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét az erőforráscsoport neveként **, majd válassza** a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy megerősített gazdagépet, és hozzárendelte azt egy virtuális hálózathoz, majd kapcsolódott egy Windows rendszerű virtuális géphez. Dönthet úgy, hogy hálózati biztonsági csoportokat használ az Azure-beli megerősített alhálózattal. Ehhez tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Hálózati biztonsági csoportok használata](bastion-nsg.md)
