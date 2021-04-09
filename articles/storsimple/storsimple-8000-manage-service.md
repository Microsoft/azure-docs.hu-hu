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
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360699"
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
A szolgáltatásban tárolt adatok titkosítási kulcsa a StorSimple Manager szolgáltatásból a StorSimple-eszközre továbbított bizalmas ügyféladatok, például a Storage-fiók hitelesítő adatainak titkosítására szolgál. Ezeket a kulcsokat rendszeresen módosítania kell, ha az informatikai szervezet kulcsfontosságú rotációs szabályzattal rendelkezik a tárolóeszközökön. A kulcs módosításának folyamata némileg eltérő lehet attól függően, hogy van-e egyetlen eszköz vagy több, a StorSimple Manager szolgáltatás által felügyelt eszköz. További információért látogasson el a [StorSimple biztonság és adatvédelem](storsimple-8000-security.md)témakörre.

A szolgáltatás adattitkosítási kulcsának módosítása egy 3 lépésből álló folyamat:

1. A Azure Resource Manager Windows PowerShell-parancsfájljainak használatával engedélyezze az eszköz számára a szolgáltatás adattitkosítási kulcsának módosítását.
2. A Windows PowerShell StorSimple-bővítménye használatával kezdeményezzen a szolgáltatás adattitkosítási kulcsának módosítását.
3. Ha egynél több StorSimple-eszközzel rendelkezik, frissítse a szolgáltatásban tárolt adattitkosítási kulcsot a többi eszközön.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. lépés: Windows PowerShell-parancsfájl használata az eszköz a szolgáltatás adattitkosítási kulcsának módosítására
Az eszköz rendszergazdája általában azt kéri, hogy a szolgáltatás rendszergazdája engedélyezzen egy eszközt a szolgáltatásban tárolt adattitkosítási kulcsok módosításához. A szolgáltatás rendszergazdája ezt követően engedélyezi az eszköz számára a kulcs módosítását.

Ezt a lépést a Azure Resource Manager-alapú parancsfájl használatával hajtja végre. A szolgáltatás-rendszergazda kijelölhet olyan eszközt, amely jogosult a hitelesítésre. Az eszköz ezután engedélyt kap a szolgáltatás adattitkosítási kulcsának módosítási folyamatának elindítására. 

A parancsfájl használatával kapcsolatos további információkért nyissa meg a [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Mely eszközök számára lehet engedélyezni a szolgáltatás adattitkosítási kulcsainak módosítását?
Az eszköznek meg kell felelnie a következő feltételeknek, mielőtt engedélyt kaphat a szolgáltatás adattitkosítási kulcsának megváltoztatására:

* Az eszköznek online állapotban kell lennie, hogy jogosult legyen a szolgáltatás adattitkosítási kulcsának módosítására.
* Ha a kulcs módosítása nem lett elindítva, 30 perc elteltével is engedélyezheti ugyanezt az eszközt.
* Egy másik eszközt is engedélyezhet, ha a korábban engedélyezett eszköz nem kezdeményezte a kulcs módosítását. Az új eszköz engedélyezése után a régi eszköz nem tudja elindítani a változást.
* Az eszköz nem engedélyezhető, amíg a szolgáltatás adattitkosítási kulcsának továbbítása folyamatban van.
* Engedélyezheti az eszközt, ha a szolgáltatásban regisztrált eszközök némelyike már a titkosítást használja, míg mások nem. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. lépés: a szolgáltatásban tárolt adattitkosítási kulcs módosításának elindításához Windows PowerShell StorSimple-bővítménye használata
Ezt a lépést a StorSimple-eszköz Windows PowerShell StorSimple-bővítménye felületén hajtja végre.

> [!NOTE]
> A StorSimple Manager szolgáltatás Azure Portal nem hajtható végre művelet, amíg a kulcs átváltása be nem fejeződik.


Ha az eszköz soros konzolját használja a Windows PowerShell felületéhez való kapcsolódáshoz, hajtsa végre az alábbi lépéseket.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>A szolgáltatásban tárolt adattitkosítási kulcs módosításának kezdeményezése
1. Válassza az 1. lehetőséget a teljes hozzáféréssel való bejelentkezéshez.
2. A parancssorba írja be a következőt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. A parancsmag sikeres befejeződése után egy új szolgáltatás-adattitkosítási kulcs jelenik meg. Másolja és mentse ezt a kulcsot a folyamat 3. lépésében való használatra. Ezt a kulcsot fogja használni a StorSimple Manager szolgáltatásban regisztrált összes fennmaradó eszköz frissítéséhez.
   
   > [!NOTE]
   > Ezt a folyamatot a StorSimple-eszközök engedélyezését négy órán belül kell kezdeményezni.
   > 
   > 
   
   Ezt követően az új kulcsot elküldi a szolgáltatásnak, hogy a szolgáltatásban regisztrált összes eszközre lekerüljön. Ekkor egy riasztás jelenik meg a szolgáltatás irányítópultján. A szolgáltatás letiltja az összes műveletet a regisztrált eszközökön, és az eszköz rendszergazdájának frissítenie kell a szolgáltatásban tárolt adatok titkosítási kulcsát a többi eszközön. Az I/o-t (a felhőbe adatokat küldő gazdagépeket) azonban nem lehet megszakítani.
   
   Ha egyetlen eszköz regisztrálva van a szolgáltatásban, akkor az átváltási folyamat befejeződött, és kihagyhatja a következő lépést. Ha több eszköz van regisztrálva a szolgáltatásban, folytassa a 3. lépéssel.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. lépés: a szolgáltatásban tárolt adattitkosítási kulcs frissítése más StorSimple-eszközökön
Ezeket a lépéseket a StorSimple-eszköz Windows PowerShell-felületén kell végrehajtani, ha több eszköz van regisztrálva a StorSimple Manager szolgáltatáshoz. A 2. lépésben beszerzett kulcsot a StorSimple Manager szolgáltatásban regisztrált összes fennmaradó StorSimple-eszköz frissítésére kell használni.

Hajtsa végre a következő lépéseket a szolgáltatás adattitkosításának frissítéséhez az eszközön.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>A szolgáltatásban tárolt adattitkosítási kulcs frissítése fizikai eszközökön
1. A-konzolhoz való kapcsolódáshoz használja a Windows PowerShell StorSimple-bővítménye. Válassza az 1. lehetőséget a teljes hozzáféréssel való bejelentkezéshez.
2. A parancssorba írja be a következőt:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Adja meg a szolgáltatás adattitkosítási kulcsának [módosítását a 2. lépés: Windows PowerShell StorSimple-bővítménye használata című témakörben](#to-initiate-the-service-data-encryption-key-change)leírtak szerint.

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>A szolgáltatásban tárolt adattitkosítási kulcs frissítése a 8010/8020-es felhőalapú készülékeken
1. Töltse le és állítsa be [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-szkriptet. 
2. Nyissa meg a PowerShellt, és a parancssorba írja be a következőt:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ez a szkript gondoskodik arról, hogy a Service-adattitkosítási kulcs az Eszközkezelőben az összes 8010/8020 felhőalapú készüléken be legyen állítva.

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
