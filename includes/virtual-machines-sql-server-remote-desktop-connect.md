---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179075"
---
1. Miután az Azure virtuális gép létrejött és fut, kattintson a Virtual Machines ikonra az Azure Portalon a virtuális gépek megtekintéséhez.

1. Kattintson a három pontot ábrázoló gombra ( **...** ) az új virtuális gép mellett.

1. Kattintson a **Connect** (Csatlakozás) gombra.

   ![Csatlakozás a portálon lévő virtuális géphez](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Nyissa meg a böngésző által a virtuális géphez letöltött **RDP**-fájlt.

1. A távoli asztali kapcsolat arról értesíti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Csatlakozás** gombra a folytatáshoz.

1. A **Windows rendszerbiztonság** párbeszédpanelen kattintson a **Másik fiók használata** elemre. Ha ez a lehetőség nem látható, a **További lehetőségek** gombra kattintva jeleníthető meg. Adja meg a virtuális gép létrehozásakor konfigurált felhasználónevet és jelszót. A felhasználónév elé írjon egy fordított perjelet.

   ![Távoli asztal hitelesítése](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. A csatlakozáshoz kattintson az **OK** gombra.
