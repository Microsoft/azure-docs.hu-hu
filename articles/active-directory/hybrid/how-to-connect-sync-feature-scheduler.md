---
title: 'Az Azure AD Connect szinkronizálása: A Scheduler |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálási beépített ütemezési funkció.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139294"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Az Azure AD Connect szinkronizálása: Scheduler
Ez a témakör ismerteti a beépített scheduler az Azure AD Connect-szinkronizálással (szinkronizálási motor).

Ez a funkció a build 1.1.105.0 (kiadás dátuma: 2016. február) jelent meg.

## <a name="overview"></a>Áttekintés
Az Azure AD Connect szinkronizálása a helyszíni címtárban, az ütemező bekövetkező változások szinkronizálása. Nincsenek két scheduler folyamatot, a egyet a jelszó-szinkronizálás és a egy másik eszközobjektum-attribútum szinkronizálása és karbantartási feladatokhoz. Ez a témakör ismerteti az utóbbi.

A korábbi kiadásokban az objektumok és attribútumok az ütemező volt a szinkronizálási motoron külső. Windows Feladatütemező vagy egy különálló Windows-szolgáltatás ezt használja a szinkronizálási folyamat indításához. Az ütemező az 1.1-es verziók beépített, a szinkronizálás a motor- és néhány testreszabási teszik lehetővé. Az új alapértelmezett szinkronizálási gyakoriság értéke 30 perc.

Az ütemező felelős két feladatot:

* **Szinkronizálási ciklust**. Az importálási folyamat szinkronizálása, és exportálni a módosításokat.
* **Karbantartási feladatok**. Újítsa meg a kulcsok és tanúsítványok a jelszó alaphelyzetbe állítása és az Eszközregisztrációs szolgáltatás (DRS). Törölje a régi bejegyzések a műveleti naplóban.

Az ütemező magát mindig fut, de csak az egyik vagy ezek a feladatok egyike futtatását is konfigurálható. Például ha rendelkezik a saját szinkronizálási ciklus folyamat van szüksége, akkor tiltsa le ezt a feladatot az ütemező az, de továbbra is futtassa a karbantartási feladatot.

## <a name="scheduler-configuration"></a>Feladatütemező konfigurációjához
A jelenlegi konfigurációs beállítások megtekintéséhez, keresse fel a PowerShell és a Futtatás `Get-ADSyncScheduler`. Jelzi, hogy a képen hasonló:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Ha látja **a szinkronizálási parancsot vagy a parancsmag nem érhető el** amikor futtatja a parancsmagot, majd a PowerShell-modul nincs betöltve. Ez a probléma akkor fordulhat elő, ha az Azure AD Connect egy tartományvezérlőn vagy PowerShell korlátozás magasabb szintű, mint az alapértelmezett beállításokat a kiszolgálón. Ha ezt a hibát látja, majd futtassa `Import-Module ADSync` , hogy a parancsmag elérhető.

* **AllowedSyncCycleInterval**. A legrövidebb közötti időintervallum szinkronizálási ciklust az Azure AD által engedélyezett. Ez a beállítás gyakrabban nem szinkronizálja, és továbbra is támogatott lesz.
* **CurrentlyEffectiveSyncCycleInterval**. Jelenleg a érvénybe az ütemezés. Stejnou hodnotu jako CustomizedSyncInterval rendelkezik (ha beállítása) Ha nem AllowedSyncInterval üzemezésnél gyakoribb. Ha egy build 1.1.281 előtt használja, és CustomizedSyncCycleInterval módosítja, a módosítás után a következő szinkronizálási ciklusban lép érvénybe. A build 1.1.281 a változás azonnal érvénybe lép.
* **CustomizedSyncCycleInterval**. Ha azt szeretné, hogy az ütemező a futtatását, hogy bármely más, mint az alapértelmezett gyakoriság 30 percet, majd ezt a beállítást konfigurálja. A fenti képen az ütemező a futni óránként van beállítva. Ha alacsonyabb, mint AllowedSyncInterval állítsa ezzel a beállítással értékre, majd az utóbbi használatos.
* **NextSyncCyclePolicyType**. A különbözeti vagy kezdeti. Meghatározza, hogy a következő futtatáskor kell csak a folyamat új módosításokkal, vagy ha a következő futtatáskor kell tennie egy teljes importálása és szinkronizálása. Az utóbbi akkor is újrafeldolgozása új vagy módosított szabályokat.
* **NextSyncCycleStartTimeInUTC**. Amikor legközelebb az ütemező indítja el a következő szinkronizálási ciklus.
* **PurgeRunHistoryInterval**. Az idő a műveletnaplók kell tárolni. Ezek a naplók a synchronization service Managert a tekinthető meg. Az alapértelmezett érték 7 napig ezek a naplók tárolása céljából.
* **SyncCycleEnabled**. Azt jelzi, hogy az Ütemező fut-e az importálás, szinkronizálási és exportálási folyamatok a művelet részeként.
* **MaintenanceEnabled**. Látható, hogy engedélyezve van-e a karbantartási folyamat. Ez frissíti a tanúsítványok vagy kulcsok és a műveleti napló kiürítése.
* **StagingModeEnabled**. Bemutatja, ha [átmeneti módban](how-to-connect-sync-staging-server.md) engedélyezve van. Ha ez a beállítás engedélyezve van, majd nem jelenít meg a kivitel futását, de továbbra is futtassa az importálás és a szinkronizálás.
* **SchedulerSuspended**. Állítsuk Connect a frissítés során ideiglenesen letiltása az ütemező futását.

Módosíthatja az egyes beállításokat a `Set-ADSyncScheduler`. Módosítható a következő paraméterekkel:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Az Azure AD Connect régebbi buildjei **isStagingModeEnabled** a Set-ADSyncScheduler elérhető volt. Ez **nem támogatott** , ezzel a tulajdonsággal. A tulajdonság **SchedulerSuspended** Connect csak módosíthatók. Ez **nem támogatott** közvetlenül be a PowerShell használatával.

A Feladatütemező beállítása az Azure ad-ben tárolt. Ha rendelkezik egy átmeneti kiszolgálón, az elsődleges kiszolgáló bármilyen módosítás befolyásolja az átmeneti kiszolgálót (kivéve a IsStagingModeEnabled) is.

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Szintaxis: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - nap, HH - Óra, pp - perc, mm – másodperc

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Módosítja az ütemező 3 óránként futtatásához.

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Megváltoztatása napi rendszerességgel ütemezik arra az ütemezőnek.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása  
Ha konfigurációs módosításokat kell, majd szeretné tiltani az ütemező. Például, amikor Ön [a szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) vagy [hajtsa végre a módosításokat a szinkronizálási szabályokon](how-to-connect-sync-change-the-configuration.md).

Az ütemező letiltásához futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Az ütemező letiltása](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Amikor végzett a módosításokat, ne felejtse el engedélyezni az ütemező szintén a `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>A Feladatütemező indítása
Az ütemező 30 percenként futtatása alapértelmezés szerint ki van. Bizonyos esetekben érdemes lehet ütemezett ciklusok között egy szinkronizálási ciklus futtatására, vagy egy más típusú futtatnia kell.

### <a name="delta-sync-cycle"></a>A különbözeti szinkronizálási ciklus
A különbözeti szinkronizálási ciklus a következő lépésekből áll:


- Különbözeti importálás az összes összekötő
- Különbözeti szinkronizálás az összes összekötő
- Exportálás az összes összekötő

### <a name="full-sync-cycle"></a>Teljes szinkronizálási ciklus
Egy teljes szinkronizálási ciklust a következő lépésekből áll:

- Teljes importálás az összes összekötő
- Teljes szinkronizálás az összes összekötő
- Exportálás az összes összekötő

Annak oka az lehet, hogy rendelkezik-e az sürgős megváltoztatására, amely szinkronizálva kell lenniük, azonnal, ezért egy ciklus manuálisan futtatnia kell. 

Ha manuálisan futtatnia kell egy szinkronizálási ciklust, majd a PowerShell futási `Start-ADSyncSyncCycle -PolicyType Delta`.

Ehhez futtassa a következő teljes szinkronizálási ciklusának kezdeményezésére, `Start-ADSyncSyncCycle -PolicyType Initial` PowerShell parancssorból.   

Egy teljes szinkronizálási ciklust futó nagyon időigényes lehet, olvassa el a következő szakaszban olvasható a folyamat optimalizálása.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Szinkronizálási teendők szükséges konfigurációs módosítások
Különböző konfigurációs módosítások különböző szinkronizálási lépéseket annak érdekében, hogy a megfelelő módosítások az összes objektum szükséges.

- További objektumok vagy attribútumok importálandó forráskönyvtárat (szinkronizálási szabályok hozzáadása vagy módosítása) szerint hozzáadva
    - Teljes importálást van szükség az összekötő a forráskönyvtár keresése
- A szinkronizálási szabályokon végrehajtott módosítások
    - A teljes szinkronizálás van szükség az összekötő a módosított szinkronizálási szabályok
- Módosított [szűrés](how-to-connect-sync-configure-filtering.md) így objektumok különböző számú tartalmaznia kell
    - Teljes importálást van szükség az összekötő minden AD-összekötő használata, a szinkronizálási motor már importált attribútumokon alapuló attribútum szerinti szűrés

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Futtassa a változások és a teljes szinkronizálás lépéseket megfelelő vegyesen szinkronizálási ciklust testreszabása
Ne futtasson egy teljes szinkronizálási ciklust a jelölheti meg az alábbi parancsmagokkal egy teljes lépés futtatásához egyedi összekötők.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Példa:  Ha módosította a szinkronizálási szabályok összekötő "AD-erdőhöz A" nem importálható, hogy új attribútumokat igénylő futtatná a következő parancsmagok futtatásához a különbözeti szinkronizálási ciklus melyik is tette a teljes szinkronizálás lépésnél az összekötőhöz.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Példa:  A módosítások a szinkronizálási szabályokon "AD-erdőhöz A"-összekötőhöz, hogy most már van szükségük a importálható, új attribútum futtatná a következő parancsmagok futtatásához egy különbözeti szinkronizálási ciklus, amely egy teljes importálást, a teljes szinkronizálás lépés annak is tette.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>A Feladatütemező leállítása
Ha az ütemező jelenleg fut egy szinkronizálási ciklust, szüksége lehet állítsa le. Például ha elindítja a telepítési varázslót, és ez a hibaüzenet:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Ha egy szinkronizálási ciklus fut, nem konfigurációmódosításokat. Sikerült megvárni, amíg az ütemező a folyamat befejeződött, de is leállíthatja, így a módosítások azonnal elvégezhetők. Az aktuális ciklus leállítása nem káros, függőben lévő módosítások feldolgozása a következő futtatáskor.

1. Indítsa el a Feladatütemező leállítása a PowerShell-parancsmaggal az aktuális ciklus kommunikációnkat `Stop-ADSyncSyncCycle`.
2. Ha 1.1.281 előtt egy buildet használ, majd leállítása az ütemező nem állítja le a jelenlegi feladat az aktuális összekötő. Az összekötő leállítani a kényszerítéséhez a következő műveleteket: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Indítsa el **szinkronizálási szolgáltatás** a start menüből. Lépjen a **összekötők**, jelölje ki az összekötő állapotát az **futó**, és válassza ki **leállítása** a műveletekből.

Az ütemező még mindig aktív, és ismét elindul a Tovább lehetőséget.

## <a name="custom-scheduler"></a>Egyéni ütemező
A parancsmagok részletes ismertetését lásd: Ez a szakasz csak érhetők el a build [1.1.130.0](reference-connect-version-history.md#111300) és újabb verziók.

Ha a beépített ütemezési nem felel meg a követelményeknek, majd ütemezhet a PowerShell-lel összekötőket.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Ezzel a módszerrel összekötőhöz megkezdése egy profil:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

A nevek [összekötők neveinek](how-to-connect-sync-service-manager-ui-connectors.md) és [futtassa a profilnév](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) tekintheti meg a [Synchronization Service Manager felhasználói felületén](how-to-connect-sync-service-manager-ui.md).

![Futtatási profil meghívása](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

A `Invoke-ADSyncRunProfile` parancsmag szinkron, azt jelenti, azt nem ad vissza vezérlő mindaddig, amíg az összekötő sikeresen vagy hibával befejeződött a műveletet.

Az összekötők ütemezésekor az ajánlás az, hogy ütemezni a következő sorrendben:

1. (Teljes vagy különbözeti) A helyszíni címtárakat, például az Active Directory importálása
2. (Teljes vagy különbözeti) Importálás az Azure ad-ben
3. (Teljes vagy különbözeti) A helyszíni címtárakat, például az Active Directory-szinkronizálás
4. (Teljes vagy különbözeti) Szinkronizálás az Azure ad-ből
5. Az Azure AD-exportálás
6. A helyszíni címtárakat, például az Active Directory exportálása

Ebben a sorrendben, az összekötők a beépített ütemezési működésével.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
A szinkronizálási motor foglalt vagy inaktív megtekintéséhez is figyelheti. A parancsmag üres értéket ad vissza, ha a szinkronizálási motor üresjáratban, és nem fut egy összekötőt. Ha egy összekötő fut, az összekötő nevét adja vissza.

```
Get-ADSyncConnectorRunStatus
```

![Összekötő futtatási állapota](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
A fenti képen az első sor olyan állapotban, amelyben a szinkronizálási motor üresjárati származik. A második sor az Azure AD Connectoron futtatásakor.

## <a name="scheduler-and-installation-wizard"></a>Ütemező és telepítővarázsló
Ha elindítja a telepítővarázslót, az ütemező ideiglenesen fel van függesztve. Ez a viselkedés azért, hogy a rendszer feltételezi, hogy a konfigurációs módosítások, és ezek a beállítások nem alkalmazható, ha a szinkronizálási motor aktívan fut-e. Ebből kifolyólag nem hagyja a telepítővarázsló nyitva leállítja a szinkronizálási motor a szinkronizálási műveletek végrehajtása óta.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
