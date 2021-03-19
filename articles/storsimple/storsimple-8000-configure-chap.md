---
title: A CHAP konfigurálása a StorSimple 8000 Series eszközhöz | Microsoft Docs
description: Útmutató a Challenge Handshake Authentication Protocol (CHAP) StorSimple-eszközön való konfigurálásához.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91766492"
---
# <a name="configure-chap-for-your-storsimple-device"></a>A CHAP konfigurálása a StorSimple-eszközhöz

Ez az oktatóanyag azt ismerteti, hogyan konfigurálható a CHAP a StorSimple-eszközhöz. A cikkben részletezett eljárás az StorSimple 8000 Series-eszközökre vonatkozik.

Challenge Handshake Authentication Protocol a CHAP áll. A kiszolgálók által használt hitelesítési séma a távoli ügyfelek identitásának ellenőrzésére szolgál. Az ellenőrzés egy megosztott jelszón vagy titkos kulcson alapul. A CHAP lehet egy módszer (egyirányú) vagy kölcsönös (kétirányú). Az egyik módszer a CHAP, amikor a cél hitelesíti a kezdeményezőt. A kölcsönös vagy fordított CHAP esetében a cél hitelesíti a kezdeményezőt, majd a kezdeményező hitelesíti a célt. A kezdeményező hitelesítés a célként megadott hitelesítés nélkül is megvalósítható. A cél hitelesítés azonban csak akkor valósítható meg, ha a kezdeményező hitelesítés is be van vezetve.

Ajánlott eljárásként javasoljuk, hogy a CHAP használatával fokozza az iSCSI-biztonságot.

> [!NOTE]
> Ne feledje, hogy az IPSEC jelenleg nem támogatott a StorSimple-eszközökön.

A StorSimple eszköz CHAP-beállításai a következő módokon konfigurálhatók:

* Egyirányú vagy egyirányú hitelesítés
* Kétirányú, kölcsönös vagy fordított hitelesítés

Ezekben az esetekben az eszköz és a kiszolgálói iSCSI-kezdeményező szoftver portálját be kell állítani. A konfiguráció részletes lépései a következő oktatóanyagban olvashatók.

## <a name="unidirectional-or-one-way-authentication"></a>Egyirányú vagy egyirányú hitelesítés

Az egyirányú hitelesítésnél a cél hitelesíti a kezdeményezőt. Ehhez a hitelesítéshez konfigurálnia kell a CHAP-kezdeményező beállításait a StorSimple-eszközön és az iSCSI-kezdeményező szoftveren a gazdagépen. A StorSimple-eszköz és a Windows-gazdagép részletes eljárásai a következő témakörben olvashatók.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Az eszköz konfigurálása egyirányú hitelesítéshez

1. A Azure Portal lépjen a StorSimple Eszközkezelő szolgáltatáshoz. Kattintson az **eszközök** elemre, és válassza ki azt az eszközt, amelyre a CHAP-t konfigurálni kívánja. Lépjen az **eszközbeállítások > biztonság** menüpontra. A **biztonsági beállítások** panelen kattintson a **CHAP** elemre.
   
    ![CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. A **CHAP** panelen és a **CHAP-kezdeményező** szakaszban:
   
   1. Adja meg a CHAP-kezdeményező felhasználónevét.
   2. Adja meg a CHAP-kezdeményező jelszavát.
      
      > [!IMPORTANT]
      > A CHAP-Felhasználónév 233 karakternél rövidebbnek kell lennie. A CHAP-jelszónak 12 és 16 karakter közöttinek kell lennie. Egy hosszú Felhasználónév vagy jelszó hitelesítési hibát eredményez a Windows-gazdagépen.
   
   3. Erősítse meg a jelszót.

       ![2. CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kattintson a **Mentés** gombra. Megerősítő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Egyirányú hitelesítés konfigurálása a Windows-gazdagépen
1. Indítsa el az iSCSI-kezdeményezőt a Windows-gazdagép kiszolgálóján.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban végezze el a következő lépéseket:
   
   1. Kattintson a **felderítés** fülre.
      
       ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-configure-chap/IC740944.png)
   2. Kattintson a **portál felderítése** elemre.
3. A **cél-portál felderítése** párbeszédpanelen:
   
   1. Adja meg az eszköz IP-címét.
   2. Kattintson a **speciális** gombra.
      
       ![Cél portál felderítése](./media/storsimple-configure-chap/IC740945.png)
4. A **Speciális beállítások** párbeszédpanelen:
   
   1. Jelölje be a **CHAP-bejelentkezés engedélyezése** jelölőnégyzetet.
   2. A **név** mezőben adja meg a Azure Portal CHAP-kezdeményezőhöz megadott felhasználónevet.
   3. A **cél titkos** mezőben adja meg a CHAP-kezdeményezőhöz megadott jelszót a Azure Portalban.
   4. Kattintson az **OK** gombra.
      
       ![Általános speciális beállítások](./media/storsimple-configure-chap/IC740946.png)
5. Az **iSCSI-kezdeményező tulajdonságai** ablak **Targets (célok** ) lapján az eszköz állapota **csatlakoztatottként** jelenik meg. Ha StorSimple 1200 eszközt használ, az egyes kötetek iSCSI-célként vannak csatlakoztatva. Ezért az 3-4-es lépéseket minden kötet esetében meg kell ismételni.
   
    ![Külön célként csatlakoztatott kötetek](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Ha módosítja az iSCSI-nevet, a rendszer az új iSCSI-munkamenetekhez használja az új nevet. Az új beállítások nem használhatók a meglévő munkamenetekhez, amíg ki nem jelentkezik, majd be nem jelentkezik.

További információ a CHAP konfigurálásáról a Windows-gazdagépen: [További szempontok](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Kétirányú vagy kölcsönös hitelesítés

Kétirányú hitelesítés esetén a cél hitelesíti a kezdeményezőt, majd a kezdeményező hitelesíti a célt. Ez az eljárás megköveteli, hogy a felhasználó konfigurálja a CHAP-kezdeményező beállításait, a fordított CHAP-beállításokat az eszközön, valamint az iSCSI-kezdeményező szoftvert a gazdagépen. Az alábbi eljárások ismertetik a kölcsönös hitelesítés konfigurálásának lépéseit az eszközön és a Windows-gazdagépen.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Az eszköz kölcsönös hitelesítésre való konfigurálása

1. A Azure Portal lépjen a StorSimple Eszközkezelő szolgáltatáshoz. Kattintson az **eszközök** elemre, és válassza ki azt az eszközt, amelyre a CHAP-t konfigurálni kívánja. Lépjen az **eszközbeállítások > biztonság** menüpontra. A **biztonsági beállítások** panelen kattintson a **CHAP** elemre.
   
    ![CHAP-cél](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Görgessen le ezen az oldalon, és a **CHAP-cél** szakaszban:
   
   1. Adja meg az eszköz **fordított CHAP-felhasználónevét** .
   2. Adja meg az eszköz **fordított CHAP-jelszavát** .
   3. Erősítse meg a jelszót.
3. A **CHAP-kezdeményező** szakaszban:
   
   1. Adja meg az eszköz **felhasználónevét** .
   2. Adja meg az eszköz **jelszavát** .
   3. Erősítse meg a jelszót.

       ![3. CHAP-kezdeményező](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kattintson a **Mentés** gombra. Megerősítő üzenet jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>A kétirányú hitelesítés konfigurálása a Windows-gazdagépen

1. Indítsa el az iSCSI-kezdeményezőt a Windows-gazdagép kiszolgálóján.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban kattintson a **konfiguráció** fülre.
3. Kattintson a **CHAP** elemre.
4. Az **iSCSI-kezdeményező kölcsönös CHAP-titka** párbeszédpanelen:
   
   1. Írja be a Azure Portal konfigurált **fordított CHAP-jelszót** .
   2. Kattintson az **OK** gombra.
      
       ![iSCSI-kezdeményező kölcsönös CHAP-titka](./media/storsimple-configure-chap/IC740949.png)
5. Kattintson a **célok** fülre.
6. Kattintson a **kapcsolat** gombra. 
7. A **Kapcsolódás a célhelyhez** párbeszédpanelen kattintson a **speciális** elemre.
8. A **Speciális tulajdonságok** párbeszédpanelen:
   
   1. Jelölje be a **CHAP-bejelentkezés engedélyezése** jelölőnégyzetet.
   2. A **név** mezőben adja meg a Azure Portal CHAP-kezdeményezőhöz megadott felhasználónevet.
   3. A **cél titkos** mezőben adja meg a CHAP-kezdeményezőhöz megadott jelszót a Azure Portalban.
   4. Jelölje be a **kölcsönös hitelesítés végrehajtása** jelölőnégyzetet.
      
       ![Speciális beállítások kölcsönös hitelesítése](./media/storsimple-configure-chap/IC740950.png)
   5. A CHAP-konfiguráció befejezéséhez kattintson **az OK** gombra.

További információ a CHAP konfigurálásáról a Windows-gazdagépen: [További szempontok](#additional-considerations).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

A **gyors kapcsolódási** funkció nem támogatja a CHAP-t engedélyező kapcsolatokat. Ha a CHAP engedélyezve van, ügyeljen arra, hogy a Targets ( **célok** ) lapon elérhető **Csatlakozás** gomb használatával kapcsolódjon a célhoz.

![Kapcsolódás a célhoz](./media/storsimple-configure-chap/IC740947.png)

A megjelenő **Kapcsolódás cél** párbeszédablakban jelölje be a **kapcsolat hozzáadása a kedvenc célok listájához** jelölőnégyzetet. Ez a beállítás biztosítja, hogy a számítógép minden újraindításakor megkísérelje visszaállítani az iSCSI kedvenc céljaihoz való kapcsolódást.

## <a name="errors-during-configuration"></a>Hibák a konfiguráció során

Ha a CHAP-konfiguráció nem megfelelő, akkor valószínű, hogy a **hitelesítési** hiba hibaüzenete jelenik meg.

## <a name="verification-of-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése

A következő lépések végrehajtásával ellenőrizheti, hogy a CHAP használatban van-e.

#### <a name="to-verify-your-chap-configuration"></a>A CHAP-konfiguráció ellenőrzése
1. Kattintson a **kedvenc célok** elemre.
2. Válassza ki azt a célt, amelyhez engedélyezte a hitelesítést.
3. Kattintson a **részletek** gombra.
   
    ![iSCSI-kezdeményező tulajdonságai – kedvenc célok](./media/storsimple-configure-chap/IC740951.png)
4. A **kedvenc cél részletei** párbeszédpanelen jegyezze fel a bejegyzést a **hitelesítés** mezőben. Ha a konfiguráció sikeres volt, azt a **CHAP**-nek kell megjelennie.
   
    ![Kedvenc cél részletei](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [StorSimple biztonságáról](storsimple-8000-security.md).
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

