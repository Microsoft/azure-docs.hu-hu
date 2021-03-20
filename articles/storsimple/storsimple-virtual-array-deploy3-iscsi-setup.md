---
title: Virtuális tömb iSCSI-kiszolgálójának Microsoft Azure StorSimple beállítása | Microsoft Docs
description: Útmutatás a kezdeti beállítás végrehajtásához, a StorSimple iSCSI-kiszolgáló regisztrálásához és az eszköz telepítésének befejezéséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956551"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple virtuális tömb üzembe helyezése – iSCSI-kiszolgálóként való beállítása Azure Portalon keresztül

![iSCSI beállítási folyamat](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az üzembe helyezési oktatóanyag a Microsoft Azure StorSimple virtuális tömbre vonatkozik. Ez az oktatóanyag leírja, hogyan végezheti el a kezdeti telepítést, regisztrálni a StorSimple iSCSI-kiszolgálót, az eszköz beállításának befejezését, majd a kötetek létrehozását, csatlakoztatását, inicializálását és formázását az iSCSI-kiszolgálóként konfigurált StorSimple virtuális tömbben. 

Az itt ismertetett eljárások elvégzése körülbelül 30 percet vesz igénybe. A cikkben közzétett információk csak a StorSimple virtuális tömbökre vonatkoznak.

## <a name="setup-prerequisites"></a>Telepítési előfeltételek

A StorSimple virtuális tömb konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Létrehozta a virtuális tömböt, és csatlakoztatta azt a következő témakörben leírtak szerint: virtuális tömb [üzembe helyezése a Hyper-V](./storsimple-virtual-array-deploy2-provision-hyperv.md) -ben vagy a [StorSimple virtuális tömb üzembe helyezése – virtuális tömb kiépítése a VMware](storsimple-virtual-array-deploy2-provision-vmware.md)-ben – StorSimple.
* A StorSimple virtuális tömbök kezeléséhez létrehozott Eszközkezelő StorSimple-szolgáltatás regisztrációs kulcsával rendelkezik. További információ: 2. **lépés: a szolgáltatás regisztrációs kulcsának beszerzése** a [StorSimple virtuális tömb üzembe helyezése – felkészülés a portálra](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Ha ez a második vagy az azt követő virtuális tömb, amelyet egy meglévő StorSimple Eszközkezelő szolgáltatáshoz regisztrál, rendelkeznie kell a szolgáltatás adattitkosítási kulcsával. Ez a kulcs akkor jött létre, amikor az első eszközt sikeresen regisztrálták ezzel a szolgáltatással. Ha elvesztette a kulcsot, tekintse meg **a szolgáltatás adattitkosítási kulcsának beszerzése** a [webes felhasználói felületen a StorSimple virtuális tömb felügyeletéhez](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)című témakört.

## <a name="step-by-step-setup"></a>Lépésenkénti beállítás

A StorSimple virtuális tömb beállításához és konfigurálásához használja a következő lépésenkénti útmutatót:

* [1. lépés: a helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* 2. lépés: a szükséges eszköz telepítésének befejezése
* [3. lépés: kötet hozzáadása](#step-3-add-a-volume)
* [4. lépés: kötet csatlakoztatása, inicializálása és formázása](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: a helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása

#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejezése és az eszköz regisztrálása

1. Nyisson meg egy böngészőablakot. A webes felhasználói felület típusához való kapcsolódáshoz:
   
    `https://<ip-address of network interface>`
   
    Használja az előző lépésben feljegyzett kapcsolatok URL-címét. A rendszer hibaüzenetet küld arról, hogy probléma merült fel a webhely biztonsági tanúsítványával kapcsolatban. Kattintson **a folytatás erre a weblapra** gombra.
   
    ![biztonsági tanúsítvány hibája](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Jelentkezzen be a virtuális eszköz webes felhasználói felületére **StorSimpleAdmin** néven. Adja meg a 3. lépésben módosított eszköz rendszergazdai jelszavát: indítsa el a virtuális eszközt a [StorSimple virtuális tömb üzembe helyezése – virtuális eszköz kiépítése a Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) -ben vagy a [StorSimple virtuális tömb üzembe helyezése – virtuális eszköz kiépítése a VMware](storsimple-virtual-array-deploy2-provision-vmware.md)-ben.
   
    ![Bejelentkezési oldal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Ekkor megnyílik a **Kezdőlap** . Ez a lap a virtuális eszköz StorSimple Eszközkezelő szolgáltatással való konfigurálásához és regisztrálásához szükséges különböző beállításokat ismerteti. Vegye figyelembe, hogy a **hálózati beállítások**, a **webproxy beállításai** és az **időbeállítások** megadása nem kötelező. Az egyetlen szükséges beállítás az **eszközbeállítások** és a **felhő beállításai**.
   
    ![Kezdőlap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. A hálózati **adapterek** **hálózati beállítások** lapján a 0. adat automatikusan be lesz állítva. Az egyes hálózati adapterek alapértelmezés szerint az IP-címek automatikus beszerzésére (DHCP) vannak beállítva. Ezért a rendszer automatikusan hozzárendeli az IP-címet, az alhálózatot és az átjárót (az IPv4 és az IPv6 esetében egyaránt).
   
    Ahogy azt tervezi, hogy az eszközt iSCSI-kiszolgálóként telepíti (a blokkos tárolás kiépítéséhez), javasoljuk, hogy tiltsa le az **IP-cím automatikus beolvasása** beállítást, és konfigurálja a statikus IP-címeket.
   
    ![Hálózati beállítások lap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Ha egynél több hálózati adaptert adott hozzá az eszköz kiépítés során, itt konfigurálhatja őket. Megjegyzés: a hálózati adaptert IPv4-ként vagy IPv4-ként vagy IPv6-ként is konfigurálhatja. Az IPv6-konfigurációk nem támogatottak.
5. A DNS-kiszolgálókat azért kell használni, mert amikor az eszköz megpróbál kommunikálni a felhőalapú tároló-szolgáltatókkal, vagy ha a fájl neve fájlkiszolgálóként van konfigurálva, akkor az eszköz név szerinti feloldására van szükség. A **hálózati beállítások** lapon a **DNS-kiszolgálók** területen:
   
   1. A rendszer automatikusan konfigurálja az elsődleges és másodlagos DNS-kiszolgálót. Ha statikus IP-címek konfigurálását választja, megadhatja a DNS-kiszolgálókat. A magas rendelkezésre állás érdekében javasoljuk, hogy állítson be egy elsődleges és egy másodlagos DNS-kiszolgálót.
   2. Kattintson az **Alkalmaz** gombra. Ez a művelet a hálózati beállításokat fogja alkalmazni és érvényesíteni.
6. Az **eszközbeállítások** lapon:
   
   1. Rendeljen egyedi **nevet** az eszközhöz. Ez a név 1-15 karakterből állhat, és tartalmazhat betűt, számot és kötőjelet.
   2. Kattintson az **iSCSI-kiszolgáló** ikonra ![ iSCSI-kiszolgáló ikonra a ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) létrehozandó eszköz **típusához** . Az iSCSI-kiszolgálók lehetővé teszik a blokkoló tárolók kiépítését.
   3. Itt adhatja meg, hogy szeretné-e, hogy az eszköz tartományhoz legyen csatlakoztatva. Ha az eszköz egy iSCSI-kiszolgáló, akkor a tartományhoz való csatlakozás nem kötelező. Ha úgy dönt, hogy nem csatlakoztatja az iSCSI-kiszolgálót egy tartományhoz, kattintson az **alkalmaz** gombra, várjon, amíg a beállítások érvénybe lépnek, majd ugorjon a következő lépésre.
      
       Ha az eszközt tartományhoz kívánja csatlakoztatni. Adja meg a **tartománynevet**, majd kattintson az **alkalmaz** gombra.
      
      > [!NOTE]
      > Ha az iSCSI-kiszolgálót egy tartományhoz csatlakoztatja, győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) van Microsoft Azure Active Directory, és nincs alkalmazva csoportházirend-objektum (GPO).
      > 
      > 
   4. Ekkor megjelenik egy párbeszédpanel. Adja meg a tartománya hitelesítő adatait a megadott formátumban. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). A tartományi hitelesítő adatok ellenőrzése megtörténik. Hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
      
       ![hitelesítő adatok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kattintson az **Alkalmaz** gombra. Ez az eszköz beállításait fogja alkalmazni és érvényesíteni.
7. (Opcionálisan) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani.
   
    ![webproxy konfigurálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    A **webproxy** lapon:
   
   1. Adja meg a **webproxy URL** -címét a következő formátumban: *http: \/ /Host-IP cím* vagy *FQDN: portszám*. Vegye figyelembe, hogy a HTTPS URL-címek nem támogatottak.
   2. A **hitelesítést** a **Basic** vagy a **none** értékre kell beállítani.
   3. Ha hitelesítést használ, meg kell adnia egy **felhasználónevet** és egy **jelszót** is.
   4. Kattintson az **Alkalmaz** gombra. Ez a művelet érvényesíti és alkalmazza a konfigurált webproxy-beállításokat.
8. (Opcionálisan) konfigurálhatja az eszköz időbeállítását, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálókat. Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.
   
    ![Idő beállításai](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Az **időbeállítások** lapon:
   
   1. A legördülő listában válassza ki az **időzónát** azon földrajzi hely alapján, amelyben az eszközt üzembe helyezi. Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   2. Adja meg az eszköz **Elsődleges NTP-kiszolgálóját** , vagy fogadja el az Time.Windows.com alapértelmezett értékét. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
   3. Opcionálisan megadhat egy **másodlagos NTP-kiszolgálót** az eszközhöz.
   4. Kattintson az **Alkalmaz** gombra. Ezzel a művelettel érvényesítheti és alkalmazhatja a beállított időértékeket.
9. Adja meg az eszközhöz tartozó felhő beállításait. Ebben a lépésben elvégzi a helyi eszköz konfigurációját, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatásával.
   
   1. Adja meg a **szolgáltatás regisztrációs kulcsát** , amelyet a **2. lépésben kapott: a szolgáltatás regisztrációs kulcsának beszerzése** a [StorSimple virtuális tömb üzembe helyezése – a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Ha nem ez az első eszköz, amelyet a szolgáltatással regisztrál, meg kell adnia a **szolgáltatás adattitkosítási kulcsát**. Erre a kulcsra a szolgáltatás regisztrációs kulcsának használatakor van szükség további eszközök regisztrálásához a StorSimple Eszközkezelő szolgáltatással. További információt a [szolgáltatás adattitkosítási kulcsának beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a helyi webes felhasználói felületen című témakörben talál.
   3. Kattintson a **Regisztrálás** parancsra. Ekkor a rendszer újraindítja az eszközt. Előfordulhat, hogy várnia kell 2-3 percet, mielőtt az eszköz regisztrálása sikeresen megtörtént. Az eszköz újraindítása után megnyílik a bejelentkezési oldal.
      
      ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Lépjen vissza az Azure Portalra.
11. Navigáljon a szolgáltatás **eszközök** paneljére. Ha sok erőforrással rendelkezik, kattintson a **minden erőforrás** lehetőségre, kattintson a szolgáltatás nevére (keresse meg, ha szükséges), majd kattintson az **eszközök** elemre.
12. Az **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz az állapot megkeresésével. Az eszköznek **Beállításra kész** állapotúnak kell lennie.
    
    ![Eszköz üzembe helyezése](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>2. lépés: az eszköz konfigurálása iSCSI-kiszolgálóként

A szükséges eszköz telepítésének befejezéséhez hajtsa végre a következő lépéseket a Azure Portal.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Az eszköz konfigurálása iSCSI-kiszolgálóként

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd lépjen a **felügyeleti > eszközökre**. Válassza ki az imént létrehozott eszközt az **eszközök** panelen. Ez az eszköz úgy jelenik **meg, hogy készen áll a beállításra**.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet, amely jelzi, hogy az eszköz készen áll a telepítésre.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kattintson a **Konfigurálás** elemre az eszköz parancssáv-sávján. Ekkor megnyílik a **configure (Konfigurálás** ) panel. A **configure (Konfigurálás** ) panelen tegye a következőket:
   
   * Az iSCSI-kiszolgáló neve automatikusan ki lesz töltve.
   * Győződjön meg arról, hogy a Felhőbeli tároló titkosítása **engedélyezve** van. Ez biztosítja, hogy az eszközről a felhőbe továbbított adatok titkosítva legyenek.
   * Válasszon egy 32 karakterből álló titkosítási kulcsot, és jegyezze fel egy kulcskezelő alkalmazásban későbbi használatra.
   * Válassza ki az eszközhöz használni kívánt Storage-fiókot. Ebben az előfizetésben választhat egy meglévő Storage-fiókot, vagy a **Hozzáadás** gombra kattintva kiválaszthatja a fiókot egy másik előfizetésből.
     
     ![Eszköz konfigurálása iSCSI-kiszolgálóként 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kattintson a **Konfigurálás** elemre az iSCSI-kiszolgáló beállításának befejezéséhez.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. A rendszer értesítést küld arról, hogy az iSCSI-kiszolgáló létrehozása folyamatban van. Az iSCSI-kiszolgáló sikeres létrehozása után a rendszer frissíti az **eszközök** panelt, és a megfelelő eszköz állapota **online** állapotú.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>3. lépés: kötet hozzáadása

1. Az **eszközök** panelen válassza ki az imént iSCSI-kiszolgálóként konfigurált eszközt. Kattintson a **...** (másik lehetőségre a jobb gombbal kattintva ebben a sorban), majd a helyi menüben válassza a **kötet hozzáadása** elemet. A menüsávban kattintson a **+ kötet hozzáadása** lehetőségre. Ekkor megnyílik a **kötet hozzáadása** panel.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. A **kötet hozzáadása** panelen tegye a következőket:
   
   * A **kötet neve** mezőben adjon meg egy egyedi nevet a kötetnek. A névnek olyan sztringnek kell lennie, amely 3 – 127 karaktert tartalmaz.
   * A **típus** legördülő listában adja meg, hogy létre kell-e hozni egy **rétegű** vagy **helyileg rögzített** kötetet. Helyi garanciákat, kis késést és nagyobb teljesítményt igénylő munkaterhelések esetén válassza a **helyileg rögzített** **kötet** lehetőséget. Az összes többi adattal válassza a **Lépcsőzetes** **kötet** lehetőséget.
   * A **kapacitás** mezőben határozza meg a kötet méretét. A rétegű kötetnek 500 GB és 5 TB közé kell esnie, és egy helyileg rögzített kötetnek 50 GB és 500 GB között kell lennie.
     
     Egy helyileg rögzített kötetet sűrűn kiosztottak, és biztosítja, hogy a köteten lévő elsődleges adatmennyiség az eszközön maradjon, és ne kerüljön a felhőbe.
     
     A többrészes köteteket a rendszer dinamikusan kiépíti. A rétegű kötetek létrehozásakor a terület körülbelül 10%-a a helyi szinten van kiépítve, a terület 90%-a pedig a felhőben van kiépítve. Ha például egy 1 TB-os kötetet telepített, a 100 GB a helyi térben, a 900 GB-ot pedig a felhőben fogja használni az adatrétegeknél. Ez azt jelenti, hogy ha elfogyott az eszközön lévő összes helyi terület, nem lehet kiépíteni egy rétegű megosztást (mivel a 10% nem lesz elérhető).
     
     ![2. kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kattintson a **csatlakoztatott gazdagépek** elemre, jelölje ki a kötethez csatlakozni kívánt iSCSI-kezdeményezőhöz tartozó hozzáférés-vezérlési REKORDOT (ACR), majd kattintson a **kiválasztás** gombra. <br><br> 
3. Új csatlakoztatott gazdagép hozzáadásához kattintson az **új hozzáadása** lehetőségre, adja meg a gazdagép nevét és az iSCSI minősített nevét (IQN), majd kattintson a **Hozzáadás** gombra. Ha nem rendelkezik a IQN, ugorjon a " [a" függelékre: a Windows Server-GAZDAGÉP IQN beszerzése](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![3. kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Ha befejezte a kötet konfigurálását, kattintson **az OK** gombra. A rendszer létrehoz egy kötetet a megadott beállításokkal, és értesítést fog látni. Alapértelmezés szerint a rendszer engedélyezi a figyelést és a biztonsági mentést a köteten.
   
     ![4. kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. A kötet sikeres létrehozásának ellenőrzéséhez nyissa meg a **kötetek** panelt. Ekkor meg kell jelennie a felsorolt kötetnek.
   
   ![5. kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4. lépés: kötet csatlakoztatása, inicializálása és formázása

A következő lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-gazdagépen.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Kötet csatlakoztatása, inicializálása és formázása

1. Nyissa meg az **iSCSI-kezdeményező** alkalmazást a megfelelő kiszolgálón.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Felderítés** lapon kattintson a **Kapu felderítése** elemre.
   
    ![portál felderítése](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. A **Tárolókapu felderítése** párbeszédablakban adja meg az iSCSI-kompatibilis hálózati adaptert, majd kattintson az **OK** gombra.
   
    ![IP-cím](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Tárolók** lapon keresse meg a **Felderített tárolók** elemet. (Minden kötet felderített cél lesz.) Az eszköz állapota **inaktívként** jelenik meg.
   
    ![felderített célok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Jelölje ki a kívánt eszközt, majd kattintson a **Csatlakoztatás** gombra. Az eszköz csatlakoztatása után az állapotnak **Csatlakoztatva** állapotra kell módosulnia. (A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért lásd: a [Microsoft iSCSI-kezdeményező telepítése és konfigurálása][1].
   
    ![cél eszköz kiválasztása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. A Windows-gazdagépen nyomja le a Windows és az X billentyűt, majd kattintson a **Futtatás** parancsra.
7. A **Futtatás** párbeszédpanelen írja be a **Diskmgmt.msc** karakterláncot. Kattintson az **OK** gombra, és megjelenik a **Lemezkezelés** párbeszédablak. A jobb oldali panel a gazdagépen lévő köteteket jeleníti meg.
8. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.
   
    ![Lemezkezelés](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kattintson a jobb gombbal, és válassza a **lemez inicializálása** elemet.
   
    ![1. lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. A párbeszédpanelen válassza ki az inicializálni kívánt lemez (eke) t, majd kattintson az **OK** gombra.
    
    ![2. lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Elindul az új egyszerű kötet varázsló. Válassza ki a lemez méretét, majd kattintson a **tovább** gombra.
    
    ![új kötet varázsló 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Rendeljen egy meghajtóbetűjelet a kötethez, majd kattintson a **tovább** gombra.
    
    ![új kötet varázsló 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Adja meg a kötet formázásához szükséges paramétereket. **A Windows Serveren csak az NTFS támogatott.** Állítsa a foglalási egység méretét 64 kb-ra. Adjon meg egy címkét a kötethez. Ajánlott eljárás, hogy a név azonos legyen a StorSimple virtuális tömbben megadott kötet nevével. Kattintson a **Tovább** gombra.
    
    ![új kötet varázsló 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Jelölje be a kötet értékeit, majd kattintson a **Befejezés** gombra.
    
    ![új kötet varázsló 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    A kötetek **online állapotban** jelennek meg a **Lemezkezelés** lapon.
    
    ![online kötetek](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a helyi webes felhasználói felületet a [StorSimple virtuális tömb felügyeletére](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>A függelék: a Windows Server-gazdagép IQN beolvasása

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás iSCSI minősített nevének (IQN) lekéréséhez.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows Server-gazdagép IQN nevének lekérése

1. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő sztringet.
   
    ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Mentse ezt a sztringet.

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)