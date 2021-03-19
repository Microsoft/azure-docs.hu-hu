---
title: StorSimple virtuális tömb kiépítése a Hyper-V-ben | Microsoft Docs
description: A StorSimple virtuális tömb üzembe helyezésének második oktatóanyaga egy virtuális tömb kiépítését foglalja magában a Hyper-V-ben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87070598"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple virtuális tömb üzembe helyezése – létesítés a Hyper-V-ben
![A virtuális tömb üzembe helyezéséhez szükséges lépéseket bemutató ábra. A második lépés első része a Hyper-V üzembe helyezése, és ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az oktatóanyag azt ismerteti, hogyan lehet StorSimple virtuális tömböt létrehozni a Hyper-V-t futtató gazdagépeken Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. Ez a cikk a StorSimple virtuális tömbök üzembe helyezésére vonatkozik Azure Portal és Microsoft Azure Government felhőben.

A virtuális tömb kiépítéséhez és konfigurálásához rendszergazdai jogosultság szükséges. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Az előfeltételek kiépítési feltételei
Itt megtalálhatja a virtuális tömb üzembe helyezésének előfeltételeit a Hyper-V-t futtató gazdagépen a Windows Server 2012 R2, a Windows Server 2012 vagy a Windows Server 2008 R2 rendszeren.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte a [portál StorSimple virtuális tömbre való előkészítésének](storsimple-virtual-array-deploy1-portal-prep.md)lépéseit.
* Letöltötte a Hyper-V virtuális tömb rendszerképét a Azure Portalról. További információt a 3. **lépés: a** [portál előkészítése a StorSimple virtuális](storsimple-virtual-array-deploy1-portal-prep.md)tömbhöz című témakörben talál.

  > [!IMPORTANT]
  > A StorSimple virtuális tömbön futó szoftver csak a StorSimple Eszközkezelő szolgáltatással használható.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz
Virtuális tömb üzembe helyezése előtt győződjön meg a következőket:

* A Hyper-V-t futtató, Windows Server 2008 R2 vagy újabb rendszerű gazdagéprendszer számára elérhető, amely az eszköz kiépítéséhez használható.
* A gazdagéprendszer a következő erőforrásokat tudja kiépíteni a virtuális tömb kiépítéséhez:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha úgy tervezi, hogy a virtuális tömböt fájlkiszolgálóként konfigurálja, a 8 GB-nál kevesebb, mint 2 000 000 fájl. 2-4 millió fájl támogatásához 16 GB RAM szükséges.
  * Egy hálózati adapter.
  * 500 GB-nyi virtuális lemez az adathoz.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt elkezdené, tekintse át a hálózati követelményeket a StorSimple virtuális tömb üzembe helyezéséhez és az adatközpont-hálózat megfelelő konfigurálásához. További információ: [StorSimple Virtual Array Network követelmények](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Lépésenkénti üzembe helyezés
Virtuális tömb kiépítéséhez és az ahhoz való kapcsolódáshoz a következő lépéseket kell elvégeznie:

1. Győződjön meg arról, hogy a gazdagéprendszer elegendő erőforrással rendelkezik a virtuális tömb minimális követelményeinek kielégítéséhez.
2. Hozzon létre egy virtuális tömböt a hypervisorban.
3. Indítsa el a virtuális tömböt, és szerezze be az IP-címet.

Ezeket a lépéseket az alábbi részekben ismertetjük.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>1. lépés: Győződjön meg arról, hogy a gazdagéprendszer megfelel a virtuális tömb minimális követelményeinek
Virtuális tömb létrehozásához a következők szükségesek:

* A Windows Server 2012 R2, a Windows Server 2012 vagy a Windows Server 2008 R2 SP1 rendszerre telepített Hyper-V szerepkör.
* Egy, a gazdagépre csatlakoztatott Microsoft Windows-ügyfélen futó Microsoft Hyper-V-kezelőre.

Győződjön meg arról, hogy a virtuális tömböt létrehozó mögöttes hardver (gazdarendszer) képes a következő erőforrásokat a virtuális tömbhöz hozzárendelni:

* Legalább 4 mag.
* Legalább 8 GB RAM. Ha úgy tervezi, hogy a virtuális tömböt fájlkiszolgálóként konfigurálja, a 8 GB-nál kevesebb, mint 2 000 000 fájl. 2-4 millió fájl támogatásához 16 GB RAM szükséges.
* Egy hálózati adapter.
* Egy 500 GB méretű virtuális lemez a rendszeradathoz.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>2. lépés: virtuális tömb kiépítése a hypervisorban
A következő lépések végrehajtásával helyezzen üzembe egy eszközt a hipervizoron.

#### <a name="to-provision-a-virtual-array"></a>Virtuális tömb kiépítése
1. A Windows Server-gazdagépen másolja a virtuális tömb rendszerképét egy helyi meghajtóra. Letöltötte ezt a rendszerképet (VHD vagy VHDX) a Azure Portalon keresztül. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.
2. Nyissa meg a **Kiszolgálókezelőt**. A jobb felső sarokban kattintson az **Eszközök** gombra, és válassza a **Hyper-V kezelője** lehetőséget.

   ![Képernyőkép a Kiszolgálókezelő kibővített eszközök menüjéről, amely a Hyper-V kezelője elemre van kijelölve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Ha a Windows Server 2008 R2 rendszert futtatja, nyissa meg a Hyper-V kezelőjét. A Kiszolgálókezelőben kattintson a **szerepkörök > Hyper-v > Hyper-v kezelője** elemre.
3. A **Hyper-V kezelője** hatókörpanelén kattintson jobb gombbal a rendszercsomópontra, majd a megnyíló helyi menüben kattintson az **Új** > **Virtuális gép** elemre.

   ![Képernyőkép a rendszercsomópont helyi menüjéről a Hyper-V kezelőjéről, amely az új és a virtuálisgép-elemek kiemelésére van kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Az Új virtuális gép varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
5. A **név és hely megadása** lapon adja meg a virtuális tömb **nevét** . Kattintson a **Tovább** gombra.

   ![Képernyőfelvétel: a név és hely megadása lap, amely a név mező kitöltését és a tovább gomb kiemelését jeleníti meg.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. A **generáció megadása** lapon válassza ki az eszköz rendszerképének típusát, majd kattintson a **tovább** gombra. Ez a lap nem jelenik meg, ha a Windows Server 2008 R2 rendszert használja.

   * Válassza a **2. generáció** lehetőséget, ha a Windows Server 2012-es vagy újabb verziójához letöltötte a. vhdx-rendszerképet.
   * Válassza az **1. generáció** lehetőséget, ha letöltött egy. vhd rendszerképet a Windows Server 2008 R2 vagy újabb verzióhoz.

   ![Képernyőfelvétel: a generáció megadása lap, amely a 2. generációs lehetőséget és a Tovább gombot jelölte ki.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. A **Memória hozzárendelése** lapon adjon meg egy legalább **8192 MB** méretű **indítási memóriát**, ne engedélyezze a dinamikus memóriát, majd kattintson a **Tovább** gombra.

   ![Képernyőfelvétel: a memória kiosztása oldal, amely a 8192 értéket mutatja az indítási memória mezőben. A tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. A **Hálózatkezelés beállítása** lapon adja meg az internetre csatlakoztatott virtuális kapcsolót, majd kattintson a **Tovább** gombra.

   ![Képernyőkép a hálózatkezelés beállítása oldalról, amelyen a Hyper V virtuális kapcsoló elem van kiválasztva a kapcsolat mezőben, és a tovább gomb kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. A **virtuális merevlemez összekapcsolása** lapon válassza a **meglévő virtuális merevlemez használata** lehetőséget, adja meg a virtuális tömb rendszerképének helyét (. vhdx vagy. vhd), majd kattintson a **tovább** gombra.

   ![Képernyőkép a virtuális merevlemez összekapcsolása oldalról, amely egy meglévő virtuális merevlemezt használ, egy helyet, és a Tovább gombot kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Tekintse át az **összefoglalást**, majd kattintson a **Befejezés** gombra a virtuális gép létrehozásához.

    ![Képernyőfelvétel: az új virtuális gép varázsló befejezése lap, amely a virtuális gép leírását tartalmazza. A Befejezés gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 4 magra van szükség ahhoz, hogy meg tudjon felelni a minimális követelményeknek. A 4 virtuális processzor hozzáadásához válassza ki a gazdarendszert a **Hyper-V kezelő** ablakában. A jobb oldali panel **Virtuális gépek** listájában keresse meg az imént létrehozott virtuális gépet. Jelölje ki, kattintson a jobb gombbal a gép nevére, majd válassza a **Beállítások** elemet.

    ![Képernyőkép a Hyper-V kezelő ablakáról. A listában egy gép van kiválasztva, és a beállítások elem ki van emelve a gép helyi menüjében.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. A **Beállítások** lap bal oldali panelén kattintson a **Processzor** lehetőségre. A jobb oldali panelen állítsa a **virtuális processzorok számát** 4-re (vagy többre). Kattintson az **Alkalmaz** gombra.

    ![Képernyőkép a beállítások lapról a felemelt processzor elemnél, a virtuális processzorok száma négy értékre, az alkalmaz gomb kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. A minimális követelmények teljesítéséhez hozzá kell adnia egy 500 GB-os virtuális adatlemezt is. A **Beállítások** lapon:

    1. A bal oldali panelen válassza az **SCSI-vezérlő** lehetőséget.
    2. A jobb oldali panelen válassza a **Merevlemez** lehetőséget, és kattintson a **Hozzáadás** gombra.

    ![Képernyőfelvétel a beállítások lapról az SCSI-vezérlő elem kiválasztásával, a merevlemez elem kiválasztásával, valamint a Hozzáadás gomb kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. A **Merevlemez** lapon válassza a **Virtuális merevlemez** lehetőséget, és kattintson az **Új** gombra. Megnyílik az **Új virtuális merevlemez varázsló**.

    ![Képernyőkép a beállítások lapról a merevlemez elem kiválasztásával, a virtuális merevlemez lehetőség kiválasztásával, és az új gomb kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Az Új virtuális merevlemez varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
16. A **Lemezformátum kiválasztása** lapon fogadja el az alapértelmezett **VHDX** formátumot. Kattintson a **Tovább** gombra. Ez a képernyő nem jelenik meg, ha a Windows Server 2008 R2 fut.

    ![Képernyőkép a lemez formázása lap kiválasztása lapon a VHDX beállítás kiválasztásával és a következő gomb kiemelésével.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. A **Lemeztípus kiválasztása** lapon állítsa a virtuális merevlemez típusát a **Dinamikusan bővülő** típusra (ajánlott). A **Rögzített méretű** lemez is működhet, de előfordulhat, hogy túl sokat kellene várnia. Azt javasoljuk, hogy ne alkalmazza a **Különbség** beállítást. Kattintson a **Tovább** gombra. A Windows Server 2012 R2 és a Windows Server 2012 rendszerben a **dinamikusan bővült** az alapértelmezett beállítás, míg a windows Server 2008 R2 rendszerben az alapértelmezett érték a **rögzített méret**.

    ![Képernyőkép a lemez típusának kiválasztása lapon a dinamikusan bővülő lehetőség kiválasztásával és a következő gomb kiemelésével.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. A **Név és hely megadása** lapon adja meg az adatlemez **nevét** és **helyét** (ez utóbbit tallózva is megkeresheti). Kattintson a **Tovább** gombra.

    ![Képernyőkép: a név és hely mezőkben szereplő értékek a név és hely megadása oldalon. A tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. A **lemez konfigurálása** lapon válassza az **új üres virtuális merevlemez létrehozása** lehetőséget, és adja meg a méretet **500 GB** -ként (vagy több). A minimális követelmény a 500 GB, így mindig nagyobb méretű lemez helyezhető üzembe. Vegye figyelembe, hogy a kiépítés után nem lehet kibontani vagy csökkenteni a lemezt. A kiépíthető lemez méretével kapcsolatos további információkért tekintse át az [ajánlott eljárásokat tartalmazó dokumentum](storsimple-ova-best-practices.md)méretezési szakaszát. Kattintson a **Tovább** gombra.

    ![Képernyőkép a lemez konfigurálása oldalról, és hozzon létre egy új, üres virtuális merevlemezt, a 500 értékre állítva, a Tovább gombot kiemelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Az **Összefoglalás** lapon tekintse át a virtuális adatlemez adatait, és ha elégedett, kattintson a **Befejezés** gombra a lemez létrehozásához. A varázsló bezáródik, és a rendszer hozzáadja a virtuális merevlemezt a géphez.

    ![Képernyőkép az új virtuális merevlemez varázsló befejezéséről a virtuális merevlemez leírását bemutató oldalon. A Befejezés gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Lépjen vissza a **Beállítások** lapra. Az **OK** gombra kattintva zárja be a **Beállítások** lapot, és lépjen vissza a Hyper-V kezelőjének ablakára.

    ![Képernyőfelvétel a beállítások lapról. Az OK gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>3. lépés: a virtuális tömb elindítása és az IP-cím beszerzése
A következő lépések végrehajtásával indítsa el a virtuális tömböt, és kapcsolódjon hozzá.

#### <a name="to-start-the-virtual-array"></a>A virtuális tömb elindítása
1. Indítsa el a virtuális tömböt.

   ![Képernyőkép a Hyper-V kezelő ablakáról. Az új tömb ki van választva a listában, és a Start elem ki van emelve a tömb helyi menüjében.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Amikor az eszköz már fut, jelölje ki, kattintson rá a jobb gombbal, és válassza a **Csatlakozás** lehetőséget.

   ![Képernyőkép a Hyper-V kezelő ablakáról. Az új tömb ki van választva, az állapota fut, és a csatlakozási elem ki van emelve a helyi menüjében.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Előfordulhat, hogy várnia kell 5-10 percet, amíg az eszköz készen áll. A folyamat előrehaladtát egy állapotüzenet jelzi a konzolon. Ha az eszköz kész, lépjen a **Művelet** területre. Nyomja meg a gombot `Ctrl + Alt + Delete` a virtuális tömbbe való bejelentkezéshez. Az alapértelmezett felhasználó a *StorSimpleAdmin* , az alapértelmezett jelszó pedig a *jelszó1*.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról, amely a bejelentkezési mezőben meghatározatlan karaktereket tartalmaz.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról. Az oldalon lévő szöveg azt jelzi, hogy a jelszót módosítani kell.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak a következő négy típusból legalább hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek. Írja be ismét a jelszót a megerősítéséhez. A rendszer értesíti, hogy a jelszó megváltozott.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról. A lap szövege azt jelzi, hogy a jelszó megváltozott.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. A jelszó sikeres módosítása után a virtuális tömb újraindulhat. Várjon, amíg az eszköz elindul.

   ![Képernyőkép a fő StorSimpleAdmin oldalról. A lapon lévő szöveg arra kéri a felhasználót, hogy várjon a rendszeresemény-értesítési szolgáltatásra.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Megjelenik az eszköz Windows PowerShell-konzolja és egy folyamatjelző.

   ![Képernyőkép, amely egy folyamatjelző sáv segítségével mutatja be a konzol ablakát. Az ablakban lévő szöveg jelzi, hogy a kezdeti beállítás folyamatban van, és megkéri a felhasználót, hogy várjon.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Ha nem DHCP-környezetben indítja el az eszközt, az alábbi képernyő jelenik meg.

   ![Képernyőfelvétel: az eszközt leíró szöveget tartalmazó konzolablak. A parancssor beolvassa a "vezérlő" kifejezést, és készen áll a bevitelre.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Ezután konfigurálja a hálózatot.
7. A `Get-HcsIpAddress` parancs használatával listázhatja a virtuális tömbön engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![Képernyőfelvétel: a Get-HcsIpAddress parancs kimenetét megjelenítő konzolablak. Az "Ethernet" az eszköz neveként jelenik meg.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Lásd a következő példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Képernyőfelvétel: a Get-Help Set-HcsIpAddress parancs kimenetét és a Set-HcsIpAddress parancs helyes használatát bemutató konzolablak.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Ezzel az IP-címmel csatlakozhat a virtuális tömb webes felhasználói felületéhez, és elvégezheti a helyi telepítést és regisztrációt.

   ![Képernyőfelvétel: a konzol ablakának megjelenítése az eszköz szalagcímének szövegével. Ez a szöveg tartalmazza az eszköz IP-címét és URL-címét.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Választható Ezt a lépést csak akkor hajtsa végre, ha központilag telepíti az eszközt a kormányzati felhőben. Ekkor engedélyezheti az Egyesült Államok Federal Information Processing standard (FIPS) üzemmódot az eszközön. Az FIPS 140 szabvány a bizalmas adatok védelméhez jóváhagyott titkosítási algoritmusokat határoz meg az USA szövetségi kormányzati számítógépes rendszereinek használatára.

    1. Az FIPS mód engedélyezéséhez futtassa a következő parancsmagot:

        `Enable-HcsFIPSMode`
    2. Az FIPS mód engedélyezése után indítsa újra az eszközt, hogy a titkosítási érvényesítések érvénybe lépnek.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja az eszköz FIPS üzemmódját. Az eszköz az FIPS és a nem FIPS üzemmód közötti váltás nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, a következő hibaüzenet jelenik meg a szalagcím szövege (lásd alább). Módosítsa az eszköz konfigurációját, hogy a gép elegendő erőforrással rendelkezzen a minimális követelmények kielégítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. Tekintse meg az 1. lépés minimális konfigurációs követelményeit: Ellenőrizze, hogy a gazdagéprendszer megfelel-e a virtuális tömb minimális követelményeinek.

![Képernyőfelvétel: a konzol ablakának megjelenítése az eszköz szalagcímének szövegével. Ez a szöveg egy hibaüzenetet tartalmaz, amely egy URL-címet biztosít a probléma elhárításához.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Ha a kezdeti konfiguráció során bármilyen más hibával szembesül a helyi webes felhasználói felülettel, tekintse meg a következő munkafolyamatokat:

* Diagnosztikai tesztek futtatása a [webes felhasználói felület beállításának hibakereséséhez](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Naplófájlok előállítása és a naplófájlok megtekintése](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Következő lépések
* [A StorSimple virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [A StorSimple virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
