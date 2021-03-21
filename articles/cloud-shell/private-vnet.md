---
title: Cloud Shell egy Azure-Virtual Network
description: Cloud Shell üzembe helyezése Azure-beli virtuális hálózaton
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222802"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Cloud Shell üzembe helyezése Azure-beli virtuális hálózaton

A rendszeres Cloud Shell munkamenetek egy Microsoft-hálózat egy tárolójában futnak az erőforrásaitól. Ez azt jelenti, hogy a tárolón belül futó parancsok nem férnek hozzá olyan erőforrásokhoz, amelyek csak egy adott virtuális hálózatról érhetők el. Az SSH nem használható például olyan virtuális géphez való Cloud Shell kapcsolódáshoz, amely csak privát IP-címmel rendelkezik, vagy a kubectl használatával csatlakozik egy Kubernetes-fürthöz, amely zárolta a hozzáférést. 

Ez a választható szolgáltatás ezeket a korlátozásokat kezeli, és lehetővé teszi Cloud Shell üzembe helyezését egy Ön által felügyelt Azure-beli virtuális hálózatban. A tároló képes a kiválasztott virtuális hálózaton belüli erőforrásokkal való interakcióra.  

Alább láthatja az ebben a forgatókönyvben üzembe helyezni és használni kívánt erőforrás-architektúrát.

![A Cloud Shell izolált VNET architektúrát mutatja be.](media/private-vnet/data-diagram.png)

A Cloud Shell saját Azure-Virtual Network való használata előtt több erőforrást kell létrehoznia a funkció támogatásához. Ez a cikk bemutatja, hogyan állíthatja be a szükséges erőforrásokat egy ARM-sablon használatával.

> [!NOTE]
> Ezeket az erőforrásokat csak egyszer kell beállítani a virtuális hálózathoz. Ezt követően az összes rendszergazda megoszthatja a virtuális hálózathoz való hozzáférést.

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

### <a name="virtual-network"></a>Virtuális hálózat
A virtuális hálózat határozza meg azt a címtartományt, amelyben egy vagy több alhálózat létre van hozva.

Meg kell határozni a Cloud Shell használni kívánt virtuális hálózatot. Ez általában egy meglévő virtuális hálózat, amely a felügyelni kívánt erőforrásokat vagy az erőforrásokat tartalmazó hálózatokat tartalmazó hálózatot tartalmaz.

### <a name="subnet"></a>Alhálózat
A kiválasztott virtuális hálózaton belül egy dedikált alhálózatot kell használni Cloud Shell tárolók számára. Ez az alhálózat a Azure Container Instances (ACI) szolgáltatáshoz van delegálva.  Amikor egy felhasználó egy Cloud Shell tárolót kér egy virtuális hálózatban, Cloud Shell az ACI használatával hoz létre egy olyan tárolót, amely ebben a delegált alhálózatban található.  Ebben az alhálózatban nem hozhatók létre más erőforrások.

### <a name="network-profile"></a>Hálózati profil
A hálózati profil az Azure-erőforrások hálózati konfigurációs sablonja, amely az erőforrás bizonyos hálózati tulajdonságait határozza meg.

### <a name="azure-relay"></a>Azure Relay
Egy [Azure Relay](../azure-relay/relay-what-is-it.md) két végpontot engedélyez, amelyek nem érhetők el közvetlenül a kommunikációhoz. Ebben az esetben a rendszer lehetővé teszi, hogy a rendszergazda böngészője kommunikáljon a magánhálózaton lévő tárolóval.

A Cloud Shellhoz használt Azure Relay-példány konfigurálható úgy, hogy szabályozza, mely hálózatok férhetnek hozzá a tároló erőforrásaihoz: 
- Elérhető a nyilvános internetről: ebben a konfigurációban a Cloud Shell lehetővé teszi az egyéb belső erőforrások elérésének módját. 
- Elérhető a megadott hálózatokból: ebben a konfigurációban a rendszergazdáknak hozzá kell férniük a Azure Portal egy olyan számítógépről, amely a megfelelő hálózaton fut, hogy használni tudja a Cloud Shell.

## <a name="storage-requirements"></a>Tárolási követelmények
A standard Cloud Shellhoz hasonlóan a Cloud Shell virtuális hálózatban való használatakor is szükség van egy Storage-fiókra. Minden rendszergazdának szüksége van egy fájlmegosztás tárolására a fájljainak tárolásához.  A Storage-fióknak a Cloud Shell által használt virtuális hálózatról kell elérhetőnek lennie. 

## <a name="virtual-network-deployment-limitations"></a>A virtuális hálózat központi telepítésére vonatkozó korlátozások
* Az érintett további hálózati erőforrások miatt a virtuális hálózatban Cloud Shell elindítása általában lassabb, mint a normál Cloud Shell-munkamenet.

* Jelenleg a közép-indiai régión kívüli Cloud Shell régiók is támogatottak. 

* A [Azure Relay](../azure-relay/relay-what-is-it.md) nem ingyenes szolgáltatás, tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/service-bus/). A Cloud Shell-forgatókönyvben minden rendszergazda számára egy hibrid kapcsolatot használ a Cloud Shell használatakor. A Cloud Shell munkamenet befejezése után a rendszer automatikusan leállítja a kapcsolatot.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Microsoft. ContainerInstances erőforrás-szolgáltatót regisztrálni kell a használni kívánt virtuális hálózatot birtokló előfizetésben. Válassza ki a megfelelő előfizetést `Set-AzContext -Subscription {subscriptionName}` , majd futtassa a parancsot:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Ha a **RegistrationState** van `Registered` , nincs szükség beavatkozásra. Ha igen `NotRegistered` , futtassa a parancsot `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Hálózati erőforrások üzembe helyezése
 
### <a name="create-a-resource-group-and-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása
Ha már van olyan kívánt VNET, amelyhez csatlakozni szeretne, ugorja át ezt a szakaszt.

A Azure Portalon vagy az Azure CLI-vel, Azure PowerShellsal stb. hozzon létre egy erőforráscsoportot és egy virtuális hálózatot az új erőforráscsoporthoz, **az erőforráscsoport és a virtuális hálózatnak ugyanabban a régióban kell lennie**.

### <a name="arm-templates"></a>ARM-sablonok
Az Azure gyors üzembe helyezési [sablonnal](https://aka.ms/cloudshell/docs/vnet/template) Cloud Shell erőforrásokat hozhat létre egy virtuális hálózaton, valamint az Azure gyors üzembe helyezési [sablonját](https://aka.ms/cloudshell/docs/vnet/template/storage) a szükséges tárterület létrehozásához. Jegyezze fel az erőforrások neveit, elsősorban a fájlmegosztás nevét.

### <a name="open-relay-firewall"></a>Továbbító tűzfal megnyitása
Navigáljon a fenti sablonnal létrehozott továbbítóhoz, válassza a "hálózatkezelés" lehetőséget a beállítások területen, és engedélyezze a hozzáférést a böngésző hálózatáról a továbbítóhoz. Alapértelmezés szerint a továbbítás csak abban a virtuális hálózatban érhető el, amelyet a ben hozott létre. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Cloud Shell konfigurálása virtuális hálózat használatára.
> [!NOTE]
> Ezt a lépést minden rendszergazda Cloud Shell fogja használni.

A fenti lépések befejezését követően navigáljon Cloud Shell a Azure Portal vagy a következőn: https://shell.azure.com . Az egyik ilyen tapasztalatot minden alkalommal fel kell használni, amikor egy elkülönített Cloud Shell felhasználói felülethez szeretne csatlakozni.

> [!NOTE]
> Ha korábban már használta a Cloud Shell, a meglévő clouddrive le kell választania. Ha a futtatását `clouddrive unmount` aktív Cloud Shell munkamenetből szeretné elvégezni, frissítse a lapot.

Cloud Shell csatlakozhat, a rendszer az első futtatási élményt fogja kérni. Válassza ki a kívánt rendszerhéj-felületet, válassza a speciális beállítások megjelenítése lehetőséget, és válassza a "VNET elkülönítési beállítások megjelenítése" mezőt. Töltse ki a mezőket az előugró ablakban.  A legtöbb mező automatikusan kitöltődik a virtuális hálózatban Cloud Shell társítható elérhető erőforrásokkal.  A fájlmegosztás nevét a felhasználónak kell kitöltenie.


![A Cloud Shell izolált VNET első élményének beállításait mutatja be.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Következő lépések
[További tudnivalók az Azure Virtual Networkről](../virtual-network/virtual-networks-overview.md)
