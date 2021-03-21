---
title: Az MPIO konfigurálása a StorSimple-eszközhöz | Microsoft Docs
description: Ismerteti, hogyan kell konfigurálni a többutas I/O-t (MPIO) a Windows Server 2012 R2 rendszerű gazdagéphez csatlakoztatott StorSimple-eszközhöz.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968808"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Többutas I/O konfigurálása a StorSimple-eszközhöz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket követnie kell a többutas I/O (MPIO) szolgáltatásnak a Windows Server 2012 R2 rendszert futtató gazdagépen történő telepítéséhez és használatához, valamint egy StorSimple fizikai eszközhöz csatlakoztatva. A jelen cikkben található útmutatás csak a StorSimple 8000 Series fizikai eszközökre vonatkozik. Az MPIO jelenleg nem támogatott StorSimple Cloud Applianceon.

A Microsoft támogatja a többutas I/O (MPIO) szolgáltatást a Windows Serveren a nagy rendelkezésre állású, hibatűrő iSCSI-hálózati konfigurációk létrehozásához. Az MPIO redundáns fizikai elérési utakat használ – adapterek, kábelek és kapcsolók – a kiszolgáló és a tárolóeszköz közötti logikai útvonalak létrehozásához. Ha van összetevő-meghibásodás, a logikai elérési út sikertelen lesz, a többutas logika egy alternatív útvonalat használ az I/O-hoz, hogy az alkalmazások továbbra is hozzáférhessenek az adataihoz. Emellett a konfigurációtól függően a többutas i/o is javíthatja a teljesítményt a terhelésnek az összes útvonalon való kiegyensúlyozásával. További információ: [MPIO – áttekintés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Az MPIO áttekintése és funkciói").

A StorSimple-megoldás magas rendelkezésre állása érdekében az MPIO-t konfigurálni kell a StorSimple-eszközön. Ha az MPIO telepítve van a Windows Server 2012 R2 rendszert futtató gazdagép-kiszolgálókra, a kiszolgálók ezt követően el tudják viselni a kapcsolati, hálózati vagy illesztőfelületi hibát.

## <a name="mpio-configuration-summary"></a>MPIO-konfiguráció összegzése

Az MPIO egy választható szolgáltatás a Windows Serveren, és alapértelmezés szerint nincs telepítve. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül.

A következő lépésekkel konfigurálhatja az MPIO-t a StorSimple-eszközön:

* 1. lépés: az MPIO telepítése a Windows Server-gazdagépen
* 2. lépés: az MPIO konfigurálása a StorSimple-kötetekhez
* 3. lépés: StorSimple-kötetek csatlakoztatása a gazdagépen
* 4. lépés: az MPIO konfigurálása a magas rendelkezésre álláshoz és a terheléselosztáshoz

Az előző lépések mindegyikét a következő szakaszokban tárgyaljuk.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1. lépés: az MPIO telepítése a Windows Server-gazdagépen

A szolgáltatás Windows Server-gazdagépre történő telepítéséhez hajtsa végre az alábbi eljárást.

#### <a name="to-install-mpio-on-the-host"></a>Az MPIO telepítése a gazdagépre

1. Nyissa meg a Kiszolgálókezelő alkalmazást a Windows Server-gazdagépen. Alapértelmezés szerint a Kiszolgálókezelő akkor indul el, amikor a rendszergazdák csoport egy tagja bejelentkezik egy Windows Server 2012 R2 vagy Windows Server 2012 rendszerű számítógépre. Ha a Kiszolgálókezelő még nincs megnyitva, kattintson a **Start > Kiszolgálókezelő** elemre.
   
   ![Kiszolgálókezelő](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kattintson **a kiszolgálókezelő > irányítópult > szerepkörök és szolgáltatások hozzáadása** elemre. Ekkor elindul a **szerepkörök és szolgáltatások hozzáadása** varázsló.
   
   ![Szerepkörök és szolgáltatások hozzáadása varázsló 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. A **szerepkörök és szolgáltatások hozzáadása** varázslóban hajtsa végre a következő lépéseket:
   
   1. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
   2. A **telepítés típusának kiválasztása** lapon fogadja el a **szerepköralapú vagy a szolgáltatáson alapuló** telepítés alapértelmezett beállítását. Kattintson a **Tovább** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. A **célkiszolgáló kijelölése** lapon válassza a **kiszolgáló kijelölése a kiszolgáló készletből** lehetőséget. A gazdagép-kiszolgálót automatikusan fel kell deríteni. Kattintson a **Tovább** gombra.
   4. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
   5. A **szolgáltatások kiválasztása** lapon válassza a **többutas I/O** lehetőséget, majd kattintson a **tovább** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. A **telepítendő összetevők megerősítése** lapon erősítse meg a kijelölést, majd szükség esetén jelölje be **a célkiszolgáló automatikus újraindítása**, amint az alább látható. Kattintson az **Install** (Telepítés) gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. A telepítés befejezésekor értesítést kap. A varázsló bezárásához kattintson a **Bezárás** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2. lépés: az MPIO konfigurálása a StorSimple-kötetekhez

Az MPIO-t úgy kell konfigurálni, hogy azonosítsa a StorSimple-köteteket. Az MPIO StorSimple-kötetek felismeréséhez való konfigurálásához hajtsa végre az alábbi lépéseket.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Az MPIO konfigurálása StorSimple-kötetekhez

1. Nyissa meg az **MPIO-konfigurációt**. Kattintson a **kiszolgálókezelő > irányítópult > eszközök > MPIO** elemre.
2. Az **MPIO tulajdonságai** párbeszédpanelen válassza a **több útvonal felderítése** lapot.
3. Válassza **a támogatás hozzáadása iSCSI-eszközökhöz** lehetőséget, majd kattintson a **Hozzáadás** gombra.  
   ![Az MPIO-tulajdonságok több elérési utat derítenek fel](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Ha a rendszer kéri, indítsa újra a kiszolgálót.
5. Az **MPIO tulajdonságai** párbeszédpanelen kattintson az **MPIO-eszközök** fülre. kattintson a **Hozzáadás** gombra.
    </br>![MPIO-tulajdonságok MPIO-eszközök](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Az **MPIO-támogatás hozzáadása** párbeszédpanel **eszköz hardver azonosítója** területén adja meg az eszköz sorozatszámát. Az eszköz sorozatszámának beszerzéséhez nyissa meg a StorSimple Eszközkezelő szolgáltatását. Navigáljon az **eszközök > irányítópultra**. Az eszköz sorozatszáma megjelenik az eszköz irányítópultjának jobb oldali **gyors áttekintése** paneljén.
    </br>
    ![MPIO-támogatás hozzáadása](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Ha a rendszer kéri, indítsa újra a kiszolgálót.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3. lépés: StorSimple-kötetek csatlakoztatása a gazdagépen

Miután az MPIO konfigurálva van a Windows Serveren, a StorSimple-eszközön létrehozott kötet (ek) csatlakoztatható, és kihasználhatja a redundancia érdekében az MPIO előnyeit. Kötet csatlakoztatásához hajtsa végre az alábbi lépéseket.

#### <a name="to-mount-volumes-on-the-host"></a>Kötetek csatlakoztatása a gazdagépen

1. Nyissa meg az **iSCSI-kezdeményező tulajdonságai** ablakot a Windows Server-gazdagépen. Kattintson a **kiszolgálókezelő > irányítópult > eszközök > iSCSI-kezdeményező** elemre.
2. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen kattintson a felderítés lapra, majd a **cél portál felderítése** elemre.
3. A **cél-portál felderítése** párbeszédpanelen hajtsa végre a következő lépéseket:
   
   1. Adja meg a StorSimple-eszköz adatportjának IP-címét (például adja meg a 0. adatmennyiséget).
   2. Kattintson az **OK** gombra az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez.
     
      > [!IMPORTANT]
      > **Ha iSCSI-kapcsolatokhoz használ magánhálózati hálózatot, adja meg a magánhálózathoz csatlakozó adatport IP-címét.**
    
4. Ismételje meg a 2-3. lépést egy második hálózati adapterhez (például az 1. adategységhez) az eszközön. Ne feledje, hogy ezeket a csatolókat engedélyezni kell az iSCSI-hez. További információ: a [hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Válassza a **célok** lapot az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen. Ekkor meg kell jelennie a StorSimple-eszköz célként megadott IQN a **felderített célokban**.

   ![iSCSI-kezdeményező tulajdonságai – célok lap](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kattintson a **Kapcsolódás** elemre, és hozzon létre egy iSCSI-munkamenetet a StorSimple-eszközzel. Megjelenik a **Kapcsolódás célhelye** párbeszédpanel.
7. A **Kapcsolódás a célhelyhez** párbeszédpanelen jelölje be a **több útvonal engedélyezése** jelölőnégyzetet. Kattintson a **speciális** gombra.
8. A **Speciális beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:
   
   1. A **helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező** lehetőséget.
   2. A **kezdeményező IP** -címe legördülő listában válassza ki a gazdagép IP-címét.
   3. A **cél-portál** IP-címe legördülő listában válassza ki az eszköz adapterének IP-címét.
   4. Kattintson az **OK** gombra az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez.
9. Kattintson a **Tulajdonságok** elemre. A **Tulajdonságok** párbeszédpanelen kattintson a **munkamenet hozzáadása** lehetőségre.
10. A **Kapcsolódás a célhelyhez** párbeszédpanelen jelölje be a **több útvonal engedélyezése** jelölőnégyzetet. Kattintson a **speciális** gombra.
11. A **Speciális beállítások** párbeszédpanelen:

    1. A **helyi adapter** legördülő listában válassza a Microsoft iSCSI-kezdeményező lehetőséget.
    2. A **kezdeményező IP** -címe legördülő listában válassza ki a gazdagépnek megfelelő IP-címet. Ebben az esetben az eszközön két hálózati adaptert csatlakoztat egyetlen hálózati adapterhez a gazdagépen. Ezért ez az illesztőfelület ugyanaz, mint az első munkamenetnél.
    3. A **cél-portál IP** -címe legördülő listában válassza ki az eszközön engedélyezett második ADATILLESZTŐ IP-címét.
    4. Kattintson az **OK** gombra az iSCSI-kezdeményező tulajdonságai párbeszédpanelre való visszatéréshez. Egy második munkamenetet adott hozzá a célhoz.
12. A számítógép- **kezelés** megnyitásához navigáljon a **Kiszolgálókezelő > irányítópultra > számítógép-kezelés**. A bal oldali ablaktáblán kattintson a **Storage > Lemezkezelés** elemre. Az ezen a gazdagépen látható StorSimple eszközön létrehozott kötet a **Lemezkezelés** alatt új lemezként jelenik meg.
13. Inicializálja a lemezt, és hozzon létre egy új kötetet. A formátum folyamata alatt válasszon egy 64 KB-os blokkos méretet.
    
    ![Lemezkezelés](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. A **Lemezkezelés** területen kattintson a jobb gombbal a **lemezre** , és válassza a **Tulajdonságok** lehetőséget.
15. A StorSimple Model # # # # # **több útvonal lemez eszköz tulajdonságai** párbeszédpanelen kattintson az **MPIO** fülre.
    
    ![StorSimple 8100, több elérési úttal rendelkező lemez DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. A **DSM neve** szakaszban kattintson a **részletek** elemre, és győződjön meg arról, hogy a paraméterek az alapértelmezett paraméterekre vannak beállítva. Az alapértelmezett paraméterek a következők:
    
    * Elérési út ellenőrzése időszak = 30
    * Újrapróbálkozások száma = 3
    * OEM-eltávolítási időszak = 20
    * Újrapróbálkozási időköz = 1
    * Az elérési út ellenőrzése engedélyezve = nincs bejelölve.

> [!NOTE]
> **Ne módosítsa az alapértelmezett paramétereket.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>4. lépés: az MPIO konfigurálása a magas rendelkezésre álláshoz és a terheléselosztáshoz

A több útvonalon alapuló magas rendelkezésre állás és a terheléselosztás esetében a különböző elérési utak deklarálása érdekében a több munkamenetet manuálisan kell hozzáadni. Ha például a gazdagép két, az iSCSI-hálózathoz csatlakozó csatolóval rendelkezik, és az eszköz két, az iSCSI-hálózathoz csatlakozó csatolóval rendelkezik, akkor a megfelelő elérésiút-permutációkkal konfigurált négy munkamenetre van szükség (csak két munkamenetre lesz szükség, ha az egyes adatillesztők és a gazdagépek interfészei eltérő IP-alhálózaton találhatók, és nem irányíthatók).

**Javasoljuk, hogy az eszköz és az alkalmazás-gazdagép között legalább 8 aktív párhuzamos munkamenet legyen.** Ezt úgy érheti el, ha engedélyezi a 4 hálózati adaptert a Windows Server rendszeren. A fizikai hálózati adapterek vagy virtuális felületek hálózati virtualizálási technológiákon keresztül, a Windows Server-gazdagép hardver-vagy operációsrendszer-szintjén keresztül használhatók. Az eszköz két hálózati adapterével ez a konfiguráció 8 aktív munkamenetet eredményezhet. Ez a konfiguráció segít optimalizálni az eszköz és a felhő átviteli sebességét.

> [!IMPORTANT]
> **Javasoljuk, hogy ne keverjünk össze 1 GbE és 10 GbE hálózati adaptert. Ha két hálózati adaptert használ, mindkét csatolónak azonos típusúnak kell lennie.**

Az alábbi eljárás azt ismerteti, hogyan adhat hozzá munkameneteket, ha egy két hálózati adapterrel rendelkező StorSimple-eszköz két hálózati adapterrel rendelkező gazdagéphez csatlakozik. Ez csak 4 munkamenetet biztosít. Ugyanezt az eljárást egy olyan StorSimple-eszközzel is használhatja, amelyen két hálózati adapter van csatlakoztatva négy hálózati adapterrel. Az itt ismertetett 4 munkamenet helyett 8-at kell konfigurálnia.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Az MPIO konfigurálása a magas rendelkezésre álláshoz és a terheléselosztáshoz

1. A cél felderítésének elvégzése: az **iSCSI-kezdeményező tulajdonságai** párbeszédpanel **felderítés** lapján kattintson a **portál** felderítése elemre.
2. A **Kapcsolódás a célhelyhez** párbeszédpanelen adja meg az eszközök egyik hálózati ADAPTERÉNEK IP-címét.
3. Kattintson az **OK** gombra az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez.
4. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen válassza a **célok** fület, jelölje ki a felderített célt, majd kattintson a **Kapcsolódás** elemre. Megjelenik a **Kapcsolódás a célhoz** párbeszédpanel.
5. A **Kapcsolódás a célhelyhez** párbeszédpanelen:
   
   1. Hagyja meg a kijelölt cél alapértelmezett beállítását a **Kapcsolódás hozzáadásához** a kedvenc célok listájához. Így az eszköz a számítógép újraindításakor automatikusan megkísérli újraindítani a kapcsolódást.
   2. Jelölje be a **több útvonal engedélyezése** jelölőnégyzetet.
   3. Kattintson a **speciális** gombra.
6. A **Speciális beállítások** párbeszédpanelen:
   
   1. A **helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező** lehetőséget.
   2. A **kezdeményező IP** -címe legördülő listában válassza ki a gazdagépen (iSCSI-illesztőn) az első csatolóhoz tartozó IP-címet.
   3. A **cél-portál IP** -címe legördülő listában válassza ki az eszközön engedélyezett első ADATILLESZTŐ IP-címét.
   4. Kattintson az **OK** gombra az iSCSI-kezdeményező tulajdonságai párbeszédpanelre való visszatéréshez.
7. Kattintson a **Tulajdonságok** elemre, majd a **Tulajdonságok** párbeszédpanelen kattintson a **munkamenet hozzáadása** lehetőségre.
8. A **Kapcsolódás a célhelyhez** párbeszédpanelen jelölje be a **több útvonal engedélyezése** jelölőnégyzetet, majd kattintson a **speciális** gombra.
9. A **Speciális beállítások** párbeszédpanelen:
   
   1. A **helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező** lehetőséget.
   2. A **kezdeményező IP** -címe legördülő listában válassza ki a gazdagép második iSCSI-felületének megfelelő IP-címet.
   3. A **cél-portál IP** -címe legördülő listában válassza ki az eszközön engedélyezett második ADATILLESZTŐ IP-címét.
   4. Kattintson az **OK** gombra az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez. Ezzel hozzáadta a cél egy második munkamenetét.
10. Ismételje meg a 8-10 lépést a további munkamenetek (elérési utak) a célhoz való hozzáadásához. Ha a gazdagépen két csatoló található, és kettő az eszközön, összesen négy munkamenetet adhat hozzá.
11. A kívánt munkamenetek (elérési utak) hozzáadása után az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen válassza ki a célhelyet, majd kattintson a **Tulajdonságok** elemre. A **Tulajdonságok** párbeszédpanel munkamenetek lapján jegyezze fel a négy munkamenet-azonosítót, amely megfelel a lehetséges elérésiút-variációknak. Egy munkamenet megszakításához jelölje be a munkamenet-azonosító melletti jelölőnégyzetet, majd kattintson a **Leválasztás** elemre.
12. A munkamenetek keretében bemutatott eszközök megtekintéséhez válassza az **eszközök** fület. A kiválasztott eszköz MPIO-házirendjének konfigurálásához kattintson az **MPIO** elemre. Megjelenik az **eszköz részletei** párbeszédpanel. Az **MPIO** lapon kiválaszthatja a megfelelő **terheléselosztási házirend** -beállításokat. Megtekintheti az **aktív** vagy a **készenléti** útvonal típusát is.

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz konfigurációjának módosításához](storsimple-8000-modify-device-config.md).