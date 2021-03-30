---
title: Kapcsolódás Azure Lab Services virtuális géphez Mac rendszerből | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat Mac rendszerű virtuális géphez Azure Lab Services-ben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444624"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Kapcsolódás virtuális géphez Mac RDP protokoll használatával
Ez a szakasz azt mutatja be, hogy egy tanuló hogyan tud csatlakozni egy osztályterem Lab virtuális géphez Mac-ről RDP használatával.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft Távoli asztal telepítése Mac gépen
1. Nyissa meg az App Store-t a Mac számítógépen, és keressen rá **Microsoft távoli asztal**.

    ![Microsoft távoli asztal](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Telepítse a Microsoft Távoli asztal legújabb verzióját. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>A virtuális gép elérése Mac-kiszolgálóról RDP használatával
1. Nyissa meg a számítógépen letöltött **RDP** -fájlt, **Microsoft távoli asztal** telepítve van. Meg kell kezdenie a virtuális géphez való csatlakozást. 

    ![Kapcsolódás egy virtuális géphez](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Válassza a **Folytatás** lehetőséget, ha a következő figyelmeztetést kapja. 

    ![Tanúsítványra vonatkozó figyelmeztetés](./media/how-to-use-classroom-lab/certificate-error.png)
1. A virtuális gépet látnia kell. 

    > [!NOTE]
    > Az alábbi példa egy CentOS Linux rendszerű virtuális gép. 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Következő lépések
A Linux rendszerű virtuális gépek RDP használatával történő kapcsolódásával kapcsolatos információkért lásd: a [Távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)


