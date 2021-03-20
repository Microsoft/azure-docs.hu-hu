---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 97491000c63c966c3d8159b9361fcb60e062aa49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86210403"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. A folyamat megkezdése után a megosztásokhoz nem lehet hozzáférni. Azt is megteheti, hogy kiüríti az eszközt, amellyel az eszközön lévő helyi adat véglegesen törlődik. A lépés befejezése után az e-Ink kijelző megjeleníti a visszáru-szállítási címkét.

> [!IMPORTANT]
> - Az eszközön lévő összes adat véglegesen törölhető ebben a lépésben, és megtisztíthatja az eszközt. Az Azure Storage-fiókban tárolt adatok megmaradnak és felmerülhetnek. Azt javasoljuk, hogy csak akkor törölje ezeket az adatfájlokat, miután meggyőződött róla, hogy az Adatmásolás egy helyszíni adatkiszolgálóra befejeződött.

1. Lépjen a **szállításra való előkészítésra** , és válassza az **előkészítés indítása** lehetőséget. 
   
    ![A szállítás előkészítése 1](media/data-box-export-prepare-to-ship/prepare-to-ship1.png)

 
2. A szállítás előkészítése elindul, és az eszközmegosztások offline állapotba kerülnek. Alapértelmezés szerint az eszköz tisztítása megtörténik, és Data Box törli a lemezén lévő összes adatát. 


    ![A szállítás előkészítése 2](media/data-box-export-prepare-to-ship/prepare-to-ship2.png)

    Kikapcsolhatja a tisztítási eljárást a kombinált lista törlésével. Ebben az esetben az eszközre vonatkozó adat később törlődik az adatközpontban.

    ![A szállítás előkészítése 3](media/data-box-export-prepare-to-ship/prepare-to-ship3.png)


3. A **szállításra való előkészítés** elindítása után egy folyamatjelző sáv jelenik meg.

    ![A szállítás előkészítése 4](media/data-box-export-prepare-to-ship/prepare-to-ship4.png)

4. A felkészülés a szállításra és az eszköz tisztítása (ha be van jelölve) befejezése után az E-Ink megjelenik az eszközön, és megjeleníti a szállítási címke visszaadását is. 

    Megtekintheti a befejezési hivatkozási számot is, amelyet a keresett országtól függően különböző dolgokhoz használ a rendszer. Megtekintheti a visszáru-szállítólevél utasításait is. A kiválasztott szállítás típusától függően a Microsoft által felügyelt vagy önállóan felügyelt utasítások eltérőek lesznek. 
        
    ![A szállítás előkészítése 5](media/data-box-export-prepare-to-ship/prepare-to-ship5.png)


5. Állítsa le az eszközt. Keresse meg a bekapcsológombot az eszköz előlapján. Kapcsolja ki az eszközt.

    ![A Data Box bekapcsológombja](media/data-box-export-prepare-to-ship/data-box-powered-door-open.png)

6. Távolítsa el a kábeleket. A következő lépés az eszköz elküldése a Microsoftnak.
