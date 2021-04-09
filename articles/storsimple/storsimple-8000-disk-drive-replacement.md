---
title: Lemezmeghajtó cseréje StorSimple 8000 sorozatú eszközön | Microsoft Docs
description: A cikk azt ismerteti, hogyan lehet lemezmeghajtót cserélni egy elsődleges StorSimple vagy egy EBOD-bekerítésen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014873"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Lemezmeghajtó cseréje StorSimple 8000 sorozatú eszközökön

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogy miként távolítható el és cserélhető le egy Microsoft Azure StorSimple eszközön található hibás vagy sikertelen merevlemez-meghajtó. Lemezmeghajtó cseréjéhez a következőket kell tennie:

* A módosítás feloldása
* Lemezmeghajtó eltávolítása
* A kicserélt lemezmeghajtó telepítése

> [!IMPORTANT]
> A lemezmeghajtó eltávolítása és cseréje előtt tekintse át a [StorSimple hardver-összetevő cseréje](storsimple-8000-hardware-component-replacement.md)biztonsági információit.
 

## <a name="disengage-the-antitamper-lock"></a>A módosítás feloldása
Ez az eljárás azt mutatja be, hogy a StorSimple-eszközön a lemezmeghajtók cseréjekor hogyan lehet kapcsolni vagy leválasztani a nem módosítható zárolásokat. A nem módosítható zárolások a meghajtó szolgáltatójánál vannak ellátva, és a fogantyú egy kis nyíláson keresztül érhetők el. A meghajtók a zárolt pozícióra beállított zárolásokkal vannak ellátva.

#### <a name="to-unlock-the-antitamper-lock"></a>Az antimanipulálás zárolásának feloldása
1. Óvatosan illessze be a zárolási kulcsot (a Microsoft által biztosított "tamperproof" T10 csavarhúzót) a fogantyúba és a foglalatba. 
   
   Ha az antimanipulálás zárolása aktiválva van, a piros kijelző látható a rekeszben.
  
    ![Zárolt lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **1. ábra** Illetéktelen módosítások zárolása elvégezve
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Kijelző rekesze |
   | 2 |Antimanipulálás zárolása |
2. Forgassa el a kulcsot egy megegyező irányba, amíg a piros mutató nem látható a kulcs feletti rekeszben.
3. Távolítsa el a kulcsot.
   
    ![Zárolt lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **2. ábra** Zárolt lemezmeghajtó
4. A lemezmeghajtót most már el lehet távolítani.

A zárolás végrehajtásához kövesse a fordított lépéseket.

## <a name="remove-the-disk-drive"></a>Lemezmeghajtó eltávolítása
A StorSimple-eszköz támogatja a RAID 10-hez hasonló tárolóhelyek konfigurációját. Ez azt jelenti, hogy a szokásos módon működhet egy meghibásodott lemezzel, SSD-meghajtóval vagy merevlemez-meghajtóval (HDD).

> [!IMPORTANT]
> * Ha a rendszer egynél több meghibásodott lemezt tartalmaz, ne távolítson el egynél több SSD-t vagy HDD-t a rendszerből egy adott időpontban. Ez adatvesztést eredményezhet.
> * Győződjön meg arról, hogy egy helyettesítő SSD-t helyez el egy olyan tárolóhelyen, amely korábban egy SSD-t foglalt. Hasonlóképpen helyezzen egy helyettesítő HDD-t egy olyan tárolóhelyre, amely korábban egy HDD-t foglalt.
> * A Azure Portal a bővítőhelyek számozása 0 – 11. Ezért ha a portálon a 2. bővítőhely egyik lemeze nem sikerült, akkor az eszközön keresse meg a meghibásodott lemezt a bal felső sarokban található harmadik tárolóhelyen.
> 
> 

A meghajtók eltávolíthatók és lecserélhetők, amíg a rendszer működik.

#### <a name="to-remove-a-drive"></a>Meghajtó eltávolítása
1. A meghibásodott lemez azonosításához a Azure Portal válassza az eszközbeállítások **> hardver állapota lehetőséget**. Mivel egy lemez meghibásodhat az elsődleges bekerítésben és/vagy egy EBOD-házban (ha 8600 modellt használ), tekintse meg a lemezek állapotát a **megosztott összetevők** alatt, valamint a **megosztott összetevők EBOD**. A bekerítésben egy hibás lemez piros állapottal jelenik meg.
2. Keresse meg az elsődleges ház vagy a EBOD-ház elején található meghajtókat. 
3. Ha a lemez fel van oldva, folytassa a következő lépéssel. Ha a lemez zárolva van, a zárolás feloldásához [kövesse a következő eljárást:.](#disengage-the-antitamper-lock)
4. Nyomja meg a meghajtó szállítói modulján a fekete kilincset, és a meghajtó szolgáltatójánál húzza ki a meghajtót, és távolítsa el azt a ház elejéről.
   
    ![Lemezmeghajtó-leíró felszabadítása](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **3. ábra** A meghajtó leírójának felszabadítása
5. Ha a meghajtó-kezelő leírója teljes mértékben ki van terjesztve, csúsztassa a meghajtót az alvázból. 
   
    ![Lemez kicsúsztatása lemezmeghajtóból](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **4. ábra** A lemezmeghajtó kihúzása a hordozóról

## <a name="install-the-replacement-disk-drive"></a>A kicserélt lemezmeghajtó telepítése
Ha egy meghajtó meghiúsult a StorSimple-eszközön, és eltávolította azt, kövesse az alábbi eljárást egy új meghajtóra való lecseréléséhez.

#### <a name="to-insert-a-drive"></a>Meghajtó behelyezése
1. Győződjön meg arról, hogy a meghajtó hordozófrekvencia-leírója teljes mértékben ki van terjesztve, ahogy az alábbi képen is látható.
   
    ![Lemezmeghajtó kibővített leíróval](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **5. ábra** Meghajtó kibővített leíróval
2. A meghajtó szállítóját csúsztassa végig az alvázon.
   
    ![Lemez lemezre helyezése a lemezmeghajtó hordozóján](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **6. ábra**  A meghajtó szállítójának elcsúsztatása a váz számára
3. Ha beszúrta a meghajtót, zárja be a lemezmeghajtó-kezelőt, miközben továbbra is leküldi a meghajtó szállítóját az alvázra, amíg a meghajtó szolgáltatója nem kattan be egy zárolt pozícióba.
4. Használja a Microsoft által biztosított zárolási kulcsot (tamperproof TORX csavarhúzó) a hordozófrekvencia a helyükre való biztonságossá tételéhez úgy, hogy a zárolási csavar egy negyedévre van bekapcsolva.
5. Ellenőrizze, hogy a csere sikeres volt-e, és hogy a meghajtó működőképes-e. Nyissa meg a Azure Portal, és navigáljon az **eszközbeállítások**  >  **hardver állapota** menüponthoz. A **megosztott összetevők** vagy a **megosztott összetevők EBOD** a meghajtó állapota legyen zöld, ami azt jelzi, hogy kifogástalan.

   
   > [!NOTE]
   > Több órát is igénybe vehet, amíg a lemez állapota a csere után zöldre vált.
  
## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevők cseréjéről](storsimple-8000-hardware-component-replacement.md).

