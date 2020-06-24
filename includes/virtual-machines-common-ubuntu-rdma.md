---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67671205"
---
1. A dapl, a rdmacm, a ibverbs és a mlx4 telepítése

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. A/etc/waagent.conf-ben engedélyezze a RDMA a következő konfigurációs sorok megjegyzésének törlésével. A fájl szerkesztéséhez rendszergazdai jogosultság szükséges.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Adja hozzá vagy módosítsa a következő memória-beállításokat a KB-ban a/etc/Security/Limits.conf fájlban. A fájl szerkesztéséhez rendszergazdai jogosultság szükséges. Tesztelési célból beállíthatja, hogy a memlock korlátlan legyen. Példa: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Telepítse az Intel MPI könyvtárat. [Vásárolja meg és töltse le](https://software.intel.com/intel-mpi-library/) a könyvtárat az Inteltől, vagy töltse le az [ingyenes próbaverziót](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Csak az Intel MPI 5. x futtatókörnyezete támogatott.
 
   A telepítési lépésekért tekintse meg az [Intel MPI-könyvtár telepítési útmutatóját](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. A nem gyökérszintű, nem hibakereső folyamatok ptrace engedélyezése (az Intel MPI legújabb verzióihoz szükséges).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
