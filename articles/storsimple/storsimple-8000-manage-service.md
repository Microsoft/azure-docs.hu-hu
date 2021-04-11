---
title: A StorSimple Eszközkezelő szolgáltatás üzembe helyezése az Azure-ban | Microsoft Docs
description: Ismerje meg a szolgáltatás létrehozásához, törléséhez, áttelepítéséhez és a szolgáltatás regisztrációs kulcsának felügyeletéhez szükséges lépéseket.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076564"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>A StorSimple Eszközkezelő szolgáltatás üzembe helyezése a StorSimple 8000 Series-eszközökön

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple-eszközhöz csatlakozik. A szolgáltatás létrehozása után a segítségével kezelheti a StorSimple Eszközkezelő szolgáltatáshoz kapcsolódó összes eszközt egyetlen központi helyről, így minimalizálva az adminisztrációs terheket.

Ez az oktatóanyag a szolgáltatás létrehozásához, törléséhez, áttelepítéséhez és a szolgáltatás regisztrációs kulcsának felügyeletéhez szükséges lépéseket ismerteti. A cikkben szereplő információk csak a StorSimple 8000 Series-eszközökre vonatkoznak. A virtuális tömbök StorSimple kapcsolatos további információkért látogasson el a [StorSimple Eszközkezelő szolgáltatás üzembe helyezése a StorSimple virtuális tömbhöz](storsimple-virtual-array-manage-service.md)című témakörben.

> [!NOTE]
> -  A Azure Portal a 5,0-es vagy újabb frissítést futtató eszközöket támogatja. Ha az eszköz nem naprakész, telepítse azonnal az 5. frissítést. További információkért keresse fel az [5. frissítés telepítése](storsimple-8000-install-update-5.md)című témakört. 
> - Ha StorSimple Cloud Appliance (8010/8020) használ, a felhőalapú berendezés nem frissíthető. A szoftver legújabb verziójával hozzon létre egy új felhőalapú készüléket a 5,0-es frissítéssel, majd végezze el a feladatátvételt a létrehozott új felhőalapú berendezéssel. 
> - Az 4,0-es vagy korábbi frissítést futtató összes eszköz kevesebb felügyeleti funkciót fog tapasztalni. 

## <a name="create-a-service"></a>Szolgáltatás létrehozása
StorSimple Eszközkezelő szolgáltatás létrehozásához a következőkre van szükség:

* Előfizetés Nagyvállalati Szerződés
* Aktív Microsoft Azure Storage-fiók
* A hozzáférés-kezeléshez használt számlázási adatok

Csak Nagyvállalati Szerződés rendelkező előfizetések engedélyezettek. Azt is megteheti, hogy létrehoz egy alapértelmezett Storage-fiókot a szolgáltatás létrehozásakor.

Egyetlen szolgáltatás több eszközt is kezelhet. Az eszközök azonban nem terjedhetnek ki több szolgáltatásra. A nagyméretű vállalatok több szolgáltatási példánnyal is dolgozhatnak különböző előfizetésekkel, szervezetekkel vagy akár üzembe helyezési helyekkel. 

> [!NOTE]
> A StorSimple 8000 sorozatú eszközök és a StorSimple virtuális tömbök felügyeletéhez külön StorSimple Eszközkezelő szolgáltatásra van szükség.

Egy szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Az egyes StorSimple Eszközkezelő szolgáltatásokhoz a következő attribútumok léteznek:

* **Név** – a StorSimple Eszközkezelő szolgáltatáshoz hozzárendelt név a létrehozáskor. **A szolgáltatás neve nem módosítható a szolgáltatás létrehozása után. Ez más entitások, például eszközök, kötetek, kötetek tárolók és biztonsági mentési házirendek esetében is igaz, amelyeket nem lehet átnevezni a Azure Portal.**
* **Status (állapot** ) – a szolgáltatás állapota, amely lehet **aktív**, **létrehozható** vagy **online**.
* **Location (hely** ) – az a földrajzi hely, ahol a StorSimple-eszköz telepítve lesz.
* **Előfizetés** – a szolgáltatáshoz társított számlázási előfizetés.

## <a name="delete-a-service"></a>Szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem használja a csatlakoztatott eszközöket. Ha a szolgáltatás használatban van, inaktiválja a csatlakoztatott eszközöket. Az inaktiválási művelet megszakítja a kapcsolatot az eszköz és a szolgáltatás között, de megőrzi az eszközön tárolt összes eszközt a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlése után a művelet nem vonható vissza. A szolgáltatást használó bármely eszközt vissza kell állítani a gyári alapértékekre, mielőtt más szolgáltatással is használható lenne. Ebben a forgatókönyvben az eszközön található helyi adatértékek, valamint a konfiguráció elvesznek.

A szolgáltatás törléséhez hajtsa végre az alábbi lépéseket.

### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Keresse meg a törölni kívánt szolgáltatást. Kattintson az **erőforrások** ikonra, majd adja meg a megfelelő kifejezéseket a kereséshez. A keresési eredmények között kattintson a törölni kívánt szolgáltatásra.

    ![A törlendő keresési szolgáltatás](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Ekkor megjelenik a StorSimple Eszközkezelő szolgáltatás paneljén. Kattintson a **Törlés** gombra.

    ![Szolgáltatás törlése](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. A megerősítő értesítésben kattintson az **Igen** gombra. A szolgáltatás törléséhez néhány percet is igénybe vehet.

    ![Törlés megerősítése](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése

Miután sikeresen létrehozott egy szolgáltatást, regisztrálnia kell a StorSimple-eszközt a szolgáltatásban. Az első StorSimple-eszköz regisztrálásához szüksége lesz a szolgáltatás regisztrációs kulcsára. Ha további eszközöket szeretne regisztrálni egy meglévő StorSimple-szolgáltatással, akkor a regisztrációs kulcsra és a szolgáltatás adattitkosítási kulcsára is szükség van (amely az első eszközön jön létre a regisztráció során). A szolgáltatási adatok titkosítási kulcsával kapcsolatos további információkért lásd: [StorSimple biztonság](storsimple-8000-security.md). A regisztrációs kulcsot a StorSimple Eszközkezelő paneljén található **kulcsok** elérésével szerezheti be.

A szolgáltatás regisztrációs kulcsának beszerzéséhez hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

A szolgáltatás regisztrációs kulcsának biztonságos helyen kell maradnia. Erre a kulcsra, valamint a szolgáltatás adattitkosítási kulcsára lesz szüksége a szolgáltatáshoz tartozó további eszközök regisztrálásához. A szolgáltatás regisztrációs kulcsának beszerzése után az eszközt az Windows PowerShell StorSimple-bővítménye felületen keresztül kell konfigurálnia.

A regisztrációs kulcs használatáról további részleteket a [3. lépés: az eszköz konfigurálása és regisztrálása a Windows PowerShell StorSimple-bővítménye használatával](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)című témakörben talál.

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újbóli előállítása
A szolgáltatás regisztrációs kulcsának újralétrehozásához szükség van a kulcs elforgatásának elvégzésére, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. A kulcs újralétrehozásakor az új kulcs csak a további eszközök regisztrálására szolgál. A folyamat nem érinti a már regisztrált eszközöket.

A szolgáltatás regisztrációs kulcsának újralétrehozásához hajtsa végre az alábbi lépéseket.

### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának előállítása
1. A **StorSimple Eszközkezelő** panelen lépjen a **felügyeleti &gt;** **kulcsok** elemre.
    
    ![Ugrás a kulcsok panelre](./media/storsimple-8000-manage-service/regenregkey2.png)

2. A **kulcsok** panelen kattintson az **újragenerálás** elemre.

    ![Kattintson az újragenerált elemre.](./media/storsimple-8000-manage-service/regenregkey3.png)
3. A **szolgáltatás regisztrációs kulcsának újragenerálása** panelen tekintse át a kulcsok újragenerálása esetén szükséges műveletet. A szolgáltatásban regisztrált összes további eszköz az új regisztrációs kulcsot használja. A megerősítéshez kattintson az **újbóli létrehozás** gombra. Az újragenerálás befejezése után értesítést kap.

    ![Újragenerált megerősítés megerősítése](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Ekkor megjelenik egy új szolgáltatás regisztrációs kulcsa.

5. Másolja ezt a kulcsot, és mentse a szolgáltatáshoz tartozó új eszközök regisztrálásához.



## <a name="change-the-service-data-encryption-key"></a>A szolgáltatásban tárolt adattitkosítási kulcs módosítása
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Az 5,0-es frissítés előtti verziót futtató eszközökön támogatott műveletek
A Azure Portal csak a 5,0-es vagy újabb frissítést futtató StorSimple-eszközök támogatottak. A régebbi verziókat futtató eszközök korlátozott támogatást kapnak. Miután áttelepítette a Azure Portalra, a következő táblázat segítségével megismerheti, hogy mely műveletek támogatottak a 5,0-es frissítés előtti verziókban.

| Művelet                                                                                                                       | Támogatott      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Eszköz regisztrálása                                                                                                               | Yes            |
| Az eszközbeállítások, például az általános, a hálózat és a biztonság konfigurálása                                                                | Yes            |
| Frissítések vizsgálata, letöltése és telepítése                                                                                             | Yes            |
| Eszköz inaktiválása                                                                                                               | Yes            |
| Eszköz törlése                                                                                                                   | Yes            |
| Mennyiségi tároló létrehozása, módosítása és törlése                                                                                   | No             |
| Kötet létrehozása, módosítása és törlése                                                                                             | No             |
| Biztonsági mentési szabályzat létrehozása, módosítása és törlése                                                                                      | No             |
| Manuális biztonsági mentés készítése                                                                                                            | No             |
| Ütemezett biztonsági mentés készítése                                                                                                         | Nem alkalmazható |
| Visszaállítás egy biztonságimásolat                                                                                                        | No             |
| Klónozás egy 3,0-es vagy újabb frissítést futtató eszközre <br> A forrásoldali eszköz a 3,0-es frissítés előtti verziót futtatja.                                | Yes            |
| Klónozás egy, a 3,0-es frissítés előtti verziót futtató eszközre                                                                          | No             |
| Feladatátvétel forrásként szolgáló eszközként <br> (egy, a 3,0-es frissítés előtt futó eszközről egy 3,0-es vagy újabb verziót futtató eszközre)                                                               | Yes            |
| Feladatátvétel célként megadott eszközként <br> (a 3,0-es frissítés előtti verziót futtató eszközre)                                                                                   | No             |
| Riasztás törlése                                                                                                                  | Yes            |
| Biztonsági mentési szabályzatok, biztonsági mentési katalógus, kötetek, mennyiségi tárolók, figyelési diagramok, feladatok és a klasszikus portálon létrehozott riasztások megtekintése | Yes            |
| Az eszközök vezérlőinek be-és kikapcsolása                                                                                              | Igen            |


## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple üzembe helyezési folyamatáról](storsimple-8000-deployment-walkthrough-u2.md).
* További információ [a StorSimple Storage-fiók kezeléséről](storsimple-8000-manage-storage-accounts.md).
* További információ arról, hogyan [használható a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).
