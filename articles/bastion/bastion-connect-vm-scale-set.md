---
title: Kapcsolódás Windows rendszerű virtuálisgép-méretezési csoporthoz az Azure Bastion használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat Azure-beli virtuálisgép-méretezési csoportokhoz az Azure Bastion használatával.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619345"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Kapcsolódás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával

Ez a cikk bemutatja, hogyan lehet biztonságosan és zökkenőmentesen RDP-t használni a Windows virtuálisgép-méretezési csoport példányaihoz az Azure-beli virtuális hálózaton az Azure Bastion használatával. Közvetlenül a Azure Portal csatlakozhat egy virtuálisgép-méretezési csoport példányaihoz. Az Azure Bastion használata esetén a virtuális gépeknek nincs szükségük ügyfélre, ügynökre vagy további szoftverre. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállította a virtuálisgép-méretezési csoportba tartozó virtuális hálózathoz tartozó Azure Bastion-gazdagépet. További információ: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a segítségével csatlakozhat egy virtuálisgép-méretezési csoport példányaihoz ebben a virtuális hálózaton. A Bastion azt feltételezi, hogy RDP-t használ egy Windows rendszerű virtuálisgép-méretezési csoporthoz való kapcsolódáshoz, és az SSH-t a linuxos virtuálisgép-méretezési csoporthoz való kapcsolódáshoz. További információ a Linux rendszerű virtuális gépekhez való kapcsolódásról: [Kapcsolódás virtuális géphez – Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Kapcsolat RDP használatával

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Navigáljon ahhoz a virtuálisgép-méretezési csoporthoz, amelyhez csatlakozni szeretne.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Keresse meg a virtuálisgép-méretezési csoport azon példányát, amelyhez csatlakozni szeretne, majd válassza a **Kapcsolódás**lehetőséget. RDP-kapcsolat használata esetén a virtuálisgép-méretezési csoportnak Windows virtuálisgép-méretezési csoportnak kell lennie.

   ![Virtuálisgép-méretezési csoport](./media/bastion-connect-vm-scale-set/2.png)
3. A **kapcsolat**kiválasztása után megjelenik egy oldalsó sáv, amely három lapot – RDP, SSH és Bastion – tartalmaz. Válassza ki az oldalsó sávban a **megerősített** lapot. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, a következő hivatkozásra kattintva állíthatja be a Bastion-t. A konfigurációs utasításokért lásd: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![Megerősített lap](./media/bastion-connect-vm-scale-set/3.png)
4. A megerősített lapon adja meg a virtuálisgép-méretezési csoport felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

   ![csatlakozásra](./media/bastion-connect-vm-scale-set/4.png)
5. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.