---
title: Felosztási-egyesítési szolgáltatás üzembe helyezése | Microsoft Docs
description: A felosztott egyesítés használatával is áthelyezheti az adatátvitelt a többrétegű adatbázisok között.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: a8c50f492c28bf1e009d15d6332e939959190a49
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568507"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Felosztási-egyesítési szolgáltatás üzembe helyezése a szilánkokra osztott adatbázisok közötti adatáthelyezéshez

A felosztott egyesítés eszköz lehetővé teszi az adatáthelyezést a töredezett adatbázisok között. Lásd: az adatáthelyezés a kibővített [felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>A Split-Merge csomagok letöltése
1. Töltse le a legújabb NuGet-verziót a [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)webhelyről.
2. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahová letöltötte a nuget. exe fájlt. A letöltés PowerShell-parancsokat tartalmaz.
3. Töltse le a legújabb felosztott egyesítési csomagot az aktuális könyvtárba az alábbi paranccsal:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

A fájlok a **Microsoft. Azure. SqlDatabase. ElasticScale. Service. SplitMerge. x. x. xxx. x** nevű könyvtárba kerülnek, ahol az *x. x. xxx. x* a verziószámot tükrözi. Keresse meg a **content\splitmerge\service** alkönyvtárában található felosztott egyesítési szolgáltatási fájlokat, valamint a **content\splitmerge\powershell** alkönyvtárában található felosztott és egyesíthető PowerShell-parancsfájlokat (és a szükséges ügyféloldali DLL-eket).

## <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy Azure SQL DB-adatbázist, amelyet a rendszer felosztó állapot-adatbázisként fog használni. Nyissa meg az [Azure Portal](https://portal.azure.com). Hozzon létre egy új **SQL Database**. Adjon nevet az adatbázisnak, és hozzon létre egy új rendszergazdát és jelszót. Ügyeljen rá, hogy a nevet és a jelszót a későbbi használatra jegyezze fel.
2. Győződjön meg arról, hogy az Azure SQL DB-kiszolgáló lehetővé teszi az Azure-szolgáltatások számára a kapcsolódást. A portálon, a **tűzfal beállításainál**ellenőrizze, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás be értékre van-e **állítva.** Kattintson a Save (Mentés) ikonra.
3. Hozzon létre egy Azure Storage-fiókot a diagnosztika kimenetéhez.
4. Hozzon létre egy Azure Cloud Service-t a Split-Merge szolgáltatáshoz.

## <a name="configure-your-split-merge-service"></a>A felosztott egyesítési szolgáltatás konfigurálása
### <a name="split-merge-service-configuration"></a>Felosztás – egyesítési szolgáltatás konfigurációja
1. Abban a mappában, amelybe a felosztott egyesítés szerelvényeket letöltötte, hozzon létre egy másolatot a **ServiceConfiguration. template. cscfg** fájlról, amely a **SplitMergeService. Cspkg** és a **ServiceConfiguration. cscfg**átnevezésével együtt lett elküldve.
2. Nyissa meg a **ServiceConfiguration. cscfg** egy szövegszerkesztőben, például a Visual Studióban, amely érvényesíti a bemeneteket, például a tanúsítvány ujjlenyomatai megfelelnek formátumát.
3. Hozzon létre egy új adatbázist, vagy válasszon ki egy meglévő adatbázist, amely az állapot-adatbázisként szolgál a felosztási és egyesítési műveletekhez, és kérje le az adatbázishoz tartozó kapcsolódási karakterláncot. 
   
   > [!IMPORTANT]
   > Ebben az esetben az állapot-adatbázisnak a latin rendezést kell használnia\_(\_az\_SQL latin\_általános CP1\_CI as). További információ: [Windows rendezési név (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Az Azure SQL DB-vel a kapcsolatok karakterlánca általában a következőkből áll:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Adja meg ezt a cscfg-fájlt a ElasticScaleMetadata beállítás **SplitMergeWeb** és **SplitMergeWorker** szerepkör szakaszában.
5. A **SplitMergeWorker** szerepkörhöz adjon meg egy érvényes kapcsolódási karakterláncot az Azure Storage-hoz az **WorkerRoleSynchronizationStorageAccountConnectionString** beállításhoz.

### <a name="configure-security"></a>Biztonság konfigurálása
A szolgáltatás biztonságának konfigurálásával kapcsolatos részletes utasításokért tekintse meg a [felosztás – egyesítés biztonsági konfigurációját](sql-database-elastic-scale-split-merge-security-configuration.md).

Az oktatóanyag egyszerű tesztelési célú üzembe helyezése érdekében a szolgáltatás működésének megkezdéséhez minimális konfigurációs lépéseket kell végrehajtani. Ezek a lépések csak azt a gépet vagy fiókot teszik lehetővé, amelyet a szolgáltatással való kommunikációra hajtanak végre.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Hozzon létre egy új könyvtárat, és ebből a könyvtárból hajtsa végre a következő parancsot a [Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) ablakának fejlesztői parancssorában:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

A titkos kulcs biztonsága érdekében a rendszer jelszót kér. Adjon meg egy erős jelszót, és erősítse meg. Ezután a rendszer kéri, hogy a jelszót később is használni lehessen. Az **Igen** gombra kattintva importálhatja azt a megbízható hitelesítésszolgáltatók legfelső szintű tárolójába.

### <a name="create-a-pfx-file"></a>PFX-fájl létrehozása
Futtassa a következő parancsot ugyanabból az ablakból, amelyben a MakeCert végre lett hajtva; használja ugyanazt a jelszót, amelyet a tanúsítvány létrehozásához használt:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Az ügyféltanúsítvány importálása a személyes tárolóba
1. A Windows Intézőben kattintson duplán a **MyCert. pfx**fájlra.
2. A **tanúsítvány importálása varázslóban** válassza az **aktuális felhasználó** lehetőséget, majd kattintson a **tovább**gombra.
3. Erősítse meg a fájl elérési útját, és kattintson a **tovább**gombra.
4. Írja be a jelszót, hagyja bejelölve az **összes kiterjesztett tulajdonságot** , és kattintson a **tovább**gombra.
5. Hagyja **automatikusan a tanúsítványtároló [...] elemet** , és kattintson a **tovább**gombra.
6. Kattintson a **Befejezés** , majd **az OK gombra**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>A PFX-fájl feltöltése a Cloud Service-be
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza a **Cloud Services**lehetőséget.
3. Válassza ki a fent létrehozott felhőalapú szolgáltatást a felosztás/egyesítés szolgáltatáshoz.
4. A felső menüben kattintson a **tanúsítványok** elemre.
5. Kattintson az alsó sávban a **feltöltés** elemre.
6. Válassza ki a PFX-fájlt, és adja meg a fenti jelszót.
7. Ha elkészült, másolja a tanúsítvány ujjlenyomatát a lista új bejegyzéséről.

### <a name="update-the-service-configuration-file"></a>A szolgáltatás konfigurációs fájljának frissítése
Illessze be a fent másolt tanúsítvány ujjlenyomatát a beállítások ujjlenyomat/érték attribútumára.
A feldolgozói szerepkörhöz:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

A webes szerepkörhöz:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Vegye figyelembe, hogy az éles üzembe helyezések esetében külön tanúsítványokat kell használni a HITELESÍTÉSSZOLGÁLTATÓhoz, a titkosításhoz, a kiszolgálói tanúsítványhoz és az ügyféltanúsítványokhöz. Részletes útmutatásért lásd: [biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>A szolgáltatás üzembe helyezése
1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Válassza ki a korábban létrehozott Cloud Service-t.
3. Kattintson az **Áttekintés** elemre.
4. Válassza ki az átmeneti környezetet, majd kattintson a **feltöltés**elemre.
5. A párbeszédpanelen adja meg a központi telepítési címkét. A "csomag" és a "konfiguráció" esetében kattintson a "helyi" lehetőségre, és válassza ki a korábban konfigurált **SplitMergeService. cspkg** fájlt és a cscfg-fájlt.
6. Győződjön meg arról, hogy az **üzembe helyezés jelölőnégyzet akkor is be van jelölve, ha egy vagy több szerepkör egyetlen példányt tartalmaz** .
7. Az üzembe helyezés megkezdéséhez kattintson a jobb alsó sarokban található ketyeg gombra. Várhatóan néhány percet is igénybe vehet.


## <a name="troubleshoot-the-deployment"></a>Az üzemelő példány hibáinak megoldása
Ha a webes szerepkör nem tud online állapotba jutni, valószínűleg probléma van a biztonsági konfigurációval. Győződjön meg arról, hogy az SSL konfigurálva van a fent leírtak szerint.

Ha a feldolgozói szerepkör nem tud online állapotba jutni, de a webes szerepkör sikeres, akkor valószínűleg probléma van a korábban létrehozott status adatbázishoz való csatlakozással.

* Győződjön meg arról, hogy a cscfg található kapcsolatok karakterlánca pontos.
* Győződjön meg arról, hogy a kiszolgáló és az adatbázis létezik, valamint hogy a felhasználói azonosító és a jelszó helyes.
* Az Azure SQL DB esetében a következő formátumúnak kell lennie a kapcsolatok karakterláncának:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Győződjön meg arról, hogy a kiszolgálónév nem a **https://** kezdődik.
* Győződjön meg arról, hogy az Azure SQL DB-kiszolgáló lehetővé teszi az Azure-szolgáltatások számára a kapcsolódást. Ehhez nyissa meg az adatbázist a portálon, és győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás a **On** ** értékre van állítva.

## <a name="test-the-service-deployment"></a>A szolgáltatás központi telepítésének tesztelése
### <a name="connect-with-a-web-browser"></a>Webböngészővel való kapcsolat
Határozza meg a felosztási-egyesítési szolgáltatás webes végpontját. Ezt a portálon a felhőalapú szolgáltatás áttekintésével, a jobb oldalon pedig a **webhely URL-címével** tekintheti meg. Cserélje le az **http://** -t a **https://** -re, mert az alapértelmezett biztonsági beállítások LEtiltják a http-végpontot Töltse be az URL-cím lapját a böngészőben.

### <a name="test-with-powershell-scripts"></a>Tesztelés PowerShell-parancsfájlokkal
A központi telepítés és a környezet a mellékelt minta PowerShell-szkriptek futtatásával is tesztelhető.

A parancsfájl fájljai a következők:

1. **SetupSampleSplitMergeEnvironment. ps1** – egy teszt adatréteget állít be a felosztáshoz/egyesítéshez (lásd az alábbi táblázatot a részletes leíráshoz)
2. **ExecuteSampleSplitMerge. ps1** – tesztelési műveleteket hajt végre a teszt adatrétegen (lásd az alábbi táblázatot a részletes leíráshoz)
3. **GetMappings. ps1** – legfelső szintű minta parancsfájl, amely kinyomtatja a szegmens leképezések aktuális állapotát.
4. **ShardManagement. psm1** – segítő parancsfájl, amely a ShardManagement API-t csomagolja
5. **SqlDatabaseHelpers. psm1** – segítő parancsfájl SQL-adatbázisok létrehozásához és kezeléséhez
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Egy szegmens Map Manager-adatbázis létrehozása</td>
     </tr>
     <tr>
       <td>2.    2 szegmensű adatbázist hoz létre.
     </tr>
     <tr>
       <td>3.    Létrehoz egy szegmenses térképet ezekhez az adatbázisokhoz (törli az adott adatbázishoz tartozó meglévő szegmenses térképeket). </td>
     </tr>
     <tr>
       <td>4.    Létrehoz egy kisméretű mintát a szegmensekben, és feltölti a táblázatot az egyik szegmensben.</td>
     </tr>
     <tr>
       <td>5.    Deklarálja a SchemaInfo a szilánkokra bontott táblához.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fájl</th>
       <th>Lépések</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Elküld egy felosztott kérelmet a Split-Merge szolgáltatás webes felületének, amely az első szegmens adatainak felére osztja ketté a második szilánkot.</td>
     </tr>
     <tr>
       <td>2.    Lekérdezi a megosztott kérelmek állapotának webes felületét, és megvárja, amíg a kérés befejeződik.</td>
     </tr>
     <tr>
       <td>3.    Egyesítő kérelmet küld a felosztási szolgáltatás webes felületének, amely az adatokat a második szegmensből az első szegmensbe helyezi vissza.</td>
     </tr>
     <tr>
       <td>4.    Lekérdezi az egyesítési kérelem állapotának webes felületét, és megvárja, amíg a kérés befejeződik.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>A telepítés ellenőrzése a PowerShell használatával
1. Nyisson meg egy új PowerShell-ablakot, és keresse meg azt a könyvtárat, ahová a felosztott egyesítési csomagot letöltötte, majd navigáljon a "PowerShell" könyvtárba.
2. Hozzon létre egy Azure SQL Database kiszolgálót (vagy válasszon ki egy meglévő kiszolgálót), ahol a rendszer létrehozza a szegmenses Térkép kezelőjét és a szegmenseket.
   
   > [!NOTE]
   > A SetupSampleSplitMergeEnvironment. ps1 parancsfájl alapértelmezés szerint ugyanazon a kiszolgálón hozza létre az összes adatbázist, hogy a szkriptet egyszerűként tárolja. Ez nem korlátozza a felosztási-egyesítési szolgáltatást.
   >
   
   Ahhoz, hogy a felosztott egyesítési szolgáltatás adatokat helyezzen át, és frissítse a szegmenses térképet, egy SQL-hitelesítési bejelentkezésre van szükség az adatbázisok olvasási/olvasási hozzáféréssel. Mivel a felosztási-egyesítési szolgáltatás a felhőben fut, jelenleg nem támogatja az integrált hitelesítést.
   
   Győződjön meg arról, hogy az Azure SQL Server úgy van konfigurálva, hogy engedélyezze a hozzáférést az ezeket a parancsfájlokat futtató gép IP-címéről. Ez a beállítás az Azure SQL Server/Configuration/Allowed IP-címek területen található.
3. Futtassa a SetupSampleSplitMergeEnvironment. ps1 parancsfájlt a minta környezet létrehozásához.
   
   A parancsfájl futtatásakor a rendszer törli az összes meglévő, a szegmensek közötti Térkép-felügyeleti adatstruktúrákat a szegmenses Térkép-kezelő adatbázisán és a szegmenseken. Előfordulhat, hogy újra kell futtatnia a szkriptet, ha újra szeretné inicializálni a szegmenses térképet vagy a szegmenseket.
   
   Minta parancssor:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Hajtsa végre a Getmappings. ps1 parancsfájlt a mintavételi környezetben jelenleg létező leképezések megtekintéséhez.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Hajtsa végre a ExecuteSampleSplitMerge. ps1 parancsfájlt egy felosztott művelet végrehajtásához (az első szegmens adatai felé haladva a második szilánkra), majd egy egyesítési műveletet (az adatáthelyezést az első szegmensre). Ha az SSL-t konfigurálta, és letiltotta a http-végpontot, használja helyette az https://-végpontot.
   
   Minta parancssor:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Ha az alábbi hibaüzenet jelenik meg, valószínűleg probléma van a webes végpont tanúsítványával. Próbáljon meg csatlakozni a webes végponthoz a kedvenc webböngészőjével, és ellenőrizze, hogy van-e hiba a tanúsítványban.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Ha a művelet sikeres volt, a kimenetnek az alábbihoz hasonlóan kell kinéznie:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Kísérletezzen más adattípusokkal. Az összes parancsfájl egy opcionálisan ShardKeyType paramétert is biztosít, amely lehetővé teszi a kulcs típusának megadását. Az alapértelmezett érték az Int32, de Int64, GUID azonosítót vagy bináris értéket is megadhat.

## <a name="create-requests"></a>Létrehozási kérelmek
A szolgáltatás a webes felhasználói felületen vagy a SplitMerge. psm1 PowerShell-modul importálásával és használatával is használható, amely a webes szerepkör használatával küldi el a kéréseit.

A szolgáltatás a többrészes táblákban és a hivatkozási táblákban is áthelyezheti az adatátvitelt. A szilánkokra osztott tábla egy horizontális Felskálázási kulcs oszlopával rendelkezik, és az egyes szegmenseken különböző sorok szerepelnek. Egy hivatkozási tábla nem található, ezért az összes szegmensen ugyanazokat az adatsorokat tartalmazza. A hivatkozási táblázatok olyan adathalmazok esetén hasznosak, amelyek gyakran nem változnak, és a rendszer a lekérdezésekben a szilánkokra osztott táblákkal való CSATLAKOZÁSra használatos.

A felosztott egyesítési művelet végrehajtásához deklarálnia kell az áthelyezni kívánt tagolt táblákat és hivatkozási táblákat. Ez a **SchemaInfo** API-val valósítható meg. Ez az API a **Microsoft. Azure. SqlDatabase. ElasticScale. ShardManagement. Schema** névtérben található.

1. Minden egyes többszintű tábla esetében hozzon létre egy **ShardedTableInfo** objektumot, amely leírja a tábla fölérendelt sémájának nevét (nem kötelező, alapértelmezés szerint "dbo"), a tábla nevét és az abban a táblában található oszlopnevet, amely a horizontális kulcsot tartalmazza.
2. Mindegyik hivatkozási táblázathoz hozzon létre egy **ReferenceTableInfo** objektumot, amely leírja a tábla szülő sémájának nevét (nem kötelező, alapértelmezés szerint "dbo") és a tábla nevét.
3. Adja hozzá a fenti TableInfo objektumokat egy új **SchemaInfo** objektumhoz.
4. Szerezzen be egy **ShardMapManager** objektumra mutató hivatkozást, és hívja meg a **GetSchemaInfoCollection**.
5. Adja hozzá a **SchemaInfo** a **SchemaInfoCollection**, amely megadja a szegmens leképezésének nevét.

Erre példa látható a SetupSampleSplitMergeEnvironment. ps1 parancsfájlban.

A felosztási-egyesítési szolgáltatás nem hozza létre a célként megadott adatbázist (vagy sémát az adatbázis bármely táblájához). A szolgáltatásnak küldött kérések elküldése előtt előre létre kell hozni őket.

## <a name="troubleshooting"></a>Hibaelhárítás
A PowerShell-parancsfájlok futtatásakor a következő üzenet jelenhet meg:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Ez a hiba azt jelenti, hogy az SSL-tanúsítvány nincs megfelelően konfigurálva. Kövesse a "csatlakozás webböngészővel" című szakasz utasításait.

Ha nem tud elküldeni olyan kérelmeket, amelyek a következőket láthatják:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Ebben az esetben vizsgálja meg a konfigurációs fájlt, különösen a **WorkerRoleSynchronizationStorageAccountConnectionString**beállítását. Ez a hiba általában azt jelzi, hogy a feldolgozói szerepkör nem tudta sikeresen inicializálni a metaadat-adatbázist az első használat során. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

