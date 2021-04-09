---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562843"
---
> [!NOTE]
> Ha módosítja a SharePoint RBS-konfiguráció StorSimple-adapterét, be kell jelentkeznie egy olyan felhasználói fiókkal, amely a Tartománygazdák csoporthoz tartozik. Emellett a konfiguráció lapot a központi adminisztrációval megegyező gazdagépen futó böngészőből kell elérnie.
> 
> 

#### <a name="to-configure-rbs"></a>Az RBS konfigurálása
1. Nyissa meg a Központi SharePoint-felügyelet oldalt, és keresse meg a **Rendszerbeállítások** lapot. 
2. Az **Azure StorSimple** szakaszban kattintson a **StorSimple-adapter konfigurálása** elemre.
   
    ![A StorSimple-adapter konfigurálása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Az **StorSimple-adapter konfigurálása** oldalon:
   
   1. Győződjön meg arról, hogy a **szerkesztési útvonal engedélyezése** jelölőnégyzet be van jelölve.
   2. A szövegmezőbe írja be a BLOB-tároló univerzális elnevezési konvenció (UNC) szerinti elérési útját.
      
      > [!NOTE]
      > A BLOB-tároló kötetét a StorSimple-eszközön konfigurált iSCSI-köteten kell tárolni.

   3. Kattintson az **Engedélyezés** gombra a távoli tárterülethez konfigurálni kívánt tartalmi adatbázisok alatt.
      
      > [!NOTE]
      > A BLOB-tárolót az összes webes előtér-(WFE-) kiszolgálónak meg kell osztania és elérhetőnek kell lennie, és a SharePoint-kiszolgálófarm számára konfigurált felhasználói fióknak hozzáféréssel kell rendelkeznie a megosztáshoz.
      
      ![Az RBS-szolgáltató engedélyezése](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Az RBS engedélyezése vagy letiltása esetén a következő üzenet is megjelenik.
      
      ![StorSimple-adapter engedélyezése – letiltás](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. A konfiguráció alkalmazásához kattintson a **frissítés** gombra. Ha a **frissítés** gombra kattint, az RBS-konfiguráció állapota frissül az összes WFE-kiszolgálón, és a teljes Farm RBS-kompatibilis lesz. A következő üzenet jelenik meg.
      
      ![Adapter konfigurációs üzenete](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Ha olyan SharePoint-farm RBS-konfigurációját konfigurálja, amely nagyon nagy számú adatbázist tartalmaz (200-nál nagyobb), akkor előfordulhat, hogy a Központi SharePoint-adminisztráció weblapja időtúllépést okoz. Ha ez történik, frissítse a lapot. Ez nincs hatással a konfigurációs folyamatra.

4. Ellenőrizze a konfigurációt:
   
   1. Jelentkezzen be a SharePoint központi adminisztrációs webhelyére, és keresse meg az **StorSimple-adapter konfigurálása** lapot.
   2. Ellenőrizze a konfiguráció részleteit, és győződjön meg arról, hogy megfelelnek a megadott beállításoknak. 
5. Ellenőrizze, hogy az RBS megfelelően működik-e:
   
   1. Dokumentum feltöltése a SharePointba. 
   2. Keresse meg a konfigurált UNC elérési utat. Győződjön meg arról, hogy az RBS-címtár szerkezete létrejött, és hogy tartalmazza a feltöltött objektumot.
6. Választható A `Migrate()` SharePoint részét képező Microsoft RBS PowerShell-parancsmag használatával áttelepítheti a meglévő blob-tartalmakat a StorSimple-eszközre. További információkért lásd: [tartalom migrálása az RBS-be vagy abból a sharepoint 2013][6] -be, illetve [tartalom áttelepítése az RBS-be vagy onnan kívülre (SharePoint Foundation 2010)][7].
7. Választható A tesztelési telepítésekben ellenőrizheti, hogy a Blobok kikerültek-e a tartalom-adatbázisból a következőképpen: 
   
   1. Indítsa el az SQL Management Studio.
   2. Futtassa a ListBlobsInDB_2010. SQL vagy ListBlobsInDB_2013. SQL lekérdezést az alábbiak szerint.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Ha az RBS megfelelően lett konfigurálva, a SizeOfContentInDB oszlopban NULL értéknek kell megjelennie az összes feltöltött és az RBS-sel sikeresen ellátott objektumhoz.
8. Választható Miután konfigurálta az RBS-t, és áthelyezte az összes BLOB tartalmát a StorSimple-eszközre, áthelyezheti a tartalom-adatbázist az eszközre. Ha úgy dönt, hogy áthelyezi a tartalom-adatbázist, javasoljuk, hogy a tartalom-adatbázis tárolóját elsődleges kötetként konfigurálja az eszközön. Ezt követően a SQL Server ajánlott eljárások segítségével telepítse át a tartalom-adatbázist a StorSimple-eszközre. 
   
   > [!NOTE]
   > A tartalom-adatbázisnak az eszközre való áthelyezése csak a StorSimple 8000 sorozat esetében támogatott (a 5000-es vagy 7000-as sorozat esetében nem támogatott).
   
   Ha a blobokat és a tartalmi adatbázist különálló köteteken tárolja a StorSimple-eszközön, azt javasoljuk, hogy ugyanazon a kötet-tárolóban konfigurálja azokat. Ez biztosítja, hogy a biztonsági mentés együtt történjen.
   
   > [!WARNING]
   > Ha nem engedélyezte az RBS használatát, nem javasoljuk a tartalom-adatbázis áthelyezését a StorSimple-eszközre. Ez egy nem tesztelt konfiguráció.
   
9. Lépjen a következő lépésre: a [Garbage Collection konfigurálása](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)