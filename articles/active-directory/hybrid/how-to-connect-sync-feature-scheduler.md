---
title: 'Azure AD Connect Sync: Scheduler | Microsoft Docs'
description: Ez a témakör a Azure AD Connect Sync beépített Scheduler szolgáltatását ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084736"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Az Azure AD Connect szinkronizálása: ütemező
Ez a témakör a Azure AD Connect Sync (Sync Engine) beépített feladatütemezőjét ismerteti.

Ez a szolgáltatás a build 1.1.105.0 (2016. február) jelent meg.

## <a name="overview"></a>Áttekintés
Azure AD Connect szinkronizálás szinkronizálja a helyszíni címtárban bekövetkező változásokat a Scheduler használatával. Két ütemező folyamat létezik, egyet a jelszó-szinkronizáláshoz, egy másikat az objektum/attribútum szinkronizálásához és a karbantartási feladatokhoz. Ez a témakör az utóbbit ismerteti.

A korábbi kiadásokban az objektumok és attribútumok ütemező kívül volt a Szinkronizáló motoron. A szinkronizálási folyamat elindításához a Windows Feladatütemezőt vagy egy külön Windows-szolgáltatást használt. Az ütemező a 1,1-es verzió beépített változatával rendelkezik, és lehetővé teszi bizonyos testreszabást. Az új alapértelmezett szinkronizálási gyakoriság 30 perc.

Az ütemező feladata két feladat:

* **Szinkronizálási ciklus**. A módosítások importálására, szinkronizálására és exportálására szolgáló folyamat.
* **Karbantartási feladatok**. Új kulcsok és tanúsítványok megújítása a jelszó-visszaállításhoz és az eszköz regisztrációs szolgáltatásához (DRS). A régi bejegyzések kiürítése az operatív naplóban.

Maga az ütemező mindig fut, de úgy is konfigurálható, hogy csak egy vagy több feladatot futtasson. Ha például saját szinkronizálási ciklusra van szüksége, letilthatja ezt a feladatot az ütemező alkalmazásban, de továbbra is futtathatja a karbantartási feladatot.

>[!IMPORTANT]
>Alapértelmezés szerint 30 percenként egy szinkronizálási ciklus fut. Ha módosította a szinkronizálási ciklust, meg kell győződnie arról, hogy a szinkronizálási ciklus legalább 7 naponta fut. 
>
>* A különbözeti szinkronizálásnak az utolsó Delta szinkronizálástól számított 7 napon belül kell történnie.
>* Az utolsó teljes szinkronizálás befejezésének időpontjától számított 7 napon belül meg kell történnie a különbözeti szinkronizálásnak (teljes szinkronizálást követően).
>
>Ha ezt elmulasztja, szinkronizálási problémák léphetnek fel, ami megköveteli, hogy teljes szinkronizálást futtasson a megoldáshoz. Ez az átmeneti módban lévő kiszolgálókra is vonatkozik.

## <a name="scheduler-configuration"></a>Ütemező konfigurálása
Az aktuális konfigurációs beállítások megtekintéséhez nyissa meg a PowerShellt, és futtassa a parancsot `Get-ADSyncScheduler` . A következőhöz hasonló képet mutat:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Ha a parancsmag futtatásakor **a szinkronizálási parancs vagy a parancsmag nem érhető el** , akkor a PowerShell-modul nincs betöltve. Ez a probléma akkor fordulhat elő, ha a Azure AD Connect tartományvezérlőn vagy az alapértelmezett beállításoknál magasabb PowerShell-korlátozási szinttel rendelkező kiszolgálón futtatja. Ha ezt a hibát látja, futtassa a parancsot a `Import-Module ADSync` parancsmag elérhetővé tételéhez.

* **AllowedSyncCycleInterval**. Az Azure AD által engedélyezett szinkronizálási ciklusok közötti legrövidebb időtartam. Ennél a beállításnál gyakrabban nem lehet szinkronizálni, és továbbra is támogatott.
* **CurrentlyEffectiveSyncCycleInterval**. Az ütemterv jelenleg érvényben van. Ez az érték megegyezik a CustomizedSyncInterval (ha be van állítva), ha az nem gyakoribb, mint a AllowedSyncInterval. Ha a 1.1.281 előtt hoz létre egy buildet, és megváltoztatja a CustomizedSyncCycleInterval, ez a változás a következő szinkronizálási ciklus után lép érvénybe. A build 1.1.281 a módosítás azonnal érvénybe lép.
* **CustomizedSyncCycleInterval**. Ha azt szeretné, hogy az ütemező az alapértelmezett 30 percnél más gyakorisággal fusson, ezt a beállítást kell beállítania. A fenti képen az ütemező úgy van beállítva, hogy minden órában fusson. Ha ezt a beállítást a AllowedSyncInterval alacsonyabb értékre állítja, akkor a rendszer az utóbbit használja.
* **NextSyncCyclePolicyType**. Akár Delta, akár kezdeti. Meghatározza, hogy a következő Futtatás csak a különbözeti változásokat dolgozza fel, vagy ha a következő futtatásnak teljes importálást és szinkronizálást kell végeznie. Az utóbbi az új vagy módosított szabályokat is újra feldolgozza.
* **NextSyncCycleStartTimeInUTC**. Amikor a Feladatütemező legközelebb elindítja a következő szinkronizálási ciklust.
* **PurgeRunHistoryInterval**. Meg kell őrizni az idő műveleti naplóit. Ezeket a naplókat a szinkronizációs szolgáltatás kezelőjében lehet megtekinteni. Alapértelmezés szerint a naplókat 7 napig kell megőrizni.
* **SyncCycleEnabled**. Azt jelzi, hogy az ütemező futtatja-e az importálási, szinkronizálási és exportálási folyamatokat a művelet részeként.
* **MaintenanceEnabled**. Azt mutatja, hogy engedélyezve van-e a karbantartási folyamat. Frissíti a tanúsítványokat és a kulcsokat, és kiüríti az operatív naplót.
* **StagingModeEnabled**. Azt mutatja, hogy engedélyezve van-e az [átmeneti üzemmód](how-to-connect-sync-staging-server.md) . Ha ez a beállítás engedélyezve van, a rendszer letiltja az exportálást, de továbbra is futtatja az importálást és a szinkronizálást.
* **SchedulerSuspended**. A frissítés során állítsa be a csatlakozást, hogy ideiglenesen blokkolja az ütemező futtatását.

Ezeket a beállításokat megváltoztathatja a következővel: `Set-ADSyncScheduler` . A következő paraméterek módosíthatók:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Azure AD Connect korábbi verzióiban a **isStagingModeEnabled** a set-ADSyncScheduler-ben volt elérhető. A tulajdonság beállítása nem **támogatott** . A **SchedulerSuspended** tulajdonságot csak a kapcsolattal kell módosítani. Nem **támogatott** a PowerShell közvetlen beállításával.

Az ütemező konfigurációját az Azure AD tárolja. Ha átmeneti kiszolgálóval rendelkezik, az elsődleges kiszolgáló változásai az átmeneti kiszolgálót is érintik (kivéve a IsStagingModeEnabled-t).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Szintaxis `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-nap, óó-óra, mm-perc, SS-másodperc

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Az ütemező úgy módosul, hogy 3 óránként fusson.

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
A módosítások az ütemező napi futtatását módosítják.

### <a name="disable-the-scheduler"></a>Ütemező letiltása  
Ha módosítania kell a konfigurációt, akkor le kívánja tiltani a Feladatütemezőt. Ha például [Beállítja a szűrést](how-to-connect-sync-configure-filtering.md) , vagy [módosítja a szinkronizálási szabályokat](how-to-connect-sync-change-the-configuration.md).

Az ütemező letiltásához futtassa a parancsot `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Ütemező letiltása](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

A módosítások elvégzése után ne felejtse el újból engedélyezni az ütemező szolgáltatását `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Az ütemező elindítása
Az ütemező alapértelmezés szerint 30 percenként fut. Bizonyos esetekben előfordulhat, hogy szinkronizálási ciklust szeretne futtatni az ütemezett ciklusok között, vagy egy másik típust kell futtatnia.

### <a name="delta-sync-cycle"></a>Különbözeti szinkronizálási ciklus
A különbözeti szinkronizálási ciklus a következő lépéseket tartalmazza:


- Különbözeti Importálás az összes összekötőn
- Különbözeti szinkronizálás az összes összekötőn
- Exportálás az összes összekötőn

### <a name="full-sync-cycle"></a>Teljes szinkronizálási ciklus
A teljes szinkronizálási ciklus a következő lépéseket tartalmazza:

- Teljes importálás minden összekötőn
- Teljes szinkronizálás minden összekötőn
- Exportálás az összes összekötőn

Előfordulhat, hogy sürgős változásra van szükség, amelyet azonnal szinkronizálni kell, ezért a ciklust manuálisan kell futtatni. 

Ha manuálisan kell futtatnia egy szinkronizálási ciklust, akkor a PowerShell futtatásával `Start-ADSyncSyncCycle -PolicyType Delta` .

Teljes szinkronizálási ciklus kezdeményezéséhez futtassa a parancsot `Start-ADSyncSyncCycle -PolicyType Initial` egy PowerShell-parancssorból.   

A teljes szinkronizálási ciklus futtatása nagyon időigényes lehet, olvassa el a következő szakaszt, és olvassa el, hogyan optimalizálhatja ezt a folyamatot.

### <a name="sync-steps-required-for-different-configuration-changes"></a>A különböző konfigurációs változásokhoz szükséges szinkronizálási lépések
A különböző konfigurációs változások különböző szinkronizálási lépéseket igényelnek annak biztosításához, hogy a módosítások megfelelően legyenek alkalmazva az összes objektumra.

- További objektumok vagy attribútumok hozzáadása a forrás-címtárból (a szinkronizálási szabályok hozzáadásával/módosításával)
    - A forrás-könyvtárhoz tartozó összekötőn teljes importálás szükséges
- Módosítások történtek a szinkronizálási szabályokban
    - A módosított szinkronizálási szabályokhoz az összekötőn teljes szinkronizálás szükséges
- Módosult a [szűrés](how-to-connect-sync-configure-filtering.md) , hogy a rendszer különböző számú objektumot tartalmazzon.
    - Minden egyes AD-összekötő esetében teljes importálásra van szükség az összekötőn, kivéve, ha attribútum-alapú szűrést használ a szinkronizálási motorba már importált attribútumok alapján.

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>A szinkronizálási ciklusok testreszabása a Delta és a teljes szinkronizálás lépéseinek megfelelő kombinációját futtatja
A teljes szinkronizálási ciklus futtatásának elkerülése érdekében a következő parancsmagok segítségével megadhatja az egyes összekötőket, hogy teljes lépést futtasson.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Példa: Ha módosította az "AD erdő" összekötőhöz tartozó szinkronizálási szabályokat, amelyek nem igényelnek új attribútumokat, akkor a következő parancsmagok futtatásával kell futtatnia a különbözeti szinkronizálási ciklust, amely az összekötő teljes szinkronizálási lépését is elvégezte.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Példa: Ha módosította az "AD-erdő" összekötőhöz tartozó szinkronizálási szabályokat, hogy most új attribútumot kelljen importálnia, akkor a következő parancsmagok futtatásával futtasson egy különbözeti szinkronizálási ciklust, amely a teljes importálást is elvégezte az összekötő teljes szinkronizálási lépésével.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Ütemező leállítása
Ha az ütemező jelenleg szinkronizálási ciklust futtat, lehet, hogy le kell állítania. Ha például elindítja a telepítővarázslót, és a következő hibaüzenet jelenik meg:

![Képernyőfelvétel: a konfigurációs hibaüzenet nem módosítható.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Ha egy szinkronizálási ciklus fut, nem végezheti el a konfigurációs módosításokat. Megvárhatja, amíg az ütemező befejezte a folyamatot, de leállíthatja azt is, hogy azonnal el tudja végezni a módosításokat. Az aktuális ciklus leállítása nem ártalmas, és a függőben lévő módosítások a következő futtatással lesznek feldolgozva.

1. Először is mondja el, hogy az ütemező leállítja a jelenlegi ciklust a PowerShell-parancsmaggal `Stop-ADSyncSyncCycle` .
2. Ha 1.1.281 előtti buildet használ, akkor az ütemező leállítása nem állítja le az aktuális összekötőt az aktuális feladatból. Az összekötő leállításának kényszerítéséhez végezze el a következő műveleteket:

   ![Képernyőfelvétel: Synchronization Service Manager kiválasztott összekötők és a kijelölt leállítás művelettel jelölt futó összekötő.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Indítsa el a **szinkronizálási szolgáltatást** a Start menüből. Nyissa meg az **Összekötők** elemet, jelölje ki az összekötőt a-t **futtató** állapottal, majd válassza a **Leállítás** lehetőséget a műveletek közül.

Az ütemező továbbra is aktív, és a következő lehetőségnél újra elindul.

## <a name="custom-scheduler"></a>Egyéni ütemező
Az ebben a szakaszban leírt parancsmagok csak a Build [1.1.130.0](reference-connect-version-history.md) és újabb verziókban érhetők el.

Ha a beépített ütemező nem felel meg a követelményeknek, akkor a PowerShell használatával ütemezheti az összekötőket.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Az összekötő profilját így is elindíthatja:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Az [összekötő-nevekhez](how-to-connect-sync-service-manager-ui-connectors.md) és a [profilok futtatásához](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) használandó nevek a [synchronization Service Manager felhasználói felületen](how-to-connect-sync-service-manager-ui.md)találhatók.

![Futtatási profil meghívása](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

A `Invoke-ADSyncRunProfile` parancsmag szinkronban van, azaz nem ad vissza vezérlőelemet, amíg az összekötő nem végezte el a műveletet, vagy ha a művelet sikertelen volt, vagy hiba történt.

Az összekötők ütemezhetik, hogy a következő sorrendben ütemezze őket:

1. (Teljes/Delta) Importálás helyszíni címtárakból, például Active Directory
2. (Teljes/Delta) Importálás az Azure AD-ből
3. (Teljes/Delta) Szinkronizálás helyszíni címtárakból, például Active Directory
4. (Teljes/Delta) Szinkronizálás az Azure AD-ből
5. Exportálás az Azure AD-be
6. Exportálás helyi címtárakba, például Active Directory

Ez a sorrend azt ismerteti, hogy a beépített ütemező hogyan futtatja az összekötőket.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
A szinkronizálási motor figyelésével ellenőrizheti, hogy foglalt vagy üresjáratban van-e. Ez a parancsmag üres eredményt ad vissza, ha a Szinkronizáló motor üresjáratban van, és nem fut összekötő. Ha egy összekötő fut, akkor az összekötő nevét adja vissza.

```
Get-ADSyncConnectorRunStatus
```

![Összekötő futtatási állapota](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
A fenti képen az első sor olyan állapotból származik, amelyben a Szinkronizáló motor inaktív. A második sor az Azure AD-összekötő futtatásakor.

## <a name="scheduler-and-installation-wizard"></a>Ütemező és telepítővarázsló
A telepítővarázsló indításakor az ütemező átmenetileg fel lesz függesztve. Ennek a viselkedésnek az az oka, hogy a konfiguráció módosításait feltételezi, és ezek a beállítások nem alkalmazhatók, ha a Szinkronizáló motor aktívan fut. Ezért ne hagyja meg a telepítővarázsló megnyitását, mert leállítja a szinkronizációs motort a szinkronizálási műveletek végrehajtásával.

## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
