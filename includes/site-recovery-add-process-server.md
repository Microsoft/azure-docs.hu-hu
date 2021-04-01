---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 31e61069c95be9bd1c7a684bb83ebcd93bcb14be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019139"
---
1. Indítsa ez az Azure Site Recovery UnifiedSetup.exe fájlt
2. Az **Előkészületek** területen válassza a **További folyamatkiszolgálók hozzáadása az üzembe helyezés horizontális felskálázásához** lehetőséget.

   ![Folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-1.png)

3. A **Konfigurációs kiszolgáló részletei** területen adja meg a konfigurációs kiszolgáló IP-címét és a hozzáférési kódot.

   ![2. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-2.png)
4. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó szolgáltató hogyan kapcsolódjon Azure site Recovery az interneten keresztül.

   ![3. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-3.png)

   * Ha a gépen jelenleg beállított proxyval szeretne csatlakozni, válassza a **Csatlakozás a meglévő proxybeállításokkal** lehetőséget.
   * Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Connect directly without a proxy** (Közvetlen kapcsolódás proxy nélkül) lehetőséget.
   * Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni a Provider csatlakoztatásához, válassza a **Csatlakozás egyéni proxybeállításokkal** lehetőséget.

     * Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
     * Ha proxyt használ, már engedélyezte a szolgáltatási URL-címekhez való hozzáférést.

5. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

     ![4. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-4.png)

6. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha igen, a telepítő ellenőrzi, hogy a PowerCLI 6.0 telepítve van-e.

     ![5. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-5.png)

7. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.
     ![A bináris fájlok és a gyorsítótár-tároló telepítési helyét bemutató képernyőkép.](./media/site-recovery-add-process-server/ps-page-6.png)

8. A **Hálózat kiválasztása** területen adja meg a figyelőt (hálózati adaptert és SSL-portot), amelyen keresztül a konfigurációs kiszolgáló küldi és fogadja a replikált adatokat. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. A replikációs forgalom küldésére és fogadására ne használja a 443-as portot.

     ![6. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-7.png)
9. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.

     ![7. folyamatkiszolgáló hozzáadása](./media/site-recovery-add-process-server/ps-page-8.png)
