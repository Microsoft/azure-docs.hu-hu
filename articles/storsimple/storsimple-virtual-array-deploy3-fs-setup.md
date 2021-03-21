---
title: StorSimple virtuális tömb beállítása fájlkiszolgálóként | Microsoft Docs
description: Ez a harmadik oktatóanyag a StorSimple Virtual Array-telepítésben arra utasítja, hogy egy virtuális eszközt fájlkiszolgálóként állítson be.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005928"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple virtuális tömb üzembe helyezése – beállítás fájlkiszolgáló használatával Azure Portal
![A virtuális tömb üzembe helyezéséhez szükséges lépéseket bemutató ábra. A harmadik lépés első része fájlkiszolgálóként van beállítva, és ki van emelve.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Bevezetés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez a cikk a kezdeti beállítás végrehajtását, a StorSimple-fájlkiszolgáló regisztrálását, az eszköz telepítésének befejezését, valamint az SMB-megosztások létrehozását és kapcsolódását ismerteti. Ez a telepítési oktatóanyagok sorozatának utolsó cikke, amely a virtuális tömb fájlkiszolgáló vagy iSCSI-kiszolgáló általi teljes üzembe helyezéséhez szükséges.

A telepítés és a konfigurálás folyamata körülbelül 10 percet vesz igénybe. A cikkben szereplő információk csak a StorSimple virtuális tömb üzembe helyezésére vonatkoznak. A StorSimple 8000 Series-eszközök üzembe helyezéséhez keresse fel a következőt: [a StorSimple 8000 Series eszköz üzembe helyezése a 2. frissítést futtató eszközön](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Telepítési előfeltételek
A StorSimple virtuális tömb konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Létrehozta a virtuális tömböt, és a hozzá tartozó módon csatlakoztatta azt a [Hyper-V StorSimple virtuális tömb](storsimple-virtual-array-deploy2-provision-hyperv.md) kiépítése vagy [egy StorSimple virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md).
* A szolgáltatás regisztrációs kulcsa a StorSimple Eszközkezelő-szolgáltatás, amelyet a StorSimple virtuális tömbök kezeléséhez hozott létre. További információ: 2. [lépés: a StorSimple Virtual Array szolgáltatás regisztrációs kulcsának beszerzése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) .
* Ha ez a második vagy az azt követő virtuális tömb, amelyet egy meglévő StorSimple Eszközkezelő szolgáltatáshoz regisztrál, rendelkeznie kell a szolgáltatás adattitkosítási kulcsával. Ez a kulcs akkor jött létre, amikor az első eszközt sikeresen regisztrálták ezzel a szolgáltatással. Ha elvesztette a kulcsot, tekintse meg a StorSimple Virtual Array [szolgáltatáshoz tartozó adattitkosítási kulcs beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) című témakört.

## <a name="step-by-step-setup"></a>Lépésenkénti beállítás
A StorSimple virtuális tömb beállításához és konfigurálásához használja a következő lépésenkénti útmutatót.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: a helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása
#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejezése és az eszköz regisztrálása
1. Nyisson meg egy böngészőablakot, és kapcsolódjon a helyi webes felhasználói felülethez. Típus:
   
   `https://<ip-address of network interface>`
   
   Használja az előző lépésben feljegyzett kapcsolatok URL-címét. Megjelenik egy hibaüzenet, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. Kattintson **a folytatás erre a weblapra** gombra.
   
   ![A biztonsági tanúsítvány problémáját jelentő böngészőablak képernyőképe. Két hivatkozás látható, egyet a webhelyhez és egyet a felhasználó kezdőlapján.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Jelentkezzen be a virtuális tömb webes felhasználói felületére **StorSimpleAdmin** néven. Adja meg a 3. lépésben módosított eszköz-rendszergazdai jelszót. a virtuális tömb elindítása a [StorSimple virtuális tömb kiépítése a Hyper-V-](storsimple-virtual-array-deploy2-provision-hyperv.md) ben vagy a [StorSimple virtuális tömb kiépítése a VMware](storsimple-virtual-array-deploy2-provision-vmware.md)-ben.
   
   ![Képernyőkép a StorSimple bejelentkezési oldaláról. A StorSimpleAdmin-Felhasználónév látható, és a jelszó mező meghatározatlan karakterrel van kitöltve.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. A **kezdőlapra** kerül. Ez a lap a virtuális tömb konfigurálásához és a StorSimple Eszközkezelő szolgáltatásban való regisztrálásához szükséges különböző beállításokat ismerteti. A **hálózati beállítások**, a **webproxy beállításai** és az **időbeállítások** megadása nem kötelező. Az egyetlen szükséges beállítás az **eszközbeállítások** és a **felhő beállításai**.
   
   ![Képernyőkép a kezdőlapról. A szöveg azt jelzi, hogy az eszköz nincs konfigurálva. A különféle típusú beállításokra mutató hivatkozások láthatók.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. A hálózati **adapterek** **hálózati beállítások** lapján a 0. adat automatikusan be lesz állítva. Az IP-címek automatikus beszerzéséhez minden hálózati adapter alapértelmezés szerint be van állítva. Ezért a rendszer automatikusan hozzárendel egy IP-címet, egy alhálózatot és egy átjárót (az IPv4 és az IPv6 esetében egyaránt).
   
   ![Képernyőkép a hálózati beállítások lapról, amely a Internet Protocol különböző verzióihoz konfigurált IP-címeket tartalmazza.](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Ha egynél több hálózati adaptert adott hozzá az eszköz kiépítés során, itt konfigurálhatja őket. Megjegyzés: a hálózati adaptert IPv4-ként vagy IPv4-ként vagy IPv6-ként is konfigurálhatja. Az IPv6-konfigurációk nem támogatottak.
5. A DNS-kiszolgálókat azért kell használni, mert amikor az eszköz megpróbál kommunikálni a felhőalapú tárolási szolgáltatóktól, vagy ha fájlkiszolgálóként van konfigurálva, akkor az eszköz név szerinti feloldására van szükség. A **hálózati beállítások** lapon a **DNS-kiszolgálók** területen:
   
   1. A rendszer automatikusan konfigurálja az elsődleges és másodlagos DNS-kiszolgálót. Ha statikus IP-címek konfigurálását választja, megadhatja a DNS-kiszolgálókat. A magas rendelkezésre állás érdekében javasoljuk, hogy állítson be egy elsődleges és egy másodlagos DNS-kiszolgálót.
   2. Kattintson az **alkalmaz** gombra a hálózati beállítások alkalmazásához és ellenőrzéséhez.
6. Az **eszközbeállítások** lapon:
   
   1. Rendeljen egyedi **nevet** az eszközhöz. Ez a név 1-15 karakterből állhat, és tartalmazhat betűt, számot és kötőjelet.
   2. Kattintson a  :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: létrehozandó eszköz **típusának** fájlkiszolgáló ikonjára. A fájlkiszolgálók lehetővé teszik a megosztott mappák létrehozását.
   3. Mivel az eszköz egy fájlkiszolgáló, csatlakoztatnia kell az eszközt egy tartományhoz. Adja meg a **tartománynevet**.
   4. Kattintson az **Alkalmaz** gombra.
7. Ekkor megjelenik egy párbeszédpanel. Adja meg a tartománya hitelesítő adatait a megadott formátumban. Kattintson a pipa ikonra. A tartományi hitelesítő adatok ellenőrzése megtörtént. Hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
   
   ![Képernyőfelvétel: a felhasználónévvel és a jelszóval kitöltött párbeszédpanel.](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kattintson az **Alkalmaz** gombra. Ez az eszköz beállításait fogja alkalmazni és érvényesíteni.
   
   ![Képernyőkép az eszközbeállítások lapról. A rendszer kitölti az eszköz nevét és a tartománynevet.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a virtuális tömb a Active Directory saját szervezeti egységében van (OU), és a csoportházirend-objektumok (GPO) nem lettek alkalmazva rá vagy örökölt. A csoportházirend olyan alkalmazásokat telepíthet, mint például a víruskereső szoftver a StorSimple virtuális tömbben. A további szoftverek telepítése nem támogatott, és az adatsérüléshez vezethet. 
   > 
   > 
9. (Opcionálisan) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani.
   
   ![Képernyőfelvétel a webproxy beállításai lapról. A webproxy engedélyezése lehetőség ki van kapcsolva, és a hitelesítés nincs értékre van állítva. Nincs beállítva más érték.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   A **webproxy** oldalon:
   
   1. Adja meg a **webproxy URL** -címét a következő formátumban: *http:// &lt; Host-IP-cím vagy FQDN &gt; :P ORT száma*. Vegye figyelembe, hogy a HTTPS URL-címek nem támogatottak.
   2. A **hitelesítést** a **Basic** vagy a **none** értékre kell beállítani.
   3. Ha hitelesítést használ, meg kell adnia egy **felhasználónevet** és egy **jelszót** is.
   4. Kattintson az **Alkalmaz** gombra. Ez a művelet érvényesíti és alkalmazza a konfigurált webproxy-beállításokat.
10. (Opcionálisan) konfigurálhatja az eszköz időbeállítását, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálókat. Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.
    
    ![Képernyőkép a Time Settings lapról. A rendszer kitölti az időzónát és az elsődleges N T P-kiszolgálót. A másodlagos N T P kiszolgáló üres.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Az **időbeállítások** lapon:
    
    1. A legördülő listából válassza ki az **időzónát** annak a földrajzi helynek az alapján, amelyben az eszközt üzembe helyezi. Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adja meg az eszköz **Elsődleges NTP-kiszolgálóját** , vagy fogadja el az Time.Windows.com alapértelmezett értékét. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Opcionálisan megadhat egy **másodlagos NTP-kiszolgálót** az eszközhöz.
    4. Kattintson az **Alkalmaz** gombra. Ezzel a művelettel érvényesítheti és alkalmazhatja a beállított időértékeket.
11. Adja meg az eszközhöz tartozó felhő beállításait. Ebben a lépésben elvégzi a helyi eszköz konfigurációját, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatásával.
    
    1. Adja meg a **szolgáltatás regisztrációs kulcsát** , amelyet a [2. lépésben kapott: a StorSimple Virtual Array szolgáltatás regisztrációs kulcsának beszerzése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) .
    2. Ha az első eszköz regisztrálva van ezzel a szolgáltatással, akkor a **szolgáltatás adattitkosítási kulcsa** jelenik meg. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. Erre a kulcsra a szolgáltatás regisztrációs kulcsának használatakor van szükség további eszközök regisztrálásához a StorSimple Eszközkezelő szolgáltatással. 
       
       Ha nem ez az első eszköz, amelyet a szolgáltatással regisztrál, meg kell adnia a szolgáltatás adattitkosítási kulcsát. További információt a [szolgáltatás adattitkosítási kulcsának](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) beszerzése a helyi webes felhasználói felületen című témakörben talál.
    3. Kattintson a **Regisztrálás** parancsra. Ekkor a rendszer újraindítja az eszközt. Előfordulhat, hogy várnia kell 2-3 percet, mielőtt az eszköz regisztrálása sikeresen megtörtént. Az eszköz újraindítása után megnyílik a bejelentkezési oldal.
       
       ![Képernyőkép a Cloud Settings lapról. A regisztrációs kulcs és a titkosítási kulcs mezők ki vannak töltve, de az értékek kimaradnak.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Lépjen vissza az Azure Portalra. Nyissa meg az **összes erőforrást**, keresse meg a StorSimple Eszközkezelő szolgáltatását.
    
    ![Képernyőkép a Azure Portal összes erőforrás oldaláról. Egy Eszközkezelő szolgáltatás ki van emelve.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. A szűrt listában válassza ki a StorSimple Eszközkezelő szolgáltatást, majd navigáljon a **felügyeleti > eszközökhöz**. Az **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz, és **készen áll-** e az állapot beállítására.
    
    ![Üzembe helyezés](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2. lépés: az eszköz konfigurálása fájlkiszolgálóként
A szükséges eszköz telepítésének befejezéséhez hajtsa végre a következő lépéseket a [Azure Portal](https://portal.azure.com/) .

#### <a name="to-configure-the-device-as-file-server"></a>Az eszköz konfigurálása fájlkiszolgálóként
1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd lépjen a  **felügyeleti > eszközökre**. Válassza ki az imént létrehozott eszközt az **eszközök** panelen. Ez az eszköz úgy jelenik **meg, hogy készen áll a beállításra**.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet, amely jelzi, hogy az eszköz készen áll a telepítésre.
   
    ![A 2. fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kattintson a parancssorban a **configure (Konfigurálás** ) elemre. Ekkor megnyílik a **configure (Konfigurálás** ) panel. A **configure (Konfigurálás** ) panelen tegye a következőket:
   
   1. A fájlkiszolgáló neve automatikusan ki lesz töltve.
    
   2. Győződjön meg arról, hogy a Felhőbeli tároló titkosítása **engedélyezve** van. Ez a szolgáltatás titkosítja a felhőbe küldendő összes adathalmazt. 
    
   3. A titkosításhoz a felhasználó által definiált kulccsal a 256 bites AES-kulcsok használatosak. Adjon meg egy 32 karakterből álló kulcsot, majd írja be újra a kulcsot a megerősítéshez. A kulcs rögzítése egy kulcskezelő alkalmazásban későbbi használatra.
    
   4. Kattintson a **kötelező beállítások konfigurálása** elemre a Storage-fiók hitelesítő adatainak megadásához az eszközzel való használathoz. Kattintson az **új hozzáadása** lehetőségre, ha nincsenek konfigurálva a Storage-fiók hitelesítő adatai. **Győződjön meg arról, hogy a használt Storage-fiók támogatja a Blobok blokkolását. Az oldal Blobok nem támogatottak.** További információ a [blobok és az oldal Blobok blokkokról](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![3. fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. A **Storage-fiók hitelesítő adatainak hozzáadása** panelen tegye a következőket: 

    1. Válassza az aktuális előfizetés lehetőséget, ha a Storage-fiók a szolgáltatással megegyező előfizetésben található. A másik érték megadásával a Storage-fiók kívül esik a szolgáltatás előfizetésén. 
    
    2. Válasszon ki egy meglévő Storage-fiókot a legördülő listából. 
    
    3. A rendszer automatikusan kitölti a helyet a megadott Storage-fiók alapján. 
    
    4. Engedélyezze a TLS-t az eszköz és a felhő közötti biztonságos hálózati kommunikációs csatorna biztosításához.
    
    5. A Storage-fiók hitelesítő adatainak hozzáadásához kattintson a **Hozzáadás** gombra. 
   
        ![4. fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. A Storage-fiók hitelesítő adatainak sikeres létrehozása után a rendszer frissíti a **configure (Konfigurálás** ) panelt, és megjeleníti a megadott Storage-fiók hitelesítő adatait. Kattintson a **Configure** (Konfigurálás) elemre.
   
   ![5. fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Látni fogja, hogy a fájlkiszolgáló létrehozása folyamatban van. A fájlkiszolgáló sikeres létrehozása után értesítést kap.
   
   ![Fájlkiszolgáló 5b konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Az eszköz állapota szintén **online** állapotra vált.
   
   ![Fájlkiszolgáló 5c konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Továbbra is hozzáadhat egy megosztást.

## <a name="step-3-add-a-share"></a>3. lépés: megosztás hozzáadása
Megosztás létrehozásához hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Megosztás létrehozása
1. Válassza ki az előző lépésben konfigurált fájlkiszolgáló-eszközt, és kattintson a **..** . (vagy a jobb gombbal) elemre. A helyi menüben válassza a **megosztás hozzáadása** elemet. Azt is megteheti, hogy a **+ megosztás hozzáadása** lehetőségre kattint az eszköz parancssáv-sávján.
   
   ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Adja meg a következő megosztási beállításokat:

   1. A megosztás egyedi neve. A névnek olyan sztringnek kell lennie, amely 3 – 127 karaktert tartalmaz.
    
   2. A megosztás opcionális **leírása** . A Leírás segítséget nyújt a megosztási tulajdonosok azonosításában.
    
   3. A megosztás **típusa** . A típus lehet **Lépcsőzetes** vagy **helyileg rögzített**, és az alapértelmezett. Helyi garanciákat, kis késést és nagyobb teljesítményt igénylő munkaterhelések esetén válasszon egy **helyileg rögzített** megosztást. Az összes többi adattal kapcsolatban válasszon egy **Lépcsőzetes** megosztást.
      A helyileg rögzített megosztások sűrűn vannak kiépítve, és biztosítja, hogy a megosztáson lévő elsődleges adatmennyiség helyi szinten maradjon az eszközön, és ne kerüljön a felhőbe. A többrészes megosztást a rendszer dinamikusan kiépíti. Többplatformos megosztás létrehozásakor a terület 10%-a a helyi szinten lett kiépítve, a terület 90%-a pedig a felhőben van kiépítve. Ha például 1 TB-os kötetet telepített, a 100 GB a helyi térben marad, és a 900 GB-ot a felhőben fogja használni az adatszintek esetében. Ez pedig azt jelenti, hogy ha elfogyott az eszközön lévő összes helyi terület, nem lehet kiépíteni egy rétegű megosztást.
   
   4. Az **alapértelmezett teljes engedélyek beállítása** mezőben rendelje hozzá az engedélyeket a felhasználóhoz, vagy a megosztáshoz hozzáférő csoportot. Adja meg a felhasználó vagy a felhasználói csoport nevét *john \@ contoso.com* formátumban. Azt javasoljuk, hogy egy felhasználói csoportot használjon (egyetlen felhasználó helyett), hogy a rendszergazdai jogosultságok hozzáférjenek a megosztásokhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
   
   5. A megosztás létrehozásához kattintson a **Hozzáadás** gombra. 
    
       ![1. megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van.
   
       ![2. megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** panel frissülni fog az új megosztásnak megfelelően. Alapértelmezés szerint a figyelés és a biztonsági mentés engedélyezve van a megosztáshoz.
   
      ![3. megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4. lépés: Kapcsolódás a megosztáshoz
Ekkor csatlakoznia kell egy vagy több, az előző lépésben létrehozott megosztáshoz. Hajtsa végre ezeket a lépéseket a StorSimple virtuális tömbhöz csatlakoztatott Windows Server-gazdagépen.

#### <a name="to-connect-to-the-share"></a>A megosztáshoz való kapcsolódáshoz
1. Nyomja meg az :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R billentyűt. A Futtatás ablakban adja meg az elérési út *&#92;&#92;&lt; fájlkiszolgáló &gt; nevét* , és cserélje le a fájlkiszolgáló *nevét* a fájlkiszolgálón hozzárendelt eszköz nevére. Kattintson az **OK** gombra.
   
   ![Képernyőkép a Futtatás párbeszédpanelről. A megnyitási mező kitöltése a fájlkiszolgálón vezető elérési úttal történik.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Ekkor megnyílik a fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. Válasszon ki és duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
   
   ![A fájlkezelő képernyőképe. Az újonnan létrehozott megosztásokat jelölő több mappa látható.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Mostantól hozzáadhat fájlokat a megosztásokhoz, és biztonsági mentést készíthet.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan használhatja a helyi webes felhasználói felületet a [StorSimple virtuális tömb felügyeletére](storsimple-ova-web-ui-admin.md).