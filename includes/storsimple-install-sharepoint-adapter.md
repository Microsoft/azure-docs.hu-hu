---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e2d258f327ade2149adc92bdb7fb4bcc87cdd58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179160"
---
#### <a name="to-install-the-storsimple-adapter-for-sharepoint"></a>A SharePoint rendszerhez készült StorSimple-adapter telepítése
1. Másolja a telepítőt a webes előtér-(WFE-) kiszolgálóra, amely a Központi SharePoint-felügyelet webalkalmazás futtatására is konfigurálva van. 
2. Rendszergazdai jogosultságokkal rendelkező fiókkal jelentkezzen be a WFE-kiszolgálóra.
3. Kattintson duplán a telepítőre. Elindul a StorSimple-adapter a SharePoint rendszerhez telepítővarázslója. A telepítés megkezdéséhez kattintson a **tovább** gombra.
   
    ![StorSimple-adapter telepítésének kezdőlapja](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup1-include.png)
4. A SharePoint telepítő konfigurációjának StorSimple-adaptere lapon válassza ki a telepítési helyet, írja be a StorSimple-eszközön található adat0 hálózati adapter IP-címét, majd kattintson a **tovább** gombra. 
   
    ![StorSimple-adapter beállításának beállítása lap](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup2-include.png) 
5. A telepítés megerősítése lapon kattintson a **telepítés** elemre.
   
    ![StorSimple-adapter beállításának megerősítése lap](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Confirm_Setup-include.png) 
6. A telepítővarázsló bezárásához kattintson a **Befejezés** gombra.
   
    ![StorSimple-adapter telepítésének befejezése lap](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup_finish-include.png) 
7. Nyissa meg a Központi SharePoint-felügyelet lapot. Meg kell jelennie egy olyan StorSimple-konfigurációs csoportnak, amely tartalmazza a SharePoint-hivatkozások StorSimple-adapterét.
8. Lépjen a következő lépésre: az [RBS konfigurálása](#configure-rbs).

