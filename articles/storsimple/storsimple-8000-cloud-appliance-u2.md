---
title: StorSimple Cloud Appliance – 3. frissítés | Microsoft Docs
description: Megtudhatja, hogyan hozható létre, helyezhető üzembe és kezelhető egy StorSimple felhőalapú készülék egy Microsoft Azure-alapú virtuális hálózaton. (A StorSimple 3. frissítésére és az újabb verziókra vonatkozik).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: d78051c1a5af82a986152c8244d25b68dd65d552
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968842"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>A StorSimple Cloud Appliance üzembe helyezése és kezelése az Azure-ban (3. frissítés és újabb)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple 8000 sorozatú felhőalapú készülék egy további elérhető képessége a Microsoft Azure StorSimple-megoldásnak. A StorSimple felhőalapú készülék egy virtuális gépen fut egy Microsoft Azure-alapú virtuális hálózatban, és arra szolgál, hogy a gazdagépeken tárolt adatokról biztonsági másolatokat készítsen, illetve klónozza őket.

Ez a cikk lépésekre bontva mutatja be a StorSimple Cloud Appliance üzembe helyezésének és kezelésének folyamatát az Azure-ban. A cikk elolvasása után:

* Megtudhatja, hogy miben különbözik a felhőalapú készülék a fizikai eszköztől.
* Képes lesz létrehozni és konfigurálni a felhőalapú készüléket.
* Csatlakozhat a felhőalapú készülékhez.
* Megismerheti a felhőalapú készülék használatának módját.

Ez az oktatóanyag a 3. frissítést és újabb verziókat futtató összes StorSimple Cloud Appliance-ra érvényes.

#### <a name="cloud-appliance-model-comparison"></a>Felhőalapú készülék-modellek összehasonlítása

A StorSimple felhőalapú készülék két modellben érhető el: a hagyományos 8010-es (előző nevén 1100-as) és a prémium 8020-as változatban (amely a 2. frissítésben jelent meg először). A két modell összehasonlítása az alábbi táblázatban látható.

| Eszközmodell | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximális kapacitás** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 mag, 7 GB memória)| Standard_DS3 (4 mag, 14 GB memória)|
| **Régiónkénti elérhetőség** |Minden Azure-régió |A Premium Storage-ot támogató Azure-régiók és DS3 Azure-beli virtuális gépek<br></br>[Ebben a listában](https://azure.microsoft.com/regions/services/) ellenőrizheti, hogy a **Virtuális gépek &gt; DS-sorozat** és a **Storage &gt; Disk Storage** termékek egyaránt elérhetők-e az adott régióban. |
| **Tárolási típus** |A helyi lemezeken Azure Standard szintű tárolást használ<br></br> További információ a [Standard szintű tárfiók létrehozásáról](../storage/common/storage-account-create.md) |Azure Premium Storaget használ a helyi lemezekhez<sup>2</sup> <br></br> |
| **Útmutató a számítási feladatokhoz** |A fájlok elemszintű lekérése a biztonsági másolatokból |Felhőalapú fejlesztési és tesztelési forgatókönyvek <br></br>Kis késés és nagyobb teljesítményű számítási feladatok<br></br>Másodlagos vészhelyreállítási eszköz |

<sup>1</sup> *korábbi nevén 1100*.

<sup>2</sup> *mind a 8010, mind a 8020 az Azure standard Storage-t használja a felhő szintjéhez. A különbség csak a helyi szinten található az eszközön belül*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>A felhőalapú készülék és a fizikai eszköz közötti különbségek

A StorSimple felhőalapú készülék egy csak szoftveres StorSimple-verzió, amely a Microsoft Azure-alapú virtuális gép egyetlen csomópontján fut. A felhőalapú berendezés támogatja azokat a vészhelyreállítási forgatókönyveket, amelyekben a fizikai eszköz nem érhető el. A felhőalapú berendezés alkalmas a biztonsági másolatokból történő elemszintű előhívásokra, a helyszíni vészhelyreállításra, valamint felhőalapú fejlesztési és tesztelési forgatókönyvekkel való használatra.

#### <a name="differences-from-the-physical-device"></a>Különbségek a fizikai eszközhöz képest

Az alábbi táblázat a StorSimple felhőalapú készülék és a StorSimple fizikai eszköz közötti fontosabb különbségeket mutatja be.

|  | Fizikai eszköz | Felhőalapú készülék |
| --- | --- | --- |
| **Hely** |Az adatközpontban található. |Az Azure-ban fut. |
| **Hálózati adapterek** |Hat hálózati adapterrel rendelkezik: DATA 0-tól DATA 5 számozásig. |Csak egy hálózati adapterrel rendelkezik: DATA 0. |
| **Regisztráció** |A rendszer a kezdeti konfigurációs lépés során regisztrálja. |A regisztráció egy külön feladat. |
| **Szolgáltatás adattitkosítási kulcsa** |Újra létrejön a fizikai eszközön, majd az új kulccsal frissíti a felhőalapú készüléket. |A felhőalapú készülékről nem tud újra létrejönni. |
| **Támogatott kötettípusok** |A helyileg rögzített és a rétegzett köteteket is támogatja. |Csak a rétegzett köteteket támogatja. |

## <a name="prerequisites-for-the-cloud-appliance"></a>A felhőalapú készülék előfeltételei

Az alábbi szakaszok ismertetik a StorSimple felhőalapú készülék előfeltételeit. Felhőalapú készülék üzembe helyezése előtt tekintse meg a felhőalapú készülékek használatára vonatkozó biztonsági szempontokat.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Azure-követelmények

A felhőalapú készülék kiépítése előtt a következő előkészületekre lesz szükség az Azure-környezetben:

* Győződjön meg arról, hogy az adatközpontban StorSimple 8000 sorozatú fizikai eszköz (8100-as vagy 8600-as modell) van üzembe helyezve és fut. Az eszközt ugyanazzal a StorSimple-eszközkezelő szolgáltatással regisztrálja, amelyhez StorSimple Cloud Appliance-t kíván létrehozni.
* A felhőalapú készülékhez [konfigurálnia kell egy virtuális hálózatot az Azure-on](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Premium szintű Storage használata esetén a Premium szintű Storage-ot támogató Azure-régióban kell létrehoznia egy virtuális hálózatot. A prémium szintű Storage-régiók olyan régiók, amelyek a Lemezes tárolás sornak felelnek meg az [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/services/) című lapon található listában.
* Saját DNS-kiszolgálónév megadása helyett az Azure által megadott alapértelmezett DNS-kiszolgáló használata javasolt. Ha a megadott DNS-kiszolgálónév nem érvényes, vagy ha a DNS-kiszolgáló nem tudja megfelelően feloldani az IP-címeket, akkor a felhőalapú készülék létrehozása sikertelen lesz.
* A végpont és telephely közötti, valamint a telephely és telephely közötti lehetőségek választhatóak, de nem kötelezőek. Igény szerint ezeket a lehetőségeket speciális forgatókönyvekhez is konfigurálhatja.
* Az [Azure-beli virtuális gépeket](../virtual-machines/windows/quick-create-portal.md) (gazdakiszolgálókat) létrehozhatja abban a virtuális hálózatban, amely a felhőalapú készüléken elérhető köteteket használja. Ezeknek a kiszolgálóknak az alábbi követelményeknek kell megfelelniük:

  * Windows vagy Linux rendszerű virtuális gépnek kell lenniük, amelyen telepítve van az iSCSI-kezdeményező szoftver.
  * Ugyanazon a virtuális hálózaton kell futniuk, mint a felhőalapú készüléknek.
  * Tudniuk kell csatlakozni a felhőalapú készülék iSCSI-tárolójához a felhőalapú készülék belső IP-címén keresztül.
  * Ellenőrizze, hogy ugyanazon a virtuális hálózaton konfigurálta-e az iSCSI és a felhőalapú forgalom támogatását.

#### <a name="storsimple-requirements"></a>A StorSimple követelményei

Hajtsa végre a következő frissítéseket a StorSimple-eszközkezelő szolgáltatáson a felhőalapú készülék létrehozása előtt:

* Vegyen fel [hozzáférés-vezérlési rekordokat](storsimple-8000-manage-acrs.md) azokhoz a gépekhez, amelyek a felhőalapú készülék gazdakiszolgálói lesznek.
* A felhőalapú készülékkel megegyező régióban lévő [tárfiókot](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) használjon. Különböző régiókban lévő tárfiókok használata esetén a teljesítmény gyenge lehet. A felhőalapú készülékkel Standard vagy Prémium szintű Storage-fiókot is használhat. További információ a [standard szintű Storage-fiókok](../storage/common/storage-account-create.md)létrehozásáról.
* A felhőalapú készülék létrehozásához ne használja ugyanazt a tárfiókot, amelyet az adataihoz is használ. Ugyanazon tárfiók használata esetén a teljesítmény gyenge lehet.

Mielőtt hozzákezd, ellenőrizze az alábbi információk meglétét:

* Az Azure Portal-fiókja és a hozzáférési hitelesítő adatai.
* A szolgáltatásadat-titkosítási kulcs másolata arról a fizikai eszközről, amely a StorSimple-eszközkezelő szolgáltatásban van regisztrálva.

## <a name="create-and-configure-the-cloud-appliance"></a>A felhőalapú készülék létrehozása és konfigurálása

Az eljárások végrehajtása előtt ellenőrizze, hogy teljesülnek-e [a felhőalapú készülék előfeltételei](#prerequisites-for-the-cloud-appliance).

Az alábbi lépések végrehajtásával hozhat létre StorSimple felhőalapú készüléket.

### <a name="step-1-create-a-cloud-appliance"></a>1. lépés: Felhőalapú készülék létrehozása

Az alábbi lépések végrehajtásával létrehozhatja a StorSimple felhőalapú készüléket.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Ha a felhőalapú készülék létrehozása ebben a lépésben meghiúsul, lehet, hogy nem rendelkezik internetkapcsolattal. Felhőalapú készülék létrehozásakor olvassa el az [internetkapcsolat hibáinak elhárításával](#troubleshoot-internet-connectivity-errors) foglalkozó szakaszt a további információk megtekintéséhez.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>2. lépés: A felhőalapú készülék konfigurálása és regisztrálása

Mielőtt hozzákezdene ehhez az eljáráshoz, ellenőrizze, hogy rendelkezik-e a szolgáltatásadat-titkosítási kulcs másolatával. A szolgáltatásadat-titkosítási kulcs akkor jött létre, amikor az első StorSimple fizikai eszközt regisztrálta a StorSimple-eszközkezelő szolgáltatásban. A kulcsot biztonságos helyre kellett mentenie. Ha nem rendelkezik a szolgáltatásadat-titkosítási kulcs másolatával, akkor a Microsoft támogatási szolgálat segítségét kell kérnie.

A StorSimple felhőalapú készülék konfigurálásához és regisztrálásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3. lépés: (opcionális) Az eszköz konfigurációs beállításainak módosítása

Az alábbi szakasz azokat az eszközkonfigurációs beállításokat ismerteti, amelyek a StorSimple felhőalapú készülék számára szükségesek, ha a CHAP protokollt vagy a StorSimple Snapshot Managert kívánja használni, vagy módosítani szeretné az eszköz rendszergazdai jelszavát.

#### <a name="configure-the-chap-initiator"></a>A CHAP-kezdeményező konfigurálása

Ez a paraméter azokat a hitelesítő adatokat tartalmazza, amelyeket a felhőalapú készülék (cél) a kötetek elérését megkísérlő kezdeményezőktől (kiszolgálóktól) vár. A hitelesítés során a kezdeményezők egy CHAP-felhasználónév és egy CHAP-jelszó segítségével azonosítják magukat az eszköznél. A részletes lépéseket lásd: [A CHAP konfigurálása az eszközhöz](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>A CHAP céljának konfigurálása

Ez a paraméter tartalmazza azokat a hitelesítő adatokat, amelyeket a felhőalapú készülék akkor használ, ha egy CHAP-támogatással rendelkező kezdeményező kölcsönös vagy kétirányú hitelesítést kér. A felhőalapú készülék egy fordított CHAP-felhasználónév és egy fordított CHAP-jelszó segítségével azonosítja magát a kezdeményezőnél a hitelesítési folyamat során.

> [!NOTE]
> A CHAP-cél beállításai globálisak. Ha alkalmazza ezeket a beállításokat, a felhőalapú készülékhez csatlakoztatott összes kötet CHAP-hitelesítést fog használni.

A részletes lépéseket lásd: [A CHAP konfigurálása az eszközhöz](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszavának konfigurálása

A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

> [!NOTE]
> A felhőalapú készülék esetén a Windows-gazdagép egy Azure-beli virtuális gép.

Amikor konfigurál egy eszközt a StorSimple Snapshot Managerben, a rendszer a tárolóeszköz hitelesítése érdekében a StorSimple-eszköz IP-címének és jelszavának megadására kéri. A részletes lépésekről lásd: [A StorSimple Snapshot Manager jelszavának konfigurálása](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása

Amikor a Windows PowerShell felületét a felhőalapú készülék elérésére használja, előfordulhat, hogy a rendszer egy eszközadminisztrátori jelszó megadására kéri. Az adatok biztonsága érdekében ezt a jelszót a felhőalapú készülék használata előtt módosítania kell. A részletes lépésekről lásd: [Az eszközadminisztrátori jelszó konfigurálása](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Távoli csatlakozás a felhőalapú készülékhez

A felhőalapú berendezés a Windows PowerShell felületéről való elérése alapértelmezés szerint nincs engedélyezve. Először engedélyeznie kell a távfelügyeletet a felhőalapú berendezésen, majd a felhőalapú berendezés eléréséhez használni kívánt ügyfélen.

Az alábbi kétlépéses eljárás bemutatja, hogyan lehet távolról csatlakozni a felhőalapú berendezéshez.

### <a name="step-1-configure-remote-management"></a>1. lépés: A távfelügyelet konfigurálása

A StorSimple Cloud Appliance távfelügyeletének konfigurálásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>2. lépés: A felhőalapú berendezés távoli elérése

Miután engedélyezte távfelügyeletet a felhőalapú berendezésen, a Windows PowerShell távoli eljáráshívás segítségével egy ugyanazon virtuális hálózatban lévő másik virtuális gépről is csatlakozhat a berendezéshez. Ez lehet például az a virtuális gazdagép, amelyet az iSCSI csatlakoztatásához használt és konfigurált. A legtöbb üzemelő példányban ekkor megnyit egy nyilvános végpontot a virtuális gazdagépe elérésére, amelyet a felhőalapú berendezés eléréséhez használhat.

> [!WARNING]
> **A biztonság növelése érdekében a végpontokhoz való csatlakozáshoz nyomatékosan ajánlott a HTTPS használata, majd a távoli PowerShell-munkamenet befejezése után a végpontok törlése.**

Ha szeretne távoli eljáráshívást beállítani a felhőalapú berendezéshez, kövesse a [StorSimple eszközhöz való távoli csatlakozást](storsimple-8000-remote-connect.md) bemutató cikk eljárásait.

## <a name="connect-directly-to-the-cloud-appliance"></a>Közvetlen csatlakozás a felhőalapú berendezéshez

Közvetlenül is csatlakozhat a felhőalapú berendezéshez. Ha a felhőalapú berendezéshez közvetlenül szeretne csatlakozni egy, a virtuális hálózaton vagy a Microsoft Azure-környezeten kívüli számítógépről, akkor további végpontokat kell létrehoznia.

A következő lépések végrehajtásával hozhat létre nyilvános végpontot a felhőalapú berendezésen.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Javasolt ugyanannak a virtuális hálózatnak egy másik virtuális gépéről csatlakozni, mivel így csökkenthető a nyilvános végpontok száma a virtuális hálózaton. Ebben az esetben egy távoli asztali munkameneten keresztül csatlakozhat a virtuális géphez, majd a virtuális gépet ugyanúgy konfigurálhatja a használatra, ahogyan bármely más Windows-ügyfelet egy helyi hálózaton. A nyilvános port számát nem kell hozzáfűznie, mivel a port már ismert.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Magánhálózati IP-cím lekérése a felhőalapú berendezéshez

Ahhoz, hogy a felhőalapú berendezés csatlakozzon az ugyanazon a virtuális hálózaton lévő gazdakiszolgálóhoz, szüksége van a felhőalapú berendezés belső vagy magánhálózati IP-címére. A felhőalapú berendezés magánhálózati IP-címének lekéréséhez tegye a következőket:

1. Lépjen a felhőalapú berendezés alapjául szolgáló virtuális gépre. A virtuális gép neve megegyezik a felhőalapú berendezés nevével. Lépen az **Összes erőforrás** területre, adja meg a felhőalapú berendezés és az előfizetés nevét, majd válassza a virtuális gépek típust. A megjelenő virtuális gépek listájáról válassza ki a felhőalapú berendezésnek megfelelő virtuális gépet, és kattintson rá.

     ![A felhőalapú berendezéshez tartozó virtuális gép kiválasztása](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Lépjen a **Beállítások > Hálózatkezelés** területre. A jobb oldali panelen megjelenik a felhőalapú berendezés magánhálózati IP-címe. Ezt jegyezze fel.

    ![A felhőalapú berendezés magánhálózati IP-címének lekérése](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>A StorSimple Cloud Appliance használata

Most, hogy létrehozta és konfigurálta a StorSimple Cloud Appliance-t, megkezdheti a használatát. Egy felhőalapú berendezésen ugyanúgy használhatja a kötettárolókat, köteteket és biztonsági mentési házirendeket, mint egy fizikai StorSimple eszközön. Az egyetlen különbség, hogy meg kell győződnie arról, hogy kiválasztotta a felhőalapú berendezést az eszközlistából. A felhőalapú berendezés különböző felügyeleti feladatainak részletes eljárásait [a felhőalapú berendezés a StorSimple-eszközkezelő szolgáltatással való felügyeletével kapcsolatos](storsimple-8000-manager-service-administration.md) részben találja.

Az alábbi szakaszok a felhőalapú berendezés használatakor tapasztalható egyes különbségeket ismertetik.

### <a name="maintain-a-storsimple-cloud-appliance"></a>A StorSimple Cloud Appliance karbantartása

Mivel a felhőalapú berendezés egy csak szoftveres eszköz, a karbantartása minimális egy fizikai eszköz karbantartásához képest.

A felhőalapú berendezések nem frissíthetők. A legújabb szoftververzióval hozzon létre egy új felhőalapú berendezést.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Tárfiókok létrehozása felhőalapú berendezéshez

A létrehozott tárfiókokat a StorSimple-eszközkezelő szolgáltatás, a felhőalapú berendezés és a fizikai eszköz használja. A tárfiókok létrehozásakor javasolt egy régióazonosító használata a valódi névben. Ez segít biztosítani a régió egységességét az összes rendszerösszetevőben. A felhőalapú berendezés esetében fontos, hogy az összes összetevője ugyanabban a régióban legyen, így elkerülhetővé válnak a teljesítménybeli problémák.

Az eljárás pontos lépéseit a [tárfiók felvételét](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) ismertető szakaszban találja.

### <a name="deactivate-a-storsimple-cloud-appliance"></a>A StorSimple Cloud Appliance inaktiválása

A felhőalapú berendezés inaktiválása törli a virtuális gépet és az annak üzembe helyezésekor létrejött erőforrásokat. A felhőalapú berendezést az inaktiválás után már nem lehet a korábbi állapotára visszaállítani. A felhőalapú berendezés inaktiválása előtt győződjön meg arról, hogy leállította vagy törölte a tőle függő ügyfeleket és gazdagépeket.

A felhőalapú berendezés inaktiválása az alábbi következményekkel jár:

* A felhőalapú berendezés el lesz távolítva.
* A felhőalapú berendezéshez létrehozott operációsrendszer-lemez és adatlemezek el lesznek távolítva.
* A kiépítés során létrehozott üzemeltetett szolgáltatás és virtuális hálózat megmarad. Ha ezeket nem használja, akkor manuálisan kell törölnie őket.
* A felhőalapú berendezéshez létrehozott felhőalapú pillanatfelvételek megmaradnak.

Az eljárás pontos lépéseit [a StorSimple eszköz inaktiválását és törlését](storsimple-8000-deactivate-and-delete-device.md) ismertető részben találja.

Amint a felhőalapú berendezés inaktiváltként jelenik meg a StorSimple-eszközkezelő szolgáltatás paneljén, törölheti a felhőalapú berendezést az **Eszközök** panel eszközeinek listájából.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>A felhőalapú berendezés indítása, leállítása és újraindítása
A StorSimple fizikai eszközzel ellentétben a StorSimple Cloud Appliance nem rendelkezik megnyomható be- vagy kikapcsoló gombbal. Azonban lehetnek olyan alkalmak, amikor le kell állítania és újra kell indítania a felhőalapú berendezést.

A felhőalapú berendezés indítása, leállítása és újraindítása a Virtual Machines szolgáltatás paneljéről a legegyszerűbb. Nyissa meg a Virtual Machines szolgáltatást. A virtuális gépek listájából válassza ki a felhőalapú berendezésének megfelelő virtuális gépet (ugyanaz a neve), és kattintson a virtuális gép nevére. A virtuális gép paneljén a felhőalapú berendezés állapota **Fut**, mivel a létrehozása után alapértelmezés szerint elindul. A virtuális eszközt bármikor elindíthatja, leállíthatja és újraindíthatja.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Gyári beállítások visszaállítása
Ha úgy dönt, hogy szeretné elölről kezdeni a felhőalapú berendezés létrehozását, egyszerűen inaktiválja és törölje, majd hozzon létre egy újat.

## <a name="fail-over-to-the-cloud-appliance"></a>Feladatátvétel a felhőalapú berendezésre
A vészhelyreállítás (DR) azon kulcsfontosságú forgatókönyvek egyike, amelyre a StorSimple Cloud Appliance-t tervezték. Ebben az esetben előfordulhat, hogy nem érhető el a fizikai StorSimple-eszköz vagy az egész adatközpont. Szerencsére a felhőalapú berendezés segítségével egy másik helyen helyreállíthatja a működést. A vészhelyreállítás során a forráseszközről származó kötettárolók tulajdonjogai módosulnak, és átkerülnek a felhőalapú berendezésre.

A vészhelyreállítás előfeltételei:

* A felhőalapú berendezést létrehozta és konfigurálta.
* A kötettárolóban található minden kötet offline állapotban van.
* A kötettárolóhoz, amelynek a feladatait át szeretné adni, tartozik felhőbeli pillanatkép.

> [!NOTE]
> * Ha felhőalapú berendezést használ a vészhelyreállítás másodlagos eszközeként, vegye figyelembe, hogy a 8010-es modell 30 TB Standard szintű tárterülettel, a 8020-as modell pedig 64 TB Prémium szintű tárterülettel rendelkezik. A nagyobb kapacitású 8020-as felhőalapú berendezés alkalmasabb lehet egy vészhelyreállítási forgatókönyvhöz.

Az eljárás pontos lépéseit a [felhőalapú berendezésre történő feladatátvételt](storsimple-8000-device-failover-cloud-appliance.md) ismertető szakaszban találja.

## <a name="delete-the-cloud-appliance"></a>A felhőalapú berendezés törlése
Ha nem szeretné, hogy egy korábban konfigurált és használt StorSimple Cloud Appliance használatáért további költségeket számoljanak fel Önnek, akkor állítsa le a felhőalapú berendezést. A felhőalapú berendezés leállítása felszabadítja a virtuális gépet. Ha ezt megteszi, nem keletkeznek további költségek az előfizetésén. Az operációs rendszer és az adatlemezek tárolása azonban továbbra is költségekkel jár majd.

Az összes költség megszüntetéséhez törölnie kell a felhőalapú berendezést. A felhőalapú berendezés által létrehozott biztonsági mentések törléséhez törölheti vagy inaktiválhatja az eszközt. További információkért lásd [a StorSimple eszköz inaktiválását vagy törlését](storsimple-8000-deactivate-and-delete-device.md) ismertető szakaszt.

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Az internetkapcsolat hibáinak elhárítása
Ha nincs internetkapcsolat a felhőalapú berendezés létrehozása közben, a létrehozási lépés sikertelen lesz. Az internetes kapcsolat hibáinak elhárítása érdekében hajtsa végre a következő lépéseket az Azure Portalon:

1. [Windows rendszerű virtuális gép létrehozása az Azure Portalon](../virtual-machines/windows/quick-create-portal.md) A virtuális gépnek ugyanazt a tárfiókot, virtuális hálózatot és alhálózatot kell használnia, mint a felhőalapú berendezés. Ha már létezik Windows Server-gazdagép az Azure-ban, amely ugyanazt a tárfiókot, virtuális hálózatot és alhálózatot használja, azt is használhatja az internetkapcsolat hibáinak elhárítására.
2. Jelentkezzen be távolról az előző lépésben létrehozott virtuális gépbe.
3. Nyisson meg egy parancsablakot a virtuális gépen (Win+R, majd írja be a `cmd` kifejezést).
4. Futtassa az alábbi parancsot a parancssorban.

    `nslookup windows.net`
5. Ha az `nslookup` sikertelen, akkor az internetkapcsolat hibája megakadályozza, hogy a felhőalapú berendezés regisztráljon a StorSimple-eszközkezelő szolgáltatásban.
6. Végezze el a szükséges módosításokat a virtuális hálózaton, hogy a felhőalapú berendezés elérhesse az Azure-helyeket, például a _windows.net_ helyet.

## <a name="next-steps"></a>Következő lépések
* Tekintse át, hogyan [felügyelhető a felhőalapú berendezés a StorSimple-eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).
* Ismerje meg, hogyan hajtható végre egy [StorSimple-kötet visszaállítása egy biztonságimentés-készletből](storsimple-8000-restore-from-backup-set-u2.md).