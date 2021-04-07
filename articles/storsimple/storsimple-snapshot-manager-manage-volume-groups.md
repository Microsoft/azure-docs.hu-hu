---
title: StorSimple Snapshot Manager mennyiségi csoportok | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul a mennyiségi csoportok létrehozásához és kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209219"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple-Snapshot Manager használata a kötetek létrehozásához és kezeléséhez
## <a name="overview"></a>Áttekintés
A **hatókör** ablaktáblán a kötetek **csoportok** csomópont használatával rendelhet köteteket a mennyiségi csoportokhoz, megtekintheti a kötetek adatait, ütemezheti a biztonsági mentést, és szerkesztheti a kötetek csoportjait.

A mennyiségi csoportok a kapcsolódó kötetek készletei, amelyek biztosítják, hogy a biztonsági másolatok alkalmazás-konzisztensek legyenek. További információ: [kötetek és mennyiségi csoportok](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) , valamint [a Windows Kötet árnyékmásolata szolgáltatás integrációja](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Egy mennyiségi csoport összes kötetének egyetlen felhőalapú szolgáltatótól kell származnia.
> * A mennyiségi csoportok konfigurálásakor ne keverje össze a fürt megosztott köteteit (CSV) és a nem CSV ugyanabban a kötetben. A StorSimple Snapshot Manager nem támogatja a CSV és a nem CSV együttes használatát ugyanabban a pillanatképben.

![Kötetek csoportjai csomópont](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**1. ábra: StorSimple Snapshot Manager mennyiségi csoportok csomópont** 

Ez az oktatóanyag azt ismerteti, hogyan használható a StorSimple Snapshot Manager a következőkre:

* A mennyiségi csoportokra vonatkozó információk megtekintése
* Kötet csoport létrehozása
* Mennyiségi csoport biztonsági mentése
* Mennyiségi csoport szerkesztése
* Mennyiségi csoport törlése

Az összes művelet a **műveletek** ablaktáblán is elérhető.

## <a name="view-volume-groups"></a>Mennyiségi csoportok megtekintése
Ha a **mennyiségi csoportok** csomópontra kattint, az **eredmények** ablaktábla az egyes kötetekre vonatkozó alábbi információkat jeleníti meg az oszlopok kiválasztott beállításaitól függően. (Az **eredmények** ablaktáblájának oszlopai konfigurálhatók. Kattintson a jobb gombbal a **kötetek** csomópontra, válassza a **nézet**, majd az **Oszlopok hozzáadása/eltávolítása** lehetőséget.)

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Név |A **Name (név** ) oszlop tartalmazza a kötet csoportjának nevét. |
| Alkalmazás |Az **alkalmazások** oszlop a Windows-gazdagépen jelenleg telepített és futó VSS-írók számát jeleníti meg. |
| Bejelölve |A **kiválasztott** oszlop a kötetben található kötetek számát jeleníti meg. A nulla (0) érték azt jelzi, hogy egyetlen alkalmazás sincs társítva a mennyiségi csoport köteteihez. |
| Importált |Az **importált** oszlop az importált kötetek számát jeleníti meg. Ha **igaz** értékre van állítva, akkor ez az oszlop azt jelzi, hogy egy kötet csoport lett importálva a Azure Portalból, és nem lett létrehozva a StorSimple Snapshot Manager. |

> [!NOTE]
> A StorSimple Snapshot Manager kötetek a Azure Portal **biztonsági mentési házirendek** lapján is megjelennek.
> 
> 

## <a name="create-a-volume-group"></a>Kötet csoport létrehozása
A következő eljárással hozhat létre kötet-csoportot.

#### <a name="to-create-a-volume-group"></a>Kötet csoport létrehozása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek csoportok** elemre, majd kattintson a **kötet csoport létrehozása** parancsra.
   
    ![Kötet csoport létrehozása](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Megjelenik a **mennyiségi csoport létrehozása** párbeszédpanel.
   
    ![Kötet csoport létrehozása párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Adja meg a következő információkat:
   
   1. A **név** mezőbe írjon be egy egyedi nevet az új kötet csoport számára.
   2. Az **alkalmazások** mezőben válassza ki a kötetekhez hozzárendelni kívánt kötetekhez tartozó alkalmazásokat.
      
       Az **Applications (alkalmazások** ) mezőben csak azok az alkalmazások jelennek meg, amelyek a StorSimple-köteteket használják, és rendelkeznek VSS-írókkal. A VSS-író csak akkor van engedélyezve, ha az író által ismert összes kötet StorSimple kötet. Ha az alkalmazások mező üres, akkor az Azure StorSimple-köteteket használó alkalmazások telepítése nem támogatott, és a VSS-írók is telepítve vannak. (Az Azure StorSimple jelenleg a Microsoft Exchange-et és SQL Server-t támogatja.) A VSS-írókkal kapcsolatos további információkért lásd: [integráció a Windows Kötet árnyékmásolata szolgáltatással](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Ha kiválaszt egy alkalmazást, a hozzá társított összes kötet automatikusan ki lesz választva. Ha viszont egy adott alkalmazáshoz társított köteteket választ, az alkalmazás automatikusan ki lesz választva az **alkalmazások** mezőben. 
   3. A **kötetek** mezőben válassza ki a StorSimple hozzáadni kívánt kötetek elemet. 
      
      * Egy vagy több partícióval rendelkező kötetek is felvehetők. (Több partíciós kötet lehet dinamikus lemez vagy több partícióval rendelkező alapszintű lemez is.) A több partíciót tartalmazó kötetek egyetlen egységként vannak kezelve. Ennek következtében, ha csak az egyik partíciót adja hozzá egy kötethez, a rendszer az összes többi partíciót is automatikusan hozzáadja az adott kötethez. Miután hozzáadta a több partíciós kötetet egy kötet-csoporthoz, a több partíciós kötet továbbra is egyetlen egységként lesz kezelve.
      * Üres köteteket úgy hozhat létre, hogy nem rendel hozzájuk köteteket. 
      * Ne keverje össze a fürt megosztott köteteit (CSV) és a nem CSV ugyanabban a kötetben. A StorSimple Snapshot Manager nem támogatja a CSV-kötetek és a nem CSV-kötetek együttes használatát ugyanabban a pillanatképben.
4. A kötet mentéséhez kattintson **az OK** gombra.

## <a name="back-up-a-volume-group"></a>Mennyiségi csoport biztonsági mentése
A kötetek biztonsági mentését az alábbi eljárással hozhatja létre.

#### <a name="to-back-up-a-volume-group"></a>Mennyiségi csoport biztonsági mentése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **kötetek csoportjai** csomópontot, kattintson a jobb gombbal a kötet csoport nevére, majd kattintson a **biztonsági mentés** készítése elemre.
   
    ![A kötet csoport biztonsági mentése azonnal](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. A **biztonsági mentés** készítése párbeszédpanelen válassza a **helyi pillanatkép** vagy a **Felhőbeli pillanatkép** lehetőséget, majd kattintson a **Létrehozás** gombra.
   
    ![Biztonsági mentés készítése párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Annak ellenőrzéséhez, hogy a biztonsági mentés fut-e, bontsa ki a **feladatok** csomópontot, majd kattintson a **Futtatás** elemre. A biztonsági másolatnak szerepelnie kell a felsorolásban.
5. A befejezett pillanatkép megtekintéséhez bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoport nevét, majd kattintson a **helyi pillanatkép** vagy a **Felhőbeli pillanatkép** elemre. A biztonsági mentés akkor jelenik meg, ha a művelet sikeresen befejeződött.

## <a name="edit-a-volume-group"></a>Mennyiségi csoport szerkesztése
A következő eljárással szerkesztheti a kötetek csoportjait.

#### <a name="to-edit-a-volume-group"></a>Mennyiségi csoport szerkesztése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **kötetek csoportjai** csomópontot, kattintson a jobb gombbal a kötet csoport nevére, majd kattintson a **Szerkesztés** parancsra.
3. Megjelenik a **mennyiségi csoport létrehozása** párbeszédpanel. Módosíthatja a **nevet**, az **alkalmazásokat** és a **kötetek** bejegyzéseit.
4. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-a-volume-group"></a>Mennyiségi csoport törlése
A köteteket a következő eljárással törölheti. 

> [!WARNING]
> Ezzel törli a mennyiségi csoporthoz társított összes biztonsági mentést is.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Mennyiségi csoport törlése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **kötetek csoportjai** csomópontot, kattintson a jobb gombbal a kötet csoport nevére, majd kattintson a **Törlés** parancsra.
3. Megjelenik a **kötet törlése** párbeszédpanel. Írja be a **megerősítés** szöveget a szövegmezőbe, majd kattintson **az OK** gombra.
   
    A törölt számítógépcsoport eltűnik az **eredményeket** tartalmazó ablaktábla listájáról, és az adott kötethez társított összes biztonsági mentés törlődik a biztonsági mentési katalógusból.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet biztonsági mentési házirendeket a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-backup-policies.md).

