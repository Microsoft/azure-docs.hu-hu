---
title: A StorSimple 5000-7000-es sorozatba tartozó adatáttelepítés 8000 sorozatú eszközre | Microsoft Docs
description: Ismerje meg, hogyan telepíthet át StorSimple 5000-7000-es adatsorozatok adatait az 8000 Series eszközre, és hogy milyen előfeltételekre van az áttelepítési folyamat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428166"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Adatok migrálása a StorSimple 5000-7000 sorozatból az 8000 Series eszközre

> [!IMPORTANT]
> - 2019. július 31-én a StorSimple 5000/7000-sorozat eléri a támogatási (EOS) állapot végét. Javasoljuk, hogy a StorSimple 5000/7000 Series-ügyfelek a dokumentumban ismertetett alternatívák egyikére váltsanak át.
> - Az áttelepítés jelenleg egy támogatott művelet. Ha az StorSimple 5000-7000 Series-eszközről egy 8000 sorozatú eszközre szeretne áttelepíteni egy adatátviteli eszközt, az áttelepítést Microsoft ügyfélszolgálatatel kell ütemeznie. A Microsoft ügyfélszolgálata ezután engedélyezi az előfizetés áttelepítését. További információkért lásd: [támogatási jegy megnyitása](storsimple-8000-contact-microsoft-support.md).
> - A szolgáltatáskérelem beírása után eltarthat néhány hétig, hogy végrehajtsa az áttelepítési tervet, és ténylegesen elindítsa az áttelepítést.
> - Mielőtt felveszi Önnel a kapcsolatot a Microsoft ügyfélszolgálataval, tekintse át és fejezze be a cikkben szereplő [áttelepítési előfeltételeket](#migration-prerequisites) .

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja az áttelepítési funkciót, amely lehetővé teszi, hogy a StorSimple 5000-7000 Series ügyfelei áttelepítse az StorSimple 8000 sorozatú fizikai eszközre vagy egy 8010/8020 felhőalapú berendezésbe. Ez a cikk a 5000-7000 sorozatú örökölt eszközről egy 8000 sorozatú fizikai vagy Felhőbeli készülékre való Migrálás lépéseire mutató részletes útmutatót is tartalmaz.

Ez a cikk a helyszíni 8000 sorozatú eszközre, valamint a StorSimple Cloud Appliance is alkalmazható.


## <a name="migration-feature-versus-host-side-migration"></a>Áttelepítési funkció és a gazdagép-oldali áttelepítés

Az adatok áthelyezhetők az áttelepítési szolgáltatással vagy egy gazdagép-oldali Migrálás használatával. Ez a szakasz az egyes módszerek sajátosságait ismerteti, beleértve az előnyeit és hátrányait. Ezekkel az információkkal kiderítheti, hogy melyik módszert kívánja folytatni az adatok áttelepíthetővé való átadásához.

Az áttelepítési funkció szimulálja a vész-helyreállítási (DR) folyamatot az 7000/5000-es sorozatból a 8000-es sorozatba. Ez a funkció lehetővé teszi az adatok 5000/7000 adatsorozat-formátumból 8000 adatsorozat-formátumba való átadását az Azure-ban. Az áttelepítési folyamat a StorSimple áttelepítési eszköz használatával indítható el. Az eszköz elindítja a biztonsági mentési metaadatok letöltését és átalakítását az 8000 sorozatú eszközön, majd a legújabb biztonsági mentéssel teszi elérhetővé a köteteket az eszközön.

| Előnyök                                                                                                                                     |Hátrányok                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Az áttelepítési folyamat megőrzi az 5000/7000 sorozaton elvégzett biztonsági másolatok előzményeit.                                               | Amikor a felhasználók megpróbálnak hozzáférni az adatokhoz, az áttelepítés az Azure-ból tölti le az adatait, így az adatletöltési költségekkel jár.                                     |
| A gazdagép oldalán nem történik adat áttelepítve.                                                                                                     | A folyamatnak a biztonsági mentés és a legújabb biztonsági mentés a 8000-es sorozatba való felszínének megkezdése közötti állásidőre van szüksége (az áttelepítési eszköz használatával becsülhető fel). |
| Ez a folyamat a 8000 sorozatú eszközökön lévő összes házirendet, sávszélesség-sablont, titkosítást és egyéb beállítást megőrzi.                      | A felhasználói hozzáférés csak a felhasználók által elért adatokat fogja visszahozni, és nem fogja kiszáradni a teljes adatkészletet.                                                  |
| Ehhez a folyamathoz további időre van szükség az Azure-ban az összes régebbi biztonsági mentés átalakításához, ami aszinkron módon történik az éles környezet nélkül | A Migrálás csak Felhőbeli konfigurációs szinten végezhető el.  A Felhőbeli konfigurációban lévő egyes kötetek nem telepíthetők át külön                       |

A gazdagép-oldali áttelepítés lehetővé teszi, hogy a 8000-es sorozatok egymástól függetlenül legyenek, és az adatok másolása a 5000/7000 sorozatú eszközről a 8000 Series eszközre. Ez egyenértékű az adatok egyik tárolóeszközről a másikra való áttelepítésével. Az adatmásoláshoz különféle eszközök, például a Diskboss, a Robocopy használható.

| Előnyök                                                                                                                      |Hátrányok                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A Migrálás többfázisú módon, mennyiségi alapon is megközelíthető.                                               | Az előző biztonsági másolatok (az 5000/7000-es sorozaton) nem lesznek elérhetők az 8000 Series eszközön.                                                                                                       |
| Lehetővé teszi az adategyesítést egyetlen Storage-fiókba az Azure-ban.                                                       | A felhőbe való első biztonsági mentés a 8000 sorozaton hosszabb időt vesz igénybe, mivel a 8000-es sorozat összes adattal biztonsági mentést kell készíteni az Azure-ba.                                                                     |
| A sikeres Migrálás után az összes érték helyi lesz a berendezésen. Az adatok elérésekor nincsenek késések. | Az Azure Storage-használat a 5000/7000-es eszközről az adatok törlése után növekszik.                                                                                                        |
|                                                                                                                           | Ha az 7000/5000-es sorozatú eszköz nagy mennyiségű adattal rendelkezik, az áttelepítés során az adatoknak le kell tölteniük az Azure-ból, ami az Azure-ból való adatletöltéssel kapcsolatos költségeket és késéseket is felmerül. |

Ez a cikk csak a 5000/7000 – 8000 sorozatú eszköz áttelepítési szolgáltatására koncentrál. A gazdagépek közötti áttelepítéssel kapcsolatos további információkért lépjen az [áttelepítés más tárolóeszközökből](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf)című témakörre.

## <a name="migration-prerequisites"></a>Migrálás előfeltételei

Az alábbi áttelepítési előfeltételek vonatkoznak az örökölt 5000 vagy 7000 sorozatú eszközre és az 8000 sorozatú StorSimple eszközre.

> [!IMPORTANT]
> Tekintse át és fejezze be az áttelepítésre vonatkozó előfeltételeket, mielőtt a szolgáltatáshoz Microsoft ügyfélszolgálata.

### <a name="for-the-50007000-series-device-source"></a>Az 5000/7000 sorozatú eszközhöz (forrás)

Az áttelepítés megkezdése előtt győződjön meg a következőket:

* Rendelkezik a 5000 vagy az 7000 sorozat forrásoldali eszközzel; az eszköz lehet élő vagy leállt.

    > [!IMPORTANT]
    > Javasoljuk, hogy az áttelepítési folyamat során soros hozzáférést biztosítson az eszközhöz. Ha bármilyen eszközzel kapcsolatos probléma merül fel, a soros hozzáférés segítséget nyújthat a hibaelhárításhoz.

* Az 5000-es vagy 7000-es adatsorozat-forrásoldali eszközön a szoftver v 2.1.1.518 vagy újabb verziója fut. A korábbi verziók nem támogatottak.
* Annak ellenőrzéséhez, hogy a 5000-es vagy a 7000-es sorozat melyik verziója fut, tekintse meg a webes felhasználói felület jobb felső sarkában. Ekkor meg kell jelennie az eszköz által futtatott szoftver verziójának. Az áttelepítéshez a 5000-es vagy a 7000-es sorozatnak a v 2.1.1.518-t kell futtatnia.

    ![Az örökölt eszközön lévő szoftververzió keresése](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Ha az élő eszköz nem a v 2.1.1.518 vagy újabb verzióját futtatja, frissítse a rendszert a szükséges minimális verzióra. Előfordulhat, hogy a frissítés végrehajtásához a Microsoft ügyfélszolgálata segítségével kell dolgoznia.
    * Ha a v 2.1.1.518-t futtatja, nyissa meg a webes felhasználói felületet, és ellenőrizze, hogy van-e értesítés a beállításjegyzék-visszaállítási hibákról. Ha a beállításjegyzék-visszaállítás sikertelen volt, futtassa a beállításjegyzék-visszaállítást. Előfordulhat, hogy a Microsoft ügyfélszolgálata használatával kell dolgoznia, hogy segítsen visszaállítani a beállításjegyzéket.
    * Ha nem a v 2.1.1.518-t futtató eszközt használ, végezzen feladatátvételt a v 2.1.1.518-t futtató helyettesítő eszközön. Részletes utasításokért tekintse meg a 5000/7000 sorozatú StorSimple-eszköz DR.
    * Készítsen biztonsági másolatot az eszköz adatairól egy Felhőbeli pillanatkép elkészítése révén.
    * Keresse meg a forrásoldali eszközön futó egyéb aktív biztonsági mentési feladatokat. Ez magában foglalja a StorSimple-adatvédelmi konzol gazdagépének feladatait. Várjon, amíg az aktuális feladatok befejeződik.


### <a name="for-the-8000-series-physical-device-target"></a>Az 8000 sorozatú fizikai eszközhöz (cél)

Az áttelepítés megkezdése előtt győződjön meg a következőket:

* A cél 8000 sorozatú eszköz regisztrálva van és fut. További információ: [a StorSimple-eszköz üzembe helyezése StorSimple Manager szolgáltatással](storsimple-8000-deployment-walkthrough-u2.md).
* Az 8000 sorozatú eszközön a legújabb StorSimple 8000 Series Update 5 van telepítve, és 6.3.9600.17845 vagy újabb verziót futtat. Ha az eszközön nincsenek telepítve a legújabb frissítések, telepítenie kell a legújabb frissítéseket, mielőtt folytatná az áttelepítést. További információ: a [legújabb frissítés telepítése az 8000 Series-eszközön](storsimple-8000-install-update-5.md).
* Az Azure-előfizetése engedélyezve van az áttelepítéshez. Ha az előfizetése nincs engedélyezve, lépjen kapcsolatba Microsoft ügyfélszolgálata az előfizetés áttelepítésének engedélyezéséhez.

### <a name="for-the-80108020-cloud-appliance-target"></a>Az 8010/8020 Cloud Appliance (cél) esetében

Az áttelepítés megkezdése előtt győződjön meg a következőket:

* A cél felhőalapú berendezés regisztrálva van és fut. További információ: [StorSimple Cloud Appliance üzembe helyezése és kezelése](storsimple-8000-cloud-appliance-u2.md).
* A felhőalapú berendezés a legújabb StorSimple 8000 Series Update 5 szoftververzió 6.3.9600.17845 fut. Ha a felhőalapú készülék nem az 5. frissítést futtatja, hozzon létre egy új, 5. frissítéssel rendelkező felhőalapú készüléket, mielőtt folytatná az áttelepítést. További információ: [8010/8020 Cloud Appliance létrehozása](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>A StorSimple áttelepítési eszközt futtató számítógép esetén

A StorSimple áttelepítési eszköz egy FELHASZNÁLÓIFELÜLET-alapú eszköz, amely lehetővé teszi az adatok áttelepítését egy StorSimple 5000-7000-sorozatból egy 8000 sorozatú eszközre. A StorSimple áttelepítési eszköz telepítéséhez használjon olyan számítógépet, amely megfelel az alábbi követelményeknek.

A számítógép internetkapcsolattal rendelkezik, és:

* A a következő operációs rendszert futtatja
    * Windows 10.
    * Windows Server 2012 R2 (vagy újabb) a StorSimple áttelepítési eszköz telepítéséhez.
* Telepítve van a .NET 4.5.2.
* Az eszköz telepítéséhez és használatához legalább 5 GB szabad terület szükséges.

> [!TIP]
> Ha a StorSimple-eszköz egy Windows Server-gazdagéphez csatlakozik, akkor telepítheti az áttelepítési eszközt a Windows Server-gazdagépen.

#### <a name="to-install-storsimple-migration-tool"></a>A StorSimple áttelepítési eszköz telepítése

A StorSimple áttelepítési eszköz telepítéséhez hajtsa végre a következő lépéseket a számítógépen.

1. Másolja a _StorSimple8000SeriesMigrationTool_ mappát a Windows rendszerű számítógépre. Győződjön meg arról, hogy a meghajtó, ahová a szoftvert másolja, elegendő lemezterülettel rendelkezik.

    Nyissa meg az eszköz konfigurációs fájlját _StorSimple8000SeriesMigrationTool.exe.config_ a mappában. Itt látható a fájl kódrészlete.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Szerkessze a kulcsoknak megfelelő értékeket, és cserélje le a következőre:

    * `UserName` – A Felhasználónév Azure Portalba való bejelentkezéshez.
    * `SubscriptionName and SubscriptionId` – Az Azure-előfizetés neve és azonosítója. A StorSimple Eszközkezelő szolgáltatás kezdőlapjának **általános** területén kattintson a **Tulajdonságok** elemre. Másolja a szolgáltatáshoz társított előfizetés nevét és előfizetési AZONOSÍTÓját.
    * `ResourceName` – A StorSimple Eszközkezelő szolgáltatás neve a Azure Portalban. A szolgáltatás tulajdonságai területen is látható.
    * `ResourceGroup` – A StorSimple Eszközkezelő szolgáltatáshoz társított erőforráscsoport neve a Azure Portal. A szolgáltatás tulajdonságai területen is látható.
    ![Cél eszköz szolgáltatás tulajdonságainak megtekintése](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure Active Directory a bérlő AZONOSÍTÓját a Azure Portalban. Jelentkezzen be Microsoft Azure rendszergazdaként. A Microsoft Azure Portal kattintson az **Azure Active Directory** elemre. A **Kezelés** területen kattintson a **Tulajdonságok** elemre. A bérlő azonosítója a **címtár-azonosító** mezőben látható.
    ![Azure Active Directory bérlői AZONOSÍTÓjának megkeresése](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Mentse a konfigurációs fájlban végrehajtott módosításokat.
4.  Az eszköz elindításához futtassa a _StorSimple8000SeriesMigrationTool.exe_ . Amikor a rendszer kéri a hitelesítő adatokat, adja meg az előfizetéséhez tartozó hitelesítő adatokat Azure Portalban. 
5.  Megjelenik a StorSimple áttelepítési eszköz felhasználói felülete.
  

## <a name="next-steps"></a>Következő lépések
Töltse le az [adatok StorSimple 5000-7000-sorozatból egy 8000 sorozatú eszközre történő áttelepítésének](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf)lépésenkénti útmutatóját.
