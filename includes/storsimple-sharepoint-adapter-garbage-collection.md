---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9c734ff03b1cf277c7e0967d8b76b1941434f414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050469"
---
Ebben az eljárásban a következőket kell tennie:

1. [Felkészülés a karbantartó végrehajtható fájl futtatására](#to-prepare-to-run-the-maintainer) .
2. [Készítse elő a tartalom-adatbázist és a Lomtárat az árva Blobok azonnali törléséhez](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Maintainer.exefuttatása ](#to-run-the-maintainer).
4. [A tartalom-adatbázis és a Lomtár beállításainak visszaállítása](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Felkészülés a karbantartó futtatására
1. A webes előtér-kiszolgálón nyissa meg a SharePoint 2013 felügyeleti rendszerhéjt rendszergazdaként.
2. Navigáljon a mappa *rendszerindítási meghajtó*: \PROGRAM Files\Microsoft SQL távoli Blob Storage 10.50 \ karbantartó\.
3. **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** átnevezése **web.configra**.
4. Használja az `aspnet_regiis -pdf connectionStrings` web.config fájl visszafejtéséhez.
5. A visszafejtett web.config fájlban a csomópont alatt `connectionStrings` adja hozzá az SQL Server-példány és a tartalom-adatbázis nevét. Tekintse meg a következő példát.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. `aspnet_regiis –pef connectionStrings`A web.config fájl ismételt titkosítására használható. 
7. web.config átnevezése Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.configra. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>A tartalom-adatbázis és a Lomtár előkészítése az árva Blobok azonnali törléséhez
1. Az SQL Management Studio SQL Server futtassa a következő frissítési lekérdezéseket a célként megadott tartalom-adatbázishoz: 

    `use WSS_Content`

    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. A webes előtér-kiszolgálón a **központi felügyelet** területen szerkessze a **webalkalmazás általános beállításait** a kívánt tartalom-adatbázishoz a Lomtár ideiglenes letiltásához. Ez a művelet a kapcsolódó webhelycsoportok esetében is üresen fogja hagyni a Lomtár használatát. Ehhez kattintson a **központi adminisztrációs**  ->  **alkalmazás-kezelés**  ->  **webalkalmazások (webalkalmazások kezelése)**  ->  **SharePoint-80**  ->  **általános Alkalmazásbeállítások** elemre. Állítsa ki a **Lomtár állapotát** **ki** értékre.
   
    ![Webalkalmazás általános beállításai](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>A karbantartó futtatása
* A webes előtér-kiszolgálón a SharePoint 2013 felügyeleti rendszerhéjban futtassa a karbantartót a következő módon:
  
    `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Jelenleg csak a `GarbageCollection` művelet támogatott a StorSimple. Azt is vegye figyelembe, hogy a Microsoft.Data.SqlRemoteBlobs.Maintainer.exe számára kiadott paraméterek megkülönböztetik a kis-és nagybetűket. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>A tartalom-adatbázis és a Lomtár beállításainak visszaállítása
1. Az SQL Management Studio SQL Server futtassa a következő frissítési lekérdezéseket a célként megadott tartalom-adatbázishoz:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. A webes előtér-kiszolgálón a **központi felügyelet** lapon szerkessze a **webalkalmazás általános beállításait** a kívánt tartalom-adatbázishoz a Lomtár újbóli engedélyezéséhez. Ehhez kattintson a **központi adminisztrációs**  ->  **alkalmazás-kezelés**  ->  **webalkalmazások (webalkalmazások kezelése)**  ->  **SharePoint-80**  ->  **általános Alkalmazásbeállítások** elemre. A Lomtár állapotának beállítása **a** következőre:.

