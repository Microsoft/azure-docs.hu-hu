---
title: 'Másolás és beillesztés egy virtuális gépről: Azure Bastion'
description: Ebből a cikkből megtudhatja, hogyan másolhat és illeszthet be egy Azure-beli virtuális gépről a Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cherylmc
ms.openlocfilehash: 50f7906992aa19daa205a30f71ce21456bafe558
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92079156"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Másolás és beillesztés virtuális gépre: Azure Bastion

Ebből a cikkből megtudhatja, hogyan másolhat és illeszthet be szöveget virtuális gépekre az Azure Bastion használatakor. A virtuális gép használata előtt győződjön meg arról, hogy követte a [megerősített gazdagép létrehozásának](./tutorial-create-host-portal.md)lépéseit. Ezután csatlakozzon ahhoz a virtuális géphez, amelyet [RDP](bastion-connect-vm-rdp.md) vagy [SSH](bastion-connect-vm-ssh.md)használatával szeretne használni.

A vágólap speciális API-hozzáférését támogató böngészők esetében a helyi eszköz és a távoli munkamenet között ugyanúgy másolhatja és illesztheti be a szöveget, ahogyan a helyi eszközön lévő alkalmazások között másol és beilleszti. Más böngészők esetében használhatja a megerősített vágólap-hozzáférési eszköz palettáját.

>[!NOTE]
>Jelenleg csak a szöveg másolása/beillesztése támogatott.
>

   ![Vágólap engedélyezése](./media/bastion-vm-manage/allow.png)

Csak a szöveg másolása/beillesztése támogatott. A közvetlen másolás és beillesztés esetében előfordulhat, hogy a böngésző a megerősített munkamenet inicializálásakor kéri a vágólap-hozzáférés megadását. A vágólap elérésének **engedélyezése** a weblap számára.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Másolás távoli munkamenetbe

Miután a [Azure Portal ](https://portal.azure.com)használatával kapcsolódott a virtuális géphez, hajtsa végre a következő lépéseket:

1. Másolja a szöveget/tartalmat a helyi eszközről a helyi vágólapra.
1. A távoli munkamenet során a két nyíl kiválasztásával indítsa el a megerősített vágólap-hozzáférési eszköz palettáját. A nyilak a munkamenet bal középső részén találhatók.

   ![Képernyőfelvétel: az ablak bal oldalán az eszközpaletta Kiemelt nyilait bemutató nyíl.](./media/bastion-vm-manage/left.png)

   ![A képernyőfelvételen a Bastion-ben másolt szöveg vágólapja látható.](./media/bastion-vm-manage/clipboard.png)
1. A másolt szöveg általában automatikusan megjelenik a bástya másolási beillesztési palettáján. Ha a szöveg nem létezik, illessze be a szöveget a paletta szöveg területén.
1. Miután a szöveg szerepel a szövegmezőben, beillesztheti a távoli munkamenetbe.

   ![Képernyőfelvétel: a másolás/beillesztés gomb kiemelve, valamint a távoli munkamenetbe másolt minta szöveges karakterlánc.](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Másolás távoli munkamenetből

Miután a [Azure Portal ](https://portal.azure.com)használatával kapcsolódott a virtuális géphez, hajtsa végre a következő lépéseket:

1. Másolja a távoli munkamenet szövegét/tartalmát a távoli vágólapra (a CTRL-C használatával).

   ![eszközpaletta](./media/bastion-vm-manage/remote.png)
1. A távoli munkamenet során a két nyíl kiválasztásával indítsa el a megerősített vágólap-hozzáférési eszköz palettáját. A nyilak a munkamenet bal középső részén találhatók.

   ![vágólapra](./media/bastion-vm-manage/clipboard2.png)
1. A másolt szöveg általában automatikusan megjelenik a bástya másolási beillesztési palettáján. Ha a szöveg nem létezik, illessze be a szöveget a paletta szöveg területén.
1. Miután a szöveg szerepel a szövegmezőben, beillesztheti azt a helyi eszközre.

   ![illessze be](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
