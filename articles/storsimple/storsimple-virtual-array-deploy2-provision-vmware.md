---
title: StorSimple virtuális tömb kiépítése VMware-ben
description: Ez a második oktatóanyag a StorSimple virtuális tömb üzembe helyezési sorozatában virtuális eszköz kiépítését jelenti a VMware-ben.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9810a34021aa039354aad24f84aff373229c0190
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87021477"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple virtuális tömb üzembe helyezése VMware-ben
![A virtuális tömb üzembe helyezéséhez szükséges lépéseket bemutató ábra. A második lépés második része a VMware-ben kiépítve van, és ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az oktatóanyag azt ismerteti, hogyan lehet kiépíteni és csatlakozni egy StorSimple virtuális tömbhöz egy VMware ESXi 5,0, 5,5, 6,0 vagy 6,5 operációs rendszert futtató gazdagépen. Ez a cikk a StorSimple virtuális tömbök üzembe helyezésére vonatkozik a Azure Portal és a Microsoft Azure Government felhőben.

A virtuális eszközök üzembe helyezéséhez és a hozzájuk való csatlakozáshoz rendszergazdai jogosultság szükséges. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Az előfeltételek kiépítési feltételei
A virtuális eszközök VMware ESXi 5,0, 5,5, 6,0 vagy 6,5 rendszert futtató gazdagépeken való kiépítésének előfeltételei a következők.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte a [portál StorSimple virtuális tömbre való előkészítésének](storsimple-virtual-array-deploy1-portal-prep.md)lépéseit.
* Letöltötte a VMware-hez készült virtuális eszköz rendszerképét a Azure Portalról. További információkért lásd: 3. lépés: a [portál előkészítése a StorSimple Virtual Array útmutatóhoz](storsimple-virtual-array-deploy1-portal-prep.md)készült **virtuális eszköz rendszerképének letöltése** .

### <a name="for-the-storsimple-virtual-device"></a>A StorSimple virtuális eszközhöz
A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* A Hyper-V (2008 R2 vagy újabb) rendszert futtató gazdagéprendszer számára elérhető, amely az eszköz kiépítéséhez használható.
* A gazdarendszer képes elkülöníteni az alábbi erőforrásokat a virtuális eszköz üzembe helyezése érdekében:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha úgy tervezi, hogy a virtuális tömböt fájlkiszolgálóként konfigurálja, a 8 GB-nál kevesebb, mint 2 000 000 fájl. 2-4 millió fájl támogatásához 16 GB RAM szükséges.
  * Egy hálózati adapter.
  * Egy 500 GB méretű virtuális lemez a rendszeradathoz.

### <a name="for-the-network-in-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Áttekintette a StorSimple virtuális eszköz üzembe helyezésének hálózati követelményeit, és az adatközpont-hálózatot a követelményeknek megfelelően konfigurálta. 

## <a name="step-by-step-provisioning"></a>Lépésenkénti üzembe helyezés
Virtuális eszköz kiépítéséhez és csatlakoztatásához a következő lépéseket kell elvégeznie:

1. Győződjön meg arról, hogy a gazdagéprendszer elegendő erőforrással rendelkezik a virtuális eszközök minimális követelményeinek kielégítéséhez.
2. Virtuális eszköz kiépítése a hypervisorban.
3. Indítsa el a virtuális eszközt, és szerezze be az IP-címet.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>1. lépés: Győződjön meg arról, hogy a gazdagéprendszer megfelel a virtuális eszközök minimális követelményeinek
Virtuális eszköz létrehozásához a következőkre lesz szüksége:

* Hozzáférés egy VMware ESXi Server 5,0, 5,5, 6,0 vagy 6,5 operációs rendszert futtató gazdagéphez.
* VMware vSphere-ügyfél az adott rendszeren az ESXi-gazdagép kezeléséhez.

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha úgy tervezi, hogy a virtuális tömböt fájlkiszolgálóként konfigurálja, a 8 GB-nál kevesebb, mint 2 000 000 fájl. 2-4 millió fájl támogatásához 16 GB RAM szükséges.
  * Egy, a hálózatra csatlakozó hálózati adapter, amely képes a forgalmat az internetre irányítani. A minimális internetes sávszélességnek 5 Mbps-nek kell lennie az eszköz optimális működésének lehetővé tételéhez.
  * 500 GB-nyi virtuális lemez az adathoz.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>2. lépés: virtuális eszköz kiépítése a hypervisorban
A következő lépések végrehajtásával helyezzen üzembe egy virtuális eszközt a hipervizoron.

1. Másolja a virtuáliseszköz-rendszerképet a rendszerre. Ezt a virtuális rendszerképet a Azure Portal használatával töltötte le.

   1. Győződjön meg arról, hogy letöltötte a legújabb képfájlt. Ha korábban letöltötte a lemezképet, töltse le újra, hogy megbizonyosodjon róla, hogy rendelkezik a legújabb képpel. A legújabb rendszerképnek két fájlja van (egy helyett).
   2. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.

2. Jelentkezzen be az ESXi-kiszolgálóra a vSphere-ügyféllel. A virtuális gép létrehozásához rendszergazdai jogosultsággal kell rendelkeznie.

   ![Képernyőkép a vSphere-ügyfél bejelentkezési oldaláról. Az IP-cím, a Felhasználónév és a jelszó mezőkben értékek szerepelnek, és a Bejelentkezés gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Az vSphere-ügyfélben a bal oldali ablaktábla leltár területén válassza ki az ESXi-kiszolgálót.

   ![Képernyőkép a vSphere-ügyfél főoldaláról. A leltár szakaszban az ESXi-kiszolgáló ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Töltse fel a VMDK-t az ESXi-kiszolgálóra. Navigáljon a **konfiguráció** lapra a jobb oldali ablaktáblán. A **hardver** területen válassza a **tároló** elemet.

   ![A vSphere-ügyfél konfiguráció lapját ábrázoló képernyőkép. A hardver szakaszban a tárterület ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. A jobb oldali panelen a **Datastores** (Adattárak) területen válassza ki azt az adattárat, amelybe fel kívánja tölteni a VMDK-t. Az adattárnak elegendő szabad hellyel kell rendelkeznie az operációs rendszer és az adatlemezek számára.

   ![A vSphere-ügyfél Storage lapját ábrázoló képernyőkép. Megnyílik az adattárolók lap, és tartalmazza az adattárolók listáját. Egy adattár van kiválasztva.](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kattintson a jobb gombbal, és válassza a **Browse Datastore** (Adattár tallózása) lehetőséget.

   ![A kiválasztott adattár helyi menüjét ábrázoló képernyőfelvétel A tallózási adattár eleme ki van választva.](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Megjelenik a **Datastore Browser** (Adattártallózó) ablak.

   ![Képernyőfelvétel egy adattár-böngészőről. Az adattár mappái láthatók.](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Az eszköztáron kattintson az :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png"::: ikonra egy új mappa létrehozásához. Adja meg a mappa nevét, és jegyezze fel. Ezt a mappanevet fogja használni később, amikor létrehoz egy virtuális gépet (ajánlott eljárás). Kattintson az **OK** gombra.

   ![Képernyőkép egy adattár-böngészőről, amely kiemelte az új mappa ikont. Megjelenik egy párbeszédpanel, és az OK gomb ki van jelölve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Az új mappa megjelenik a **Datastore Browser** (Adattártallózó) bal oldali panelén.

   ![Képernyőkép egy adattár-böngészőről, amely az új mappában látható a mappa-hierarchiában.](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kattintson a feltöltés ikonra :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png"::: , és válassza a **fájl feltöltése** lehetőséget.

    ![Képernyőfelvétel: a feltöltés ikon helyi menüjének megjelenítése. A fájl feltöltése elemet választotta ki.](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Tallózással keresse meg a letöltött VMDK-fájlokat. Kettő fájl érhető el. Válasszon ki egy feltöltendő fájlt.

    ![Képernyőkép a mappákról és a két V M D K-fájlról. Az egyik fájl ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kattintson a **Megnyitás** gombra. Megkezdődik a VMDK-fájl feltöltése a megadott adattárra. A feltöltés több percig is eltarthat.
13. A feltöltést követően a fájl megjelenik az adattáron a létrehozott mappában.

    ![Képernyőfelvétel egy adattár-böngészőről. Az új mappa ki van emelve a mappa-hierarchiában, és a feltöltött fájl látható a mappában.](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Most töltse fel a második VMDK-fájlt is ugyanarra az adattárra.
14. Lépjen vissza a vSphere-ügyfél ablakába. Ha az ESXi-kiszolgáló ki van választva, kattintson a jobb gombbal, és válassza az **új virtuális gép** lehetőséget.

    ![Képernyőfelvétel az ESXi-kiszolgáló helyi menüjéről. Az új virtuálisgép-eleme ki van választva.](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Ekkor megjelenik az **új virtuális gép létrehozása** ablak. A **konfiguráció** lapon válassza az **Egyéni** lehetőséget. Kattintson a **Tovább** gombra.
    ![Képernyőkép az új virtuális gép létrehozása ablak konfiguráció lapján. Az egyéni beállítás ki van választva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. A **név és hely** lapon adja meg a virtuális gép nevét. Ennek a névnek meg kell egyeznie a 8. lépésben korábban megadott mappanév (ajánlott eljárás) nevével.

    ![Képernyőkép az új virtuális gép létrehozása ablak név és hely lapján. A név mező ki van töltve, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. A **tárterület** lapon válassza ki a virtuális gép kiépítéséhez használni kívánt adattárt.

    ![Képernyőkép az új virtuális gép létrehozása ablak Storage oldaláról. Egy adattár van kiválasztva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. A **virtuális gép verziója** lapon válassza a **virtuális gép verziója: 8** elemet.

    ![Képernyőkép a virtuális gép verziójának oldaláról. A virtuális gép 8-as verziója beállítás ki van választva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. A **vendég operációs rendszer** lapon válassza ki a **vendég operációs rendszert** **windowsként**. **Verzió** esetén a legördülő listából válassza a **Microsoft Windows Server 2012 (64 bites)** lehetőséget.

    ![Képernyőfelvétel a vendég operációs rendszer oldaláról a Windows kiválasztásával, a Microsoft Windows Server 2012 (64 bites) verzióra beállított verzió, majd a következő Kiemelt elem.](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. A **processzorok** lapon állítsa be a virtuális szoftvercsatornák **számát** és a **magok számát virtuális socket** -ként, hogy a **magok teljes száma** 4 (vagy több) legyen. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel a CPU-oldalról, amely egy virtuális szoftvercsatornát, négy magot és egy virtuális szoftvercsatornát, valamint négy teljes magot mutat be. A tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. A **memória** lapon válassza a RAM 8 GB (vagy több) értékét. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel a memória oldalról. A memória méretéhez 8 GB értékű érték van kitöltve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. A **hálózat** lapon határozza meg a hálózati adapterek számát. A minimális követelmény egy hálózati adapter.

    ![Képernyőfelvétel a hálózat lapról. A hálózati adapterek száma az egyikre van állítva, a tovább gomb pedig ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Az **SCSI-vezérlő** lapon fogadja el az alapértelmezett **LSI Logic sas-vezérlőt**.

    ![Képernyőkép az SCSI-vezérlő oldaláról. Az L S I Logic S A S beállítás van kiválasztva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. A **lemez kiválasztása** lapon válassza a **meglévő virtuális lemez használata** lehetőséget. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel a lemez kiválasztása lapról, a meglévő virtuális lemez használata lehetőség kiválasztásával és a Tovább gombot kiemelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. A **meglévő lemez kijelölése** lap **lemez elérési útja** területén kattintson a **Tallózás** gombra. Ekkor megnyílik a **tallózási** adattárolók párbeszédpanel. Navigáljon arra a helyre, ahová a VMDK feltöltötte. Most már csak egy fájl jelenik meg az adattárban, mert az eredetileg feltöltött két fájl össze lett fésülve. Válassza ki a fájlt, majd kattintson **az OK** gombra. Kattintson a **Tovább** gombra.

    ![Képernyőkép a meglévő lemez kiválasztása oldalról. A Tallózás gomb ki van emelve, és egy párbeszédpanel egy fájlt és egy kiemelt OK gombot tartalmaz.](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. A **Speciális beállítások** lapon fogadja el az alapértelmezett beállítást, és kattintson a **tovább** gombra.

    ![Képernyőkép a speciális beállítások lapról. A tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. A **Ready to Complete** (Befejezésre kész) oldalon tekintse át az új virtuális gépre vonatkozó beállításokat. **A befejezés előtt keresse meg a virtuális gép beállításainak szerkesztését**. Kattintson a **Folytatás** gombra.

    ![Képernyőkép a készen áll a befejezésről oldalon egy kiemelt folytatás gombbal. A virtuális gép beállításainak szerkesztése a befejezés előtt beállítás be van jelölve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. A **Virtual Machines tulajdonságok** lap **hardver** lapján keresse meg az eszköz hardverét. Válassza az **új merevlemez** lehetőséget. Kattintson a **Hozzáadás** parancsra.

    ![Képernyőkép a Virtual Machines Tulajdonságok lap hardver lapján. Az új merevlemez ki van választva a hardverek listájában. A Hozzáadás gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Ekkor megjelenik a **hardver hozzáadása** ablak. Az **eszköz típusa** lapon, **a válassza ki a hozzáadni kívánt eszköz típusát** területen válassza a **merevlemez** lehetőséget, majd kattintson a **tovább** gombra.

    ![Képernyőkép a hardver hozzáadása ablak eszköz típusa lapján. A merevlemez-eszköz ki van választva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. A **lemez kiválasztása** lapon válassza az **új virtuális lemez létrehozása** lehetőséget. Kattintson a **Tovább** gombra.

    ![Képernyőkép a lemez kiválasztása lapról. Az új virtuális lemez létrehozása lehetőség ki van választva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. A **lemez létrehozása** lapon módosítsa a **lemez méretét** 500 GB-ra (vagy akár többre). A minimális követelmény a 500 GB, így mindig nagyobb méretű lemez helyezhető üzembe. Vegye figyelembe, hogy a kiépítés után nem lehet kibontani vagy csökkenteni a lemezt. A kiépíthető lemez méretével kapcsolatos további információkért tekintse át az [ajánlott eljárásokat tartalmazó dokumentum](storsimple-ova-best-practices.md)méretezési szakaszát. A **lemez kiépítése** területen válassza a **vékony kiépítés** lehetőséget. Kattintson a **Tovább** gombra.

    ![Képernyőkép a lemez létrehozása lapról. A lemez mérete 500 GB, a vékony kiépítés lehetőség van kiválasztva, és a tovább gomb kiemelve jelenik meg.](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. A **Speciális beállítások** lapon fogadja el az alapértelmezett értéket.

    ![Képernyőkép a speciális beállítások lapról. A virtuális eszköz csomópontja az SCSI (0:0) értékre van beállítva, és a tovább gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. A **kész** lapon tekintse át a lemez beállításait. Kattintson a **Finish** (Befejezés) gombra.

    ![Képernyőkép a készen áll a teljes oldalról. A lemez beállításainak összegzése látható, a Befejezés gomb pedig ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Térjen vissza a virtuális gép tulajdonságai lapra. A rendszer új merevlemezt ad hozzá a virtuális géphez. Kattintson a **Finish** (Befejezés) gombra.

    ![Képernyőkép a virtuális gép tulajdonságai lapról. A hardver lista tartalmazza az új merevlemezt, és a Befejezés gomb ki van emelve.](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. A jobb oldali ablaktáblán válassza ki a virtuális gépet, és navigáljon az **Összefoglalás** lapra. Tekintse át a virtuális gép beállításait.

    ![Képernyőkép az vSphere-ügyfél összegzése lapról. Az új virtuális gép ki van emelve, és a hozzá tartozó erőforrások és általános tulajdonságok láthatók.](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A rendszer üzembe helyezi a virtuális gépet. A következő lépés a virtuális gép bekapcsolása és az IP-cím lekérése.

> [!NOTE]
> Javasoljuk, hogy ne telepítse a VMware-eszközöket a virtuális tömbre (a fenti kiépítés során). A VMware-eszközök telepítése egy nem támogatott konfigurációt eredményez.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>3. lépés: a virtuális eszköz elindítása és az IP-cím beszerzése
Az alábbi lépések végrehajtásával indítsa el a virtuális eszközt, és csatlakozzon hozzá.

#### <a name="to-start-the-virtual-device"></a>A virtuális eszköz indítása
1. Indítsa el a virtuális eszközt. A vSphere Configuration Manager bal oldali ablaktábláján válassza ki az eszközt, és kattintson a jobb gombbal a helyi menü kikapcsolásához. Válassza a **Power** (Főkapcsoló), majd a **Power on** (Bekapcsolás) lehetőséget. Ezzel bekapcsolja a virtuális gépet. Az állapotot a vSphere-ügyfél alsó **legutóbbi feladatok** paneljén tekintheti meg.

   ![Képernyőkép az eszköz helyi menüjéről. A kikapcsolt energiagazdálkodási pont van kiválasztva. Egy szomszédos menü látható, amelyen be van jelölve a bekapcsolás elem.](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. A telepítési feladatok elvégzése néhány percet vesz igénybe. Az eszköz futása után lépjen a **konzol** lapra. a CTRL + ALT + DELETE billentyűkombinációval jelentkezzen be az eszközre. Azt is megteheti, hogy a kurzort a konzol ablakára irányítja, és a CTRL + ALT + INSERT billentyűkombinációt is használja. Az alapértelmezett felhasználó a *StorSimpleAdmin* , az alapértelmezett jelszó pedig a *jelszó1*.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról. A jelszó mező üres.](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról. Az oldalon lévő szöveg azt jelzi, hogy a jelszót módosítani kell.](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak 3 a következő követelményeknek kell szerepelnie: kis-és nagybetűk, számok és speciális karakterek. Írja be ismét a jelszót a megerősítéséhez. A rendszer értesítést küld arról, hogy a jelszó megváltozott.

   ![Képernyőkép a vSphere-ügyfél konzoljának lapról. Az oldalon lévő szöveg azt jelzi, hogy a jelszó megváltozott.](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. A jelszó sikeres módosítása után a virtuális eszköz újraindulhat. Várjon, amíg az újraindítás befejeződik. Előfordulhat, hogy az eszköz Windows PowerShell-konzolja egy folyamatjelző sáv mellett jelenik meg.

   ![Képernyőkép, amely egy folyamatjelző sáv segítségével mutatja be a konzol ablakát. Az ablakban lévő szöveg jelzi, hogy a kezdeti beállítás folyamatban van, és megkéri a felhasználót, hogy várjon.](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Ha nem DHCP-környezetben indítja el az eszközt, az alábbi képernyő jelenik meg.

   ![Képernyőfelvétel: az eszközt leíró szöveget tartalmazó konzolablak. A parancssor beolvassa a "vezérlő" kifejezést, és készen áll a bevitelre.](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Ezután konfigurálja a hálózatot.
7. A `Get-HcsIpAddress` parancs használatával listázhatja a virtuális eszközön engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![Képernyőfelvétel: a Get-HcsIpAddress parancs kimenetét megjelenítő konzolablak. Az "Ethernet" az eszköz neveként jelenik meg.](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Erre alább láthat egy példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Képernyőfelvétel: a Get-Help Set-HcsIpAddress parancs kimenetét és a Set-HcsIpAddress parancs helyes használatát bemutató konzolablak.](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Ezt az IP-címet fogja használni a virtuális eszköz webes felhasználói felületéhez való kapcsolódáshoz, valamint a helyi telepítés és regisztráció befejezéséhez.

   ![Képernyőfelvétel: a konzol ablakának megjelenítése az eszköz szalagcímének szövegével. Ez a szöveg tartalmazza az eszköz IP-címét és URL-címét.](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Választható Ezt a lépést csak akkor hajtsa végre, ha központilag telepíti az eszközt a kormányzati felhőben. Ekkor engedélyezheti az Egyesült Államok Federal Information Processing standard (FIPS) üzemmódot az eszközön. Az FIPS 140 szabvány a bizalmas adatok védelméhez jóváhagyott titkosítási algoritmusokat határoz meg az USA szövetségi kormányzati számítógépes rendszereinek használatára.

    1. Az FIPS mód engedélyezéséhez futtassa a következő parancsmagot:

        `Enable-HcsFIPSMode`
    2. Az FIPS mód engedélyezése után indítsa újra az eszközt, hogy a titkosítási érvényesítések érvénybe lépnek.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja az eszköz FIPS üzemmódját. Az eszköz az FIPS és a nem FIPS üzemmód közötti váltás nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, egy hibaüzenet jelenik meg a szalagcím szövegében (lásd alább). Módosítania kell az eszköz konfigurációját, hogy elegendő erőforrással rendelkezzen a minimális követelmények teljesítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. A minimális konfigurációs követelményeket az [1. lépésben tekintheti meg, amely ismerteti, hogyan ellenőrizheti a gazdarendszer megfelelését a virtuális eszközökre vonatkozó minimális követelményeknek](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![Képernyőfelvétel: a konzol ablakának megjelenítése az eszköz szalagcímének szövegével. Ez a szöveg egy hibaüzenetet tartalmaz, amely egy URL-címet biztosít a probléma elhárításához.](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Ha a kezdeti konfiguráció során bármilyen más hibával szembesül a helyi webes felhasználói felülettel, tekintse meg a következő munkafolyamatokat:

* Diagnosztikai tesztek futtatása a [webes felhasználói felület beállításának hibakereséséhez](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Naplófájlok előállítása és a naplófájlok megtekintése](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Következő lépések
* [A StorSimple virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [A StorSimple virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
