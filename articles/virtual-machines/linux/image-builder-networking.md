---
title: Az Azure rendszerkép-készítő szolgáltatás hálózatkezelési beállításai
description: Az Azure VM rendszerkép-készítő szolgáltatás üzembe helyezésének hálózati beállításainak ismertetése
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669440"
---
# <a name="azure-image-builder-service-networking-options"></a>Az Azure rendszerkép-készítő szolgáltatás hálózatkezelési beállításai

Az Azure rendszerkép-készítőt meglévő VNET vagy anélkül kell telepítenie.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Üzembe helyezés meglévő VNET meghatározása nélkül

Ha nem ad meg meglévő VNET, az Azure rendszerkép-készítő létrehoz egy VNET és alhálózatot az átmeneti erőforráscsoporthoz. Egy nyilvános IP-erőforrást egy hálózati biztonsági csoporttal kell használni az Azure rendszerkép-készítő szolgáltatás bejövő forgalmának korlátozására. A nyilvános IP-cím megkönnyíti az Azure rendszerkép-készítő parancsainak csatornáját a rendszerkép létrehozása során. A Build befejezése után a rendszer törli a virtuális gépet, a nyilvános IP-címet, a lemezeket és a VNET. Ha ezt a beállítást szeretné használni, ne határozzon meg semmilyen VNET-tulajdonságot.

## <a name="deploy-using-an-existing-vnet"></a>Üzembe helyezés meglévő VNET használatával

Ha megad egy VNET és egy alhálózatot, az Azure rendszerkép-szerkesztő üzembe helyezi a virtuális gépet a kiválasztott VNET. Elérheti a VNET elérhető erőforrásokat. Létrehozhat olyan silózott VNET is, amely nem kapcsolódik más VNET. Ha megad egy VNET, az Azure rendszerkép-készítő nem használ nyilvános IP-címet. Az Azure rendszerkép-készítő szolgáltatás és a Build virtuális gép közötti kommunikáció az Azure Private link technológiáját használja.

További információkért tekintse meg az alábbi példák egyikét:

* [Az Azure rendszerkép-készítő használata a Windows rendszerű virtuális gépekhez, amelyek lehetővé teszik egy meglévő Azure-VNET való hozzáférést](../windows/image-builder-vnet.md)
* [Az Azure-rendszerkép-készítő használata Linux rendszerű virtuális gépekhez, amely lehetővé teszi egy meglévő Azure-VNET való hozzáférést](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat?

Az Azure Private-kapcsolat privát kapcsolatot biztosít egy virtuális hálózatról az Azure platform szolgáltatásként (Pásti), az ügyfél tulajdonában lévő vagy a Microsoft partner szolgáltatásaiba. A szolgáltatás leegyszerűsíti a hálózati architektúrát, és az Azure-beli végpontok közötti kapcsolatot a nyilvános internetre való adatvédelem megszüntetésével biztosítja. További információt a [Private link dokumentációjában](../../private-link/index.yml)talál.

### <a name="required-permissions-for-an-existing-vnet"></a>Szükséges engedélyek egy meglévő VNET

Az Azure rendszerkép-készítő speciális engedélyeket igényel a meglévő VNET használatához. További információ: [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával](image-builder-permissions-cli.md) vagy az [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Mi történik a rendszerkép létrehozásakor?

Egy meglévő VNET használata azt jelenti, hogy az Azure rendszerkép-szerkesztő egy további virtuális gépet (proxy virtuális gépet) és egy Azure Load Balancer (ALB) helyez üzembe, amely a privát kapcsolathoz van csatlakoztatva. A AIB szolgáltatástól érkező forgalom az ALB-beli privát kapcsolaton keresztül halad. Az ALB a 60001-as porton keresztül kommunikál a proxy virtuális géppel a Linux operációs rendszer vagy a 60000 a Windows operációs rendszerhez. A proxy továbbítja a parancsokat a Build virtuális géphez a 22-es port használatával a Linux operációs rendszerhez vagy a 5986 for Windows operációs rendszerhez.

> [!NOTE]
> A VNET ugyanabban a régióban kell lennie, mint az Azure rendszerkép-készítő szolgáltatás régiója.
> 

### <a name="why-deploy-a-proxy-vm"></a>Miért érdemes proxy virtuális gépet üzembe helyezni?

Ha egy nyilvános IP-címmel nem rendelkező virtuális gép belső Load Balancer mögött található, nem rendelkezik internet-hozzáféréssel. A VNET használt Azure Load Balancer belső. A proxy virtuális gép lehetővé teszi az internet-hozzáférést a Build virtuális gép számára a buildek során. A társított hálózati biztonsági csoportok segítségével korlátozható a virtuális gépekhez való hozzáférés.

Az üzembe helyezett proxy virtuálisgép-méret szabványos A1_v2 a Build VM mellett. A proxy virtuális gép az Azure rendszerkép-készítő szolgáltatás és a Build virtuális gép közötti parancsok küldésére szolgál. A proxy virtuális gép tulajdonságai nem módosíthatók, beleértve a méretet vagy az operációs rendszert. A proxy virtuális gép tulajdonságai nem változtathatók meg a Windows-és Linux-rendszerképekhez készült buildek esetében is.

### <a name="image-template-parameters-to-support-vnet"></a>A VNET támogató Képsablon paraméterei
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Beállítás | Leírás |
|---------|---------|
| name | Választható Egy már létező virtuális hálózat neve. |
| subnetName | A megadott virtuális hálózaton belüli alhálózat neve. Csak akkor kell megadni, ha a *név* meg van adva. |
| resourceGroupName | A megadott virtuális hálózatot tartalmazó erőforráscsoport neve. Csak akkor kell megadni, ha a *név* meg van adva. |

A Private link Service-nek az adott VNET és alhálózatból származó IP-címet kell megadnia. Az Azure jelenleg nem támogatja a hálózati házirendeket ezeken az IP-címeken. Ezért a hálózati házirendeket le kell tiltani az alhálózaton. További információt a [Private link dokumentációjában](../../private-link/index.yml)talál.

### <a name="checklist-for-using-your-vnet"></a>Ellenőrzőlista a VNET használatához

1. A proxy virtuális géppel való kommunikáció engedélyezése Azure Load Balancer (ALB) számára egy NSG
    * [AZ CLI example](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell-példa](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Privát szolgáltatási házirend letiltása az alhálózaton
    * [AZ CLI example](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell-példa](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure-rendszerkép-szerkesztő engedélyezése ALB létrehozásához és virtuális gépek hozzáadása a VNET
    * [AZ CLI example](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell-példa](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. A forrásfájlok olvasására és írására, valamint lemezképek létrehozására szolgáló Azure Image Builder engedélyezése
    * [AZ CLI example](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell-példa](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Győződjön meg arról, hogy a VNET-t használja ugyanabban a régióban, mint az Azure rendszerkép-készítő szolgáltatási régiója.


## <a name="next-steps"></a>Következő lépések

További információ: az [Azure rendszerkép-készítő áttekintése](../image-builder-overview.md).