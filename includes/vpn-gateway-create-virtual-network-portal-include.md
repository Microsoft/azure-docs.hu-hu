---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d35da4f1eaed91411c015ed7665944d886f9d79c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407665"
---
Az alábbi lépésekkel hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. Használja a [példaértékeket](#values), ha a lépéseket csupán az oktatóanyag elvégzése érdekében hajtja végre. Ha a lépéseket nem az oktatóanyag keretében hajtja végre, ne felejtse el az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon, egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amelyet kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>
>

1. Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és arra kattintva nyissa meg a **Virtuális hálózat** lapot.
3. A Virtuális hálózat lap alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemre. Ez megnyitja a „Virtuális hálózat létrehozása” lapot.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "A Virtuális hálózat létrehozása lap")
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek.

   - **Név**: Adja meg a virtuális hálózat nevét. Ebben a példában a VNet1 nevet használjuk.
   - **Címtér**: Adja meg a címteret. Ha több címteret szeretne felvenni, vegye fel az elsőt. A virtuális hálózat létrehozása után később további címtereket is felvehet. Ellenőrizze, hogy a megadott címtér ne legyen átfedésben a helyszínen található címtérrel.
   - **Előfizetés**: Ellenőrizze, hogy a felsorolt előfizetések a megfelelőt. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
   - **Hely**: Válassza ki a virtuális hálózat helyét. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
   - **Alhálózat**: Adja hozzá az első alhálózat nevét és az alhálózat címtartományát. A virtuális hálózat létrehozása után később további alhálózatokat és az átjáró alhálózatát is felveheti. 

5. Ha szeretné könnyen megtalálni a VNetet az irányítópulton, akkor válassza a **Pin to dashboard** (Rögzítés az irányítópulton) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra. A **Create** (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.
