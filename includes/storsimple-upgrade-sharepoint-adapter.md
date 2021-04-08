---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561596"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Frissítse a SharePoint 2010-et a SharePoint 2013-re, majd telepítse a SharePointhoz készült StorSomple-adaptert
> [!IMPORTANT]
> A korábban az RBS használatával külső tárolóba áthelyezett fájlok addig nem lesznek elérhetők, amíg a frissítés be nem fejeződik, és az RBS szolgáltatás ismét engedélyezve lesz. A felhasználói hatás korlátozásához hajtson végre minden frissítést vagy újratelepítést egy tervezett karbantartási időszak alatt.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>A SharePoint 2010 frissítése a SharePoint 2013-re, majd az adapter telepítése
1. A SharePoint 2010-farmban jegyezze fel a külső Blobok BLOB-tárolójának elérési útját, valamint azokat a tartalmi adatbázisokat, amelyeken az RBS engedélyezve van. 
2. Telepítse és konfigurálja az új SharePoint 2013-farmot. 
3. Adatbázisok, alkalmazások és webhelycsoportok áthelyezése a SharePoint 2010-farmról az új SharePoint 2013-farmba. Útmutatásért [tekintse meg a frissítési folyamat áttekintése a SharePoint 2013-re](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process)című témakört.
4. Telepítse a SharePointhoz készült StorSimple-adaptert az új farmon. Nyissa meg [a StorSimple-adapter sharepointhoz való telepítését](#install-the-storsimple-adapter-for-sharepoint) ismertető eljárást.
5. Az 1. lépésben feljegyzett információk alapján engedélyezze az RBS-t ugyanahhoz a tartalom-adatbázishoz, és adja meg ugyanazt a BLOB-tároló elérési útját, amelyet a SharePoint 2010-telepítésben használt. Nyissa meg az [RBS konfigurálása](#configure-rbs) eljárásokhoz című témakört. A lépés elvégzése után a korábban kihelyezett fájlokat elérhetővé kell tenni az új farmról. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>A SharePointhoz készült StorSimple-adapter frissítése
> [!IMPORTANT]
> Ezt a frissítést úgy kell ütemezni, hogy a tervezett karbantartási időszak során a következő okok miatt történjen:
> 
> * A korábban külsőleg elvégezhető tartalom addig nem lesz elérhető, amíg újra nem telepítik az adaptert.
> * A webhelyre feltöltött bármely tartalom, miután eltávolította a SharePointhoz készült StorSimple-adapter előző verzióját, de az új verzió telepítése előtt a rendszer a tartalom-adatbázisban tárolja. Az új adapter telepítése után át kell helyeznie a tartalmat a StorSimple-eszközre. A `RBS Migrate()` sharepointhoz mellékelt Microsoft PowerShell-parancsmag használatával áttelepítheti a tartalmat. További információ: [tartalom migrálása az RBS-be vagy](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))abból. 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>A SharePointhoz készült StorSimple-adapter frissítése
1. Távolítsa el a SharePointhoz készült StorSimple-adapter előző verzióját.
   
   > [!NOTE]
   > Ez automatikusan letiltja az RBS-t a tartalom-adatbázisokban. A meglévő Blobok azonban továbbra is a StorSimple-eszközön maradnak. Mivel az RBS le van tiltva, és a Blobok nem lettek áttelepítve a tartalom-adatbázisba, a Blobokra vonatkozó kérelmek sikertelenek lesznek. 
   > 
   > 
2. Telepítse az új StorSimple-adaptert a SharePointhoz. Az új adapter automatikusan felismeri az RBS-ben korábban engedélyezett vagy letiltott tartalmi adatbázisokat, és az előző beállításokat fogja használni.