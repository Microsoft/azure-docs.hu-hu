---
title: Rendszeres biztonsági mentési konfiguráció ismertetése
description: Az Service Fabric rendszeres biztonsági mentési és visszaállítási funkciójának használatával konfigurálhatja a megbízható állapot-nyilvántartó szolgáltatások vagy Reliable Actors rendszeres biztonsági mentését.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018885"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Az Azure Service Fabric rendszeres biztonsági mentési konfigurációjának ismertetése

A megbízható állapot-nyilvántartó szolgáltatások vagy Reliable Actors rendszeres biztonsági mentésének konfigurálása a következő lépésekből áll:

1. **Biztonsági mentési szabályzatok létrehozása**: ebben a lépésben a követelményektől függően egy vagy több biztonsági mentési szabályzat jön létre.

2. **Biztonsági mentés engedélyezése**: ebben a lépésben az **1. lépésben** létrehozott biztonsági mentési házirendeket rendeli hozzá a szükséges entitásokhoz, _alkalmazáshoz_, _szolgáltatáshoz_ vagy _partícióhoz_.

## <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat a következő konfigurációkból áll:

* **Automatikus visszaállítás adatvesztés** esetén: Megadja, hogy a rendszer automatikusan aktiválja-e a visszaállítást a legújabb elérhető biztonsági mentéssel, ha a partíció adatvesztési eseményt használ.
> [!NOTE]
> Azt javasoljuk, hogy ne állítson be automatikus visszaállítást üzemi fürtökben
>

* **Növekményes biztonsági mentések** maximális száma: meghatározza a két teljes biztonsági mentés közötti növekményes biztonsági mentések maximális számát. A növekményes biztonsági mentések maximális száma a felső korlátot határozza meg. A megadott számú növekményes biztonsági mentések a következő feltételek egyike után teljes biztonsági mentést végezhetnek.

    1. A replika soha nem készített teljes biztonsági mentést, mert az elsődleges.

    2. Néhány naplózási rekord a legutóbbi biztonsági mentés óta csonkolt.

    3. A replika átadotta a MaxAccumulatedBackupLogSizeInMB korlátot.

* **Biztonsági mentési ütemezés**: az időszakos biztonsági másolatok készítésének ideje vagy gyakorisága. Az egyik ütemezheti a biztonsági mentések ütemezését a megadott időközönként vagy napi/heti rögzített időtartamon keresztül.

    1. **Gyakoriság-alapú biztonsági mentési ütemterv**: ezt az ütemtervet akkor kell használni, ha az adatbiztonsági mentést rögzített időközönként kell végrehajtani. A két egymást követő biztonsági mentések közötti kívánt időintervallumot a ISO8601 formátuma határozza meg. A gyakoriság-alapú biztonsági mentési ütemterv az intervallum felbontását támogatja a percben.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Időalapú biztonsági mentési ütemterv**: ezt az ütemtervet akkor kell használni, ha az adatbiztonsági mentést a nap vagy hét adott időpontjában kell végrehajtani. Az ütemezett gyakoriság típusa lehet naponta vagy hetente.
        1. **_Napi_ időalapú biztonsági mentési ütemterv**: ezt az ütemtervet akkor kell használni, ha az adatbiztonsági mentést a nap adott időpontjában kell végrehajtani. Ha ezt szeretné megadni, `ScheduleFrequencyType` állítsa _napi_ értékre, és állítsa be a `RunTimes` kívánt idő listáját a nap folyamán a ISO8601 formátumban, a dátummal együtt megadott dátum figyelmen kívül lesz hagyva. Például a `0001-01-01T18:00:00` _6:00 PM_ mindennapi, figyelmen kívül hagyva a _0001-01-01_. Az alábbi példa azt szemlélteti, hogy a napi biztonsági mentést az _9:00_ -es és a _6:00_ -as nap minden nap elindítsa.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Heti_ időalapú biztonsági mentési ütemterv**: ezt az ütemtervet akkor kell használni, ha az adatbiztonsági mentést a nap adott időpontjában kell végrehajtani. Ennek megadásához állítsa be a következőt `ScheduleFrequencyType` : _Weekly_; állítsa be `RunDays` azt a napot egy héten belül, amikor a biztonsági mentést el kell indítani `RunTimes` , és a nap során a kívánt időt kell beállítani a ISO8601 formátumban, és az idővel megadott dátumot figyelmen kívül hagyja a rendszer. Egy hét napjainak listája az időszakos biztonsági mentés elindításához. Az alábbi példa azt szemlélteti, hogyan lehet a napi biztonsági mentést az _9:00_ -es és a _6:00_ -es időpontra kiváltani a hétfőtől péntekig.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Biztonsági mentési tár**: megadja a biztonsági másolatok feltöltésének helyét. A tárterület lehet Azure Blob Store vagy fájlmegosztás.
    1. **Azure Blob Store**: ezt a tárolási típust akkor kell kiválasztani, ha a létrehozott biztonsági mentéseket az Azure-ban kell tárolni. Az _önálló_ és az _Azure-alapú_ fürtök is használhatják ezt a tárolási típust. A tárolási típus leírásához a kapcsolati sztringre és annak a tárolónak a nevére van szükség, amelyben a biztonsági másolatokat fel kell tölteni. Ha a megadott nevű tároló nem érhető el, akkor a biztonsági mentés feltöltése során jön létre.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > A biztonsági mentési visszaállítási szolgáltatás nem működik a v1 Azure Storage-ban
        >

    2. **Fájlmegosztás**: ezt a tárolási típust _különálló_ fürtökhöz kell kiválasztani, ha az adatbiztonsági mentést a helyszínen kell tárolni. A tárolási típus leírásához meg kell adni a fájlmegosztás elérési útját, ahol a biztonsági másolatokat fel kell tölteni. A fájlmegosztás elérését az alábbi lehetőségek egyikével konfigurálhatja
        1. _Integrált Windows-hitelesítés_, ahol a fájlmegosztás elérését a Service Fabric fürthöz tartozó összes számítógép számára biztosítjuk. Ebben az esetben állítsa be a következő mezőket a _fájlmegosztás_ alapú biztonsági mentési tár konfigurálásához.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. A _fájlmegosztás védelme felhasználónévvel és jelszóval_, ahol a fájlmegosztás elérését meghatározott felhasználók számára biztosítjuk. A fájlmegosztás tárolási specifikációja lehetőséget biztosít a másodlagos Felhasználónév és a másodlagos jelszó megadására, hogy a rendszer visszaadja a hitelesítő adatokat, ha a hitelesítés meghiúsul az elsődleges felhasználónévvel és az elsődleges jelszóval. Ebben az esetben állítsa be a következő mezőket a _fájlmegosztás_ alapú biztonsági mentési tár konfigurálásához.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Győződjön meg arról, hogy a tárolási megbízhatóság megfelel vagy meghaladja a biztonsági mentési adat megbízhatósági követelményeit.
>

* **Adatmegőrzési szabály**: a konfigurált tárolóban lévő biztonsági másolatok megőrzésére vonatkozó házirendet határozza meg. Csak az alapszintű adatmegőrzési szabályok támogatottak.
    1. **Alapszintű adatmegőrzési szabály**: ez az adatmegőrzési szabály lehetővé teszi az optimális tárterület-kihasználtság biztosítását a nem szükséges biztonsági mentési fájlok eltávolításával. `RetentionDuration` megadható azt az időtartományt, ameddig a tárolóban meg kell őrizni a biztonsági mentéseket. `MinimumNumberOfBackups` egy opcionális paraméter, amely megadható, hogy a megadott számú biztonsági mentés mindig a-tól függetlenül megmaradjon `RetentionDuration` . Az alábbi példa azt szemlélteti, hogy a biztonsági másolatok _10_ napig megmaradjanak, és nem teszi lehetővé, hogy a biztonsági másolatok száma _20_ alá kerüljön.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Miután meghatározta a biztonsági mentési szabályzatot az adatbiztonsági mentési követelmények teljesítése érdekében, a biztonsági mentési szabályzatnak megfelelő módon kell társítania egy _alkalmazást_ vagy _szolgáltatást_, vagy egy _partíciót_.

> [!NOTE]
> A biztonsági mentés engedélyezése előtt győződjön meg arról, hogy a folyamatban lévő alkalmazások frissítése nem történt meg
>

### <a name="hierarchical-propagation-of-backup-policy"></a>A biztonsági mentési szabályzat hierarchikus propagálása
Service Fabric az alkalmazás, a szolgáltatás és a partíciók közötti kapcsolat hierarchikus az [alkalmazás modelljében](./service-fabric-application-model.md)leírtak szerint. A biztonsági mentési szabályzat egy _alkalmazással_, _szolgáltatással_ vagy a hierarchiában található _partícióval_ is társítható. A biztonsági mentési szabályzat hierarchikusan propagálja a következő szintre. Feltéve, hogy csak egy biztonsági mentési szabályzatot hozott létre és társít egy _alkalmazáshoz_, az összes _megbízható állapot-nyilvántartó szolgáltatáshoz_ és az alkalmazás _Reliable Actors_ tartozó összes  állapot-nyilvántartó partíciót a biztonsági mentési szabályzattal kell elkészíteni. Ha a biztonsági mentési szabályzat _megbízható állapot-nyilvántartó szolgáltatáshoz_ van társítva, a biztonsági mentési szabályzattal minden partíciója biztonsági mentésre kerül.

### <a name="overriding-backup-policy"></a>Biztonsági mentési szabályzat felülbírálása
Előfordulhat, hogy az alkalmazás összes szolgáltatásához azonos biztonsági mentési ütemtervtel rendelkező adatbiztonsági mentésre van szükség, kivéve azokat a szolgáltatásokat, amelyekhez szükség van az adatbiztonsági mentésre a magasabb gyakoriságú ütemterv használatával, vagy a biztonsági mentést egy másik Storage-fiókra vagy fájlmegosztás. Az ilyen forgatókönyvek kezeléséhez a Backup Restore Service lehetővé teszi a propagált házirend felülbírálását a szolgáltatás és a partíció hatókörén. Ha a biztonsági mentési szabályzat _szolgáltatáshoz_ vagy _partícióhoz_ van társítva, akkor felülbírálja a propagált biztonsági mentési szabályzatot, ha van ilyen.

### <a name="example"></a>Példa

Ez a példa a telepítőt használja két alkalmazással, _MyApp_A_ és _MyApp_Bval_. Az alkalmazás _MyApp_A_ két megbízható állapot-nyilvántartó szolgáltatást tartalmaz, a _SvcA1_  &  _SvcA3_ és egy megbízható Actor Service-t, a _ActorA2_-t. A _SvcA1_ három partíciót tartalmaz, míg a _ActorA2_ és a _SvcA3_ két partíciót tartalmaz.  Az alkalmazás _MyApp_B_ három megbízható állapot-nyilvántartó szolgáltatást, _SvcB1_, _SvcB2_ és _SvcB3_ tartalmaz. A _SvcB1_ és a _SvcB2_ két partíciót tartalmaz, míg a _SvcB3_ három partíciót tartalmaz.

Tegyük fel, hogy ezek az alkalmazások biztonsági mentési követelményei a következők:

1. MyApp_A
    1. Napi biztonsági mentés készítése az összes _megbízható állapot-nyilvántartó szolgáltatás_ és az alkalmazáshoz tartozó _Reliable Actors_ összes partíciója számára. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

    2. Az egyik szolgáltatás ( _SvcA3_) minden órában biztonsági mentést igényel.

    3. A partíciós _SvcA1_P2_ adatmérete a vártnál nagyobb, és a biztonsági mentési adatmennyiséget a különböző tárolási hely _BackupStore2_ kell tárolni.

2. MyApp_B
    1. Készítsen biztonsági mentést minden vasárnap 8:00 ÓRAKOR a _SvcB1_ szolgáltatás összes partíciója számára. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

    2. Készítsen biztonsági másolatot az adatokról minden nap 8:00 ÓRAKOR a következőhöz: Partition _SvcB2_P1_. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

Az adatbiztonsági mentésre vonatkozó követelmények megoldásához a biztonsági mentési szabályzatok BP_1 BP_5 létrejön, és a biztonsági mentés az alábbiak szerint van engedélyezve.
1. MyApp_A
    1. Hozzon létre biztonsági mentési szabályzatot, _BP_1_ a gyakoriság-alapú biztonsági mentési ütemtervtel, ahol a gyakoriság értéke 24 óra. és a biztonsági mentési tár a tárolási hely _BackupStore1_ használatára van konfigurálva. Engedélyezze ezt a házirendet az alkalmazás- _MyApp_A_ az [alkalmazás-biztonsági mentési API engedélyezése](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) paranccsal. Ez a művelet lehetővé teszi az adatbiztonsági mentést a biztonsági mentési szabályzattal _BP_1_ a _megbízható állapot-nyilvántartó szolgáltatások_ összes partíciója és az alkalmazás- _MyApp_Ahoz_ tartozó _Reliable Actors_ számára.

    2. Hozzon létre biztonsági mentési szabályzatot, _BP_2_, a gyakoriság-alapú biztonsági mentési ütemtervtel, ahol a gyakoriság értéke 1 óra. és a biztonsági mentési tár a tárolási hely _BackupStore1_ használatára van konfigurálva. Engedélyezze ezt a házirendet a szolgáltatás _SvcA3_ a [Service backup API engedélyezése](/rest/api/servicefabric/sfclient-api-enableservicebackup) használatával. Ez a művelet felülbírálja a propagált házirendet _BP_1_ explicit módon engedélyezett biztonsági mentési házirend _BP_2_ a _SvcA3_ összes partíciója számára, amely az adatbiztonsági mentést a biztonsági mentési szabályzattal _BP_2_ a partíciók esetében.

    3. Hozzon létre biztonsági mentési szabályzatot, _BP_3_ a gyakoriság-alapú biztonsági mentési ütemtervtel, ahol a gyakoriság értéke 24 óra. és a biztonsági mentési tár a tárolási hely _BackupStore2_ használatára van konfigurálva. Engedélyezze ezt a házirendet a partíciós _SvcA1_P2_ a [partíciós biztonsági mentési API engedélyezése](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) paranccsal. Ez a művelet felülbírálja a propagált házirendet _BP_1_ explicit módon engedélyezett biztonsági mentési házirend _BP_3_ a partíció _SvcA1_P2_.

2. MyApp_B
    1. Biztonsági mentési szabályzat létrehozása, _BP_4_, időalapú biztonsági mentési ütemtervtel, ahol az ütemezett gyakoriság típusa heti értékre van állítva, a futtatási napok értéke vasárnap, a futtatási idők értéke pedig 8:00. A biztonsági mentési tár a tárolási hely _BackupStore1_ használatára van konfigurálva. Engedélyezze ezt a házirendet a szolgáltatás _SvcB1_ a [Service backup API engedélyezése](/rest/api/servicefabric/sfclient-api-enableservicebackup) használatával. Ez a művelet lehetővé teszi az adatbiztonsági mentést a biztonsági mentési szabályzattal _BP_4_ a Service _SvcB1_ összes partíciójának használatával.

    2. Biztonsági mentési szabályzat létrehozása, _BP_5_, időalapú biztonsági mentési ütemtervtel, ahol az ütemezett gyakoriság típusa napi értékre van állítva, és a futtatási időpontok értéke 8:00. A biztonsági mentési tár a tárolási hely _BackupStore1_ használatára van konfigurálva. Engedélyezze ezt a házirendet a partíciós _SvcB2_P1_ a [partíciós biztonsági mentési API engedélyezése](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) paranccsal. Ez a művelet lehetővé teszi az adatbiztonsági mentést a _SvcB2_P1_ partícióra vonatkozó biztonsági mentési szabályzat _BP_5_ használatával.

Az alábbi ábra a explicit módon engedélyezett biztonsági mentési házirendeket és a propagált biztonsági mentési szabályzatokat ábrázolja.

![Service Fabric alkalmazás-hierarchia][0]

## <a name="disable-backup"></a>Biztonsági mentés letiltása
A biztonsági mentési szabályzatok letilthatók, ha nincs szükség az adatbiztonsági mentésre. Az _alkalmazásban_ engedélyezett biztonsági mentési szabályzatot csak az alkalmazás-biztonsági mentési API [letiltásával](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) lehet letiltani, és a _szolgáltatásban_ engedélyezett biztonsági mentési szabályzat a  [szolgáltatás biztonsági mentési API-k letiltásával](/rest/api/servicefabric/sfclient-api-disableservicebackup) is letiltható. _a partíciós_ biztonsági [mentési API](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) letiltásával _ugyanazon a_ _partíción_ is letiltható.

* Egy _alkalmazás_ biztonsági mentési szabályzatának letiltása leállítja az összes rendszeres biztonsági mentést a biztonsági mentési szabályzat propagálásának eredményeképpen, megbízható állapot-nyilvántartó partíciók vagy megbízható szereplők partíciói számára.

* Egy _szolgáltatás_ biztonsági mentési szabályzatának letiltása leállítja az összes rendszeres biztonsági mentést, amely a biztonsági mentési szabályzatnak a _szolgáltatás_ partíciókhoz való propagálásának eredményeképpen történik.

* Egy _partíció_ biztonsági mentési szabályzatának letiltása leállítja az összes rendszeres biztonsági mentést a partíció biztonsági mentési szabályzata miatt.

* Az entitások (alkalmazás/szolgáltatás/partíció) biztonsági mentésének letiltása közben `CleanBackup` _igaz_ értékre állítható, hogy a konfigurált tárolóban lévő összes biztonsági mentést törölni lehessen.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Győződjön meg arról, hogy a biztonsági mentés letiltása előtt nincsenek folyamatban az alkalmazások frissítése
>

## <a name="suspend--resume-backup"></a>Felfüggesztés & biztonsági mentés folytatása
Bizonyos helyzetek ideiglenes felfüggesztést igényelhetnek az adatmennyiség rendszeres biztonsági mentéséről. Ilyen helyzetekben a követelménytől függően a backup API felfüggesztése egy _alkalmazáson_, _szolgáltatáson_ vagy _partíción_ is felhasználható. A biztonsági mentés rendszeres felfüggesztése az alkalmazás hierarchiájának az alkalmazott pontról való átjárása. 

* Ha egy _alkalmazás_ felfüggesztését az [alkalmazás felfüggesztése](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API-val alkalmazza, akkor az alkalmazásban lévő összes szolgáltatás és partíció fel van függesztve az adat rendszeres biztonsági mentéséhez.

* Ha a felfüggesztést a szolgáltatás [felfüggesztése szolgáltatás biztonsági mentési](/rest/api/servicefabric/sfclient-api-suspendservicebackup) _API használatával alkalmazza_ , akkor a szolgáltatásban lévő összes partíció fel van függesztve az adat rendszeres biztonsági mentéséhez.

* Ha a felfüggesztést egy olyan _partíción_ alkalmazza, amely a [felfüggesztési partíció biztonsági mentési](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API-ját használja, a rendszer felfüggeszti a szolgáltatásban lévő partíciókat a rendszeres biztonsági mentéshez.

Miután a felfüggesztés igénybe van véve, az időszakos biztonsági mentés visszaállítható a megfelelő folytatási biztonsági mentési API használatával. Az időszakos biztonsági mentést ugyanabban az _alkalmazásban_, _szolgáltatásban_ vagy _partíción_ kell folytatni, ahol fel lett függesztve.

* Ha a felfüggesztést egy _alkalmazáson_ alkalmazták, akkor azt az alkalmazás- [biztonsági mentési](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API-val folytathatja. 

* Ha a felfüggesztést egy _szolgáltatáson_ alkalmazták, akkor azt folytatni kell a [Service backup API folytatásával](/rest/api/servicefabric/sfclient-api-resumeservicebackup) .

* Ha a felfüggesztést egy _partíción_ alkalmazták, akkor azt újra kell folytatni a [partíciós biztonsági mentési](/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API-val.

### <a name="difference-between-suspend-and-disable-backups"></a>A biztonsági másolatok felfüggesztése és letiltása közötti különbség
Ha egy adott alkalmazáshoz, szolgáltatáshoz vagy partícióhoz már nincs szükség biztonsági mentésre, akkor tiltsa le a biztonsági mentést. Az egyik meghívhatja a biztonsági mentési kérelem letiltását a tiszta biztonsági mentések paraméterrel, ami azt jelenti, hogy az összes meglévő biztonsági mentés is törlődik. A felfüggesztést azonban olyan helyzetekben kell használni, amikor az egyik az ismert hálózati probléma miatt nem sikerül ideiglenesen kikapcsolni a biztonsági mentéseket. 

Bár a Letiltás csak olyan szinten hívható meg, amely korábban engedélyezve lett a biztonsági mentéshez, azonban a felfüggesztés bármely olyan szinten alkalmazható, amely jelenleg engedélyezve van a biztonsági mentéshez közvetlenül, vagy öröklés/hierarchia használatával. Ha például a biztonsági mentés alkalmazási szinten engedélyezve van, az egyik csak az alkalmazás szintjén lehet letiltani a letiltást, azonban az alkalmazásban az alkalmazáshoz tartozó bármely szolgáltatás vagy partíció is meghívja a felfüggesztést. 

## <a name="auto-restore-on-data-loss"></a>Automatikus visszaállítás az adatvesztéskor
A szolgáltatás partíciója nem várt hibák miatt elveszítheti az adatvesztést. Például a partíciók három másodpéldánya (beleértve az elsődleges replikát is) esetében a lemez megsérül vagy törölve lesz.

Ha Service Fabric észleli, hogy a partíció adatvesztésben van, meghívja a `OnDataLossAsync` csatoló metódust a partíción, és elvárja, hogy a partíció elvégezze az adatvesztést. Ebben az esetben, ha a partíción a tényleges biztonsági mentési házirendben a `AutoRestoreOnDataLoss` jelző van beállítva, `true` akkor a visszaállítás automatikusan aktiválódik a partíció legújabb elérhető biztonsági másolatának használatával.

> [!NOTE]
> Azt javasoljuk, hogy ne állítson be automatikus visszaállítást üzemi fürtökben
>

## <a name="get-backup-configuration"></a>Biztonsági mentési konfiguráció beolvasása
Az _alkalmazások_, _szolgáltatások_ és _partíciók_ hatókörében külön API-k érhetők el a biztonsági mentési konfigurációs információk lekéréséhez. Az [alkalmazás biztonsági mentési konfigurációs adatainak](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)beszerzése, a [szolgáltatás biztonsági mentési konfigurációs adatainak](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)beolvasása, valamint a [partíció biztonsági mentési konfigurációs adatainak](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) beolvasása ezen API-k Ezek az API-k elsősorban a megfelelő biztonsági mentési szabályzatot, a biztonsági mentési szabályzat hatálya alá eső hatókört és a biztonsági mentési felfüggesztés részleteit adják vissza. Az alábbi rövid leírás az API-k visszaadott eredményeiről szól.

- Az alkalmazás biztonsági másolatának konfigurációs adatai: az alkalmazáson alkalmazott biztonsági mentési házirend részleteit, valamint az alkalmazáshoz tartozó szolgáltatásokon és partíciókban található összes, a felett felülbírált szabályzatot tartalmazza. Emellett az alkalmazás és az IT-szolgáltatások, valamint a partíciók felfüggesztési információit is tartalmazza.

- A szolgáltatás biztonsági mentési konfigurációjának adatai: megadja a szolgáltatásra vonatkozó érvényes biztonsági mentési házirend részleteit, valamint azt a hatókört, amelyre a szabályzatot alkalmazták A szolgáltatás és a partíciók felfüggesztési információit is tartalmazza.

- Partíció biztonsági mentési konfigurációs adatai: a biztonsági mentési szabályzat részletes adatait tartalmazza a partíción, valamint azt a hatókört, amelyre a szabályzat érvényes. A partíciók felfüggesztési információit is tartalmazza.

## <a name="list-available-backups"></a>Elérhető biztonsági másolatok listázása

Az elérhető biztonsági másolatok a biztonsági mentési listával API-val is megtekinthetők. Az API-hívás eredménye tartalmazza a biztonsági mentési tárolóban elérhető biztonsági másolatokhoz kapcsolódó biztonsági mentési adatokat, amelyek a megfelelő biztonsági mentési házirendben vannak konfigurálva. Az API különböző változatai az alkalmazáshoz, szolgáltatáshoz vagy partícióhoz tartozó elérhető biztonsági másolatok listázására szolgálnak. Ezek az API-k támogatják az összes megfelelő partíció _legújabb_ elérhető biztonsági mentését, illetve a biztonsági másolatok szűrését a _kezdő dátum_ és a _befejezési dátum_ alapján.

Ezek az API-k az eredmények tördelését is támogatják, ha a _MaxResults_ paraméter értéke nem nulla pozitív egész szám, akkor az API a maximális _MaxResults_ biztonsági mentési információ elemeit adja vissza. Ha a _MaxResults_ értéknél több biztonsági mentési információ is elérhető, akkor a rendszer a folytatási tokent adja vissza. A folytatási jogkivonat érvényes paramétere az eredmények következő készletének beolvasására használható. Ha az API következő hívására érvényes folytatólagos jogkivonat értéket ad át, az API a következő eredményeket adja vissza. Az összes elérhető eredmény visszaadásakor a válasz nem tartalmazza a folytatási tokent.

Az alábbiakban a támogatott változatokkal kapcsolatos rövid információk szerepelnek.

- [Alkalmazás biztonsági mentési listájának beolvasása](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): az adott Service Fabric alkalmazáshoz tartozó összes partícióhoz elérhető biztonsági másolatok listáját adja vissza.

- [Szolgáltatás biztonsági mentési listájának beolvasása](/rest/api/servicefabric/sfclient-api-getservicebackuplist): a megadott Service Fabric szolgáltatáshoz tartozó minden partíció számára elérhető biztonsági másolatok listáját adja vissza.
 
- [Partíció biztonsági mentési listájának beolvasása](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): a megadott partícióhoz elérhető biztonsági másolatok listáját adja vissza.

## <a name="next-steps"></a>Következő lépések
- [Biztonsági mentés visszaállítása REST API referenciája](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
