---
title: Kapcsolódás Windows rendszerű virtuális géphez az Azure Bastion használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Windows rendszerű Azure-beli virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521534"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával

Az Azure Bastion használatával biztonságosan és zökkenőmentesen csatlakozhat a virtuális gépekhez az SSL-en keresztül közvetlenül a Azure Portal. Az Azure Bastion használata esetén a virtuális gépekhez nem szükséges ügyfél, ügynök vagy további szoftver. Ez a cikk bemutatja, hogyan csatlakozhat a Windows rendszerű virtuális gépekhez. További információ a Linux rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás Linux rendszerű](bastion-connect-vm-ssh.md)virtuális géphez.

Az Azure Bastion biztonságos kapcsolatot biztosít a virtuális hálózatban lévő összes olyan virtuális géphez, amelyben kiépítve van. Az Azure Bastion használatával megvédheti a virtuális gépeket az RDP/SSH-portok a külvilág felé való kitéve, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. További információ: [Mi az az Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, ellenőrizze, hogy teljesültek-e az alábbi feltételek:

* Már telepítve van egy VNet a megerősített gazdagéptel.

   Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózatban található bármelyik virtuális GÉPHEZ. Az Azure Bastion-gazdagépek beállításával kapcsolatban lásd: [megerősített gazdagép létrehozása](tutorial-create-host-portal.md#createhost).
* Egy Windows rendszerű virtuális gép a virtuális hálózaton.
* A következő szükséges szerepkörök:
  * Olvasó szerepkör a virtuális gépen.
  * Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.
  * Olvasó szerepkör az Azure-beli megerősített erőforráson.
* Portok: a Windows rendszerű virtuális géphez való csatlakozáshoz a következő portokat kell megnyitnia a Windows rendszerű virtuális gépen:
  * Bejövő portok: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Csatlakozni

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.