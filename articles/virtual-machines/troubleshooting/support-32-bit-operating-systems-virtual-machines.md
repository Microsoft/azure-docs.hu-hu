---
title: 32 bites operációs rendszerek támogatása az Azure Virtual Machines szolgáltatásban | Microsoft Docs
description: Információk az Azure Virtual Machines szolgáltatásban támogatott operációs rendszerekről
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 1b801bac28b131ff43c47024649bfe0986f768c7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202895"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken

A Microsoft Azure mostantól lehetővé teszi a felhasználók számára, hogy a 32 bites Windows operációs rendszereket használják az Azure-ba. Csak a speciális VHD-k támogatottak, és az általánosított lemezképek nem fognak működni az Azure-ban. Mivel a fenti operációs rendszerek némelyike már elérte az élettartam-támogatási szerződést, előfordulhat, hogy a Microsoft nem nyújt további támogatást. A támogatás nem érhető el a Linux-alapú vagy a Berkeley Distribution (BSD) alapú operációs rendszerekhez, amelyek Microsoft Azure virtuális gépen (VM) futnak.

> [!NOTE]
> Az Azure platformhoz a 32 bites operációs rendszert futtató virtuális gépekre vonatkozó memória-címtartomány-korlátozás vonatkozik, ahol a virtuális gép csak 1 GB memóriát tesz elérhetővé, és a virtuális gép memóriája a vendég virtuális gépen foglalt módon fog megjelenni. Ez egy ismert probléma, és jelenleg nem található ETA a javításhoz. Javasoljuk, hogy váltson 64 bites operációsrendszer-verzióra.
> 

## <a name="more-information"></a>További információ

Az Azure Virtual Machines szolgáltatásban támogatott operációs rendszerekkel kapcsolatos további információkért látogasson el a Microsoft Tudásbázis következő cikkeire:

* [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [A Linux és a nyílt forráskódú technológiák támogatása az Azure-ban](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Hivatkozások

* [További információ az Azure-beli Windows Server 2008/R2-hez készült ingyenes bővített biztonsági frissítésekről](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [További információ a Windows Server 2008 SP2 32 bites speciális rendszerképek támogatásáról az Azure-ban](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [További információ a Windows Server 2008-lemezképek Azure-ba való áttelepítésének támogatásáról Azure Site Recovery használatával](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [További információ az Azure-bővítmény által támogatott operációs rendszerekről](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [További információ a Windows Server 2003 futtatásáról Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Következő lépések

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/).

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
