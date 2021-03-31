---
title: StorSimple Snapshot Manager MMC menü műveletei | Microsoft Docs
description: Ismerteti, hogyan használható a Microsoft Management Console (MMC) szabványos StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90056003"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Használja az MMC menü műveleteit a StorSimple Snapshot Manager

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager a következő műveleteket fogja látni az összes művelet menüiben és a **műveletek** ablaktábla összes változatában.

* Nézet
* Új ablak innen 
* Frissítés 
* Exportálási lista 
* Help 

Ezek a műveletek a Microsoft Management Console (MMC) részét képezik, és nem kifejezetten a StorSimple Snapshot Manager. Ez az oktatóanyag ismerteti ezeket a műveleteket, és elmagyarázza, hogyan használhatja őket a StorSimple Snapshot Manager.

## <a name="view"></a>Nézet
A **View (megtekintés** ) lehetőséggel módosíthatja az **eredmények** ablaktábla nézetét, és módosíthatja a konzol ablak nézetét. 

#### <a name="to-change-the-results-pane-view"></a>Az eredmények ablaktábla nézetének módosítása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemre az **eredmények** ablaktábláján, majd kattintson a **nézet** lehetőségre. 
3. Az **eredmények** ablaktáblán megjelenő oszlopok hozzáadásához vagy eltávolításához kattintson az **Oszlopok hozzáadása/eltávolítása** elemre. Megjelenik az **Oszlopok hozzáadása/eltávolítása** párbeszédpanel.
   
    ![Oszlopok hozzáadása vagy eltávolítása az eredmények ablaktáblájából](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Töltse ki az űrlapot a következőképpen:
   
   * Jelölje ki az **elérhető** oszlopok listából az elemeket, majd a **Hozzáadás** gombra kattintva vegye fel őket a **megjelenített oszlopok** listájára. 
   * A **megjelenített oszlopok** listában kattintson az elemek elemre, majd az **Eltávolítás** gombra kattintva távolítsa el őket a listáról. 
   * Jelöljön ki egy elemet a **megjelenített** oszlopok listájában, **és kattintson a feljebb vagy** **lejjebb gombra, hogy az** elemet feljebb vagy lejjebb helyezze a listában. 
   * Kattintson az **Alapértelmezések visszaállítása** lehetőségre az alapértelmezett **eredmények** ablaktábla konfigurációjához való visszatéréshez. 
5. Ha végzett a beállításokkal, kattintson **az OK** gombra. 

#### <a name="to-change-the-console-window-view"></a>A konzol ablak nézetének módosítása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, kattintson a **nézet** elemre, majd kattintson a **Testreszabás** elemre. Megjelenik a **Testreszabás** párbeszédpanel.
   
    ![A konzol ablakának testreszabása](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Jelölje be a jelölőnégyzetet, vagy törölje a jelölést az elemek megjelenítéséhez vagy elrejtéséhez a konzol ablakban. Ha végzett a beállításokkal, kattintson **az OK** gombra.

## <a name="new-window-from-here"></a>Új ablak innen
Új konzolablak megnyitásához az **itt található új ablak** lehetőséget használhatja.

#### <a name="to-open-a-new-console-window"></a>Új konzolablak megnyitása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, majd kattintson az **új ablak** innen elemre. 
   
    Ekkor megjelenik egy új ablak, amely csak a kiválasztott hatókört jeleníti meg. Ha például a jobb gombbal a **biztonsági mentési házirendek** csomópontra kattint, az új ablak csak a **biztonsági mentési házirendek** csomópontot jeleníti meg a **hatókör** ablaktáblán, valamint az **eredményeket** tartalmazó ablaktáblán található meghatározott biztonsági mentési szabályzatok listáját. Tekintse meg a következő példát.
   
    ![Új ablak innen](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Frissítés
A **frissítési** művelettel frissítheti a konzolablak ablakát.

#### <a name="to-update-the-console-window"></a>A konzol ablakának frissítése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemre az **eredmények** ablaktábláján, majd kattintson a **frissítés** parancsra. 

## <a name="export-list"></a>Exportálási lista
A lista **exportálása** művelettel egy vesszővel tagolt (CSV-) fájlba mentheti a listákat. Exportálhatja például a biztonsági mentési szabályzatok listáját vagy a biztonsági mentési katalógust. Ezután importálhatja a CSV-fájlt egy táblázatkezelő alkalmazásba elemzés céljából.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Lista mentése vesszővel tagolt (CSV-) fájlba
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára. 
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemre az **eredmények** ablaktábláján, majd kattintson a **lista exportálása** parancsra. 
3. Megjelenik az **exportálási lista** párbeszédpanel. Töltse ki az űrlapot a következőképpen: 
   
   1. A **fájlnév** mezőben adja meg a CSV-fájl nevét, vagy kattintson a nyílra a legördülő listából való kiválasztáshoz.
   2. A fájltípus **mezőben kattintson** a nyílra, és válassza ki a kívánt fájltípust a legördülő listából.
   3. Csak a kijelölt elemek mentéséhez jelölje ki a sorokat, majd kattintson a **csak a kijelölt sorok mentése** jelölőnégyzetre. Az összes exportált lista mentéséhez törölje a jelölést a **csak a kijelölt sorok mentése** jelölőnégyzetből.
   4. Kattintson a **Mentés** gombra.
      
      ![Lista exportálása vesszővel elválasztott érték fájlként](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
A **Súgó** menü használatával megtekintheti a StorSimple Snapshot Manager és az MMC-hez elérhető online súgót.

#### <a name="to-view-available-online-help"></a>Az elérhető online Súgó megtekintése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópontra, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemre az **eredmények** ablaktábláján, majd kattintson a **Súgó** elemre. 

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple Snapshot Manager felhasználói felületéről](storsimple-use-snapshot-manager.md).
* További információ a [StorSimple Snapshot Manager használatáról a StorSimple-megoldás felügyeletéhez](storsimple-snapshot-manager-admin.md).

