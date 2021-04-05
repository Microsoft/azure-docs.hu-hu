---
title: Kapcsolódás Azure Lab Services virtuális géphez a Chromebook-ből | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat egy Chromebook a Azure Lab Services-beli virtuális géphez.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97813224"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Kapcsolódás virtuális géphez RDP protokoll használatával Chromebook

Ez a szakasz azt mutatja be, hogy egy tanuló hogyan tud csatlakozni egy osztályterem Lab virtuális géphez egy Chromebook RDP használatával.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Microsoft Távoli asztal telepítése Chromebook

1. Nyissa meg a Chromebook az App Store áruházat, és keressen rá **Microsoft távoli asztal**.

    ![Microsoft távoli asztal](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Telepítse a Microsoft Távoli asztal legújabb verzióját. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>A virtuális gép elérése a Chromebook RDP használatával

1. Nyissa meg a számítógépen letöltött **RDP** -fájlt, **Microsoft távoli asztal** telepítve van. Meg kell kezdenie a virtuális géphez való csatlakozást. 

    ![Kapcsolódás egy virtuális géphez](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Ha a rendszer kéri, adja meg a jelszavát.

    ![Képernyőfelvétel: a bejelentkezési képernyő, ahol a felhasználónevet és a jelszót adja meg.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Válassza a **Folytatás** lehetőséget, ha a következő figyelmeztetést kapja. 

    ![Tanúsítványra vonatkozó figyelmeztetés](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Látnia kell a virtuális gép asztalát, amelyhez csatlakozik.

## <a name="next-steps"></a>Következő lépések

További információ a Linux rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)

