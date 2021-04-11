---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075581"
---
A szolgáltatásban tárolt adatok titkosítási kulcsa a StorSimple Manager szolgáltatásból a StorSimple-eszközre továbbított bizalmas ügyféladatok, például a Storage-fiók hitelesítő adatainak titkosítására szolgál. Ezeket a kulcsokat rendszeresen módosítania kell, ha az informatikai szervezet kulcsfontosságú rotációs szabályzattal rendelkezik a tárolóeszközökön. A kulcs módosításának folyamata némileg eltérő lehet attól függően, hogy van-e egyetlen eszköz vagy több, a StorSimple Manager szolgáltatás által felügyelt eszköz. További információért látogasson el a [StorSimple biztonság és adatvédelem](../articles/storsimple/storsimple-8000-security.md)témakörre.

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