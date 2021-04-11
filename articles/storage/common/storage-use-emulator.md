---
title: Az Azure Storage Emulator használata fejlesztéshez és teszteléshez
description: Az Azure Storage Emulator ingyenes helyi fejlesztési környezetet biztosít az Azure Storage-alkalmazások fejlesztéséhez és teszteléséhez.
author: twooley
ms.author: twooley
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 909de4b6782a0cd03b7c7d7c72bfa50e5541b6d7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277113"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Az Azure Storage Emulator használata fejlesztéshez és teszteléshez

A Microsoft Azure Storage Emulator egy olyan eszköz, amely az Azure Blob-, üzenetsor-és Table Services-t emulálja helyi fejlesztési célokra. Az alkalmazást helyileg tesztelheti az Azure-előfizetés létrehozása vagy a költségek felmerülése nélkül. Ha meggyőződött arról, hogy az alkalmazás hogyan működik az emulátorban, váltson egy Azure Storage-fiók használatára a felhőben.

> [!IMPORTANT]
> Az Azure Storage-emulátor már nincs aktívan kifejlesztve. A [**Azurite**](storage-use-azurite.md) a Storage Emulator platform tovább tart. A Azurite felülírja az Azure Storage-emulátort. A Azurite továbbra is frissülni fog az Azure Storage API-k legújabb verzióinak támogatásához. További információ: [**a Azurite Emulator használata a helyi Azure Storage-fejlesztéshez**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>A Storage Emulator beszerzése

A Storage Emulator a [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)részeként érhető el. A Storage Emulatort a [különálló telepítő](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (közvetlen letöltés) használatával is telepítheti. A Storage Emulator telepítéséhez rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

A Storage Emulator jelenleg csak Windows rendszeren fut. Ha a Linux rendszerhez készült Storage Emulatorra van szüksége, az egyik lehetőség a Közösség által karbantartott, nyílt forráskódú Storage Emulator- [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> A Storage Emulator egyik verziójában létrehozott adatmennyiség nem garantált, hogy más verzió használata esetén nem érhető el. Ha hosszú távon is meg kell őriznie az adatait, javasoljuk, hogy az adatait egy Azure Storage-fiókban tárolja, nem pedig a Storage Emulatorban.
> 
> A Storage Emulator a OData-kódtárak adott verzióitól függ. A Storage Emulator által más verziókkal használt OData DLL-ek nem támogatottak, és nem várt viselkedést okozhatnak. A tárolási szolgáltatás által támogatott bármely OData azonban a kérelmeknek az emulátorba való küldésére is használható.

## <a name="how-the-storage-emulator-works"></a>A Storage Emulator működése

A Storage Emulator egy helyi Microsoft SQL Server 2012 Express LocalDB-példányt használ az Azure Storage-szolgáltatások emulálása érdekében. Beállíthatja, hogy a Storage Emulator a LocalDB-példány helyett SQL Server helyi példányához férhessen hozzá. További információkért tekintse meg a [Storage Emulator elindítása és inicializálása](#start-and-initialize-the-storage-emulator) című szakaszt a cikk későbbi részében.

A Storage Emulator a Windows-hitelesítés használatával csatlakozik SQL Serverhoz vagy LocalDB.

A Storage Emulator és az Azure Storage szolgáltatás között számos funkció létezik. További információ ezekről a különbségekről: a [Storage Emulator és az Azure Storage közötti különbségek](#differences-between-the-storage-emulator-and-azure-storage) a jelen cikk későbbi részében.

## <a name="start-and-initialize-the-storage-emulator"></a>A Storage Emulator elindítása és inicializálása

Az Azure Storage Emulator elindítása:

1. Kattintson a **Start** gombra, vagy nyomja le a **Windows** billentyűt.
2. Kezdjen el gépelni `Azure Storage Emulator` .
3. Válassza ki az emulátort a megjelenített alkalmazások listájából.

A Storage Emulator indításakor megjelenik egy parancssori ablak. A konzol ablakával elindíthatja és leállíthatja a Storage-emulátort. Törölheti is az adatok, az állapot lekérése és az emulátor inicializálását a parancssorból. További információt a cikk későbbi, a [Storage Emulator parancssori eszközének ismertetése](#storage-emulator-command-line-tool-reference) című szakaszában talál.

> [!NOTE]
> Előfordulhat, hogy az Azure Storage-emulátor nem indul el megfelelően, ha egy másik tároló-emulátor, például a Azurite fut a rendszeren.

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén.

A Storage Emulator parancssori ablakának bezárását követően a Storage Emulator továbbra is futni fog. A Storage Emulator Console ablak újbóli létrehozásához kövesse az előző lépéseket a Storage Emulator elindítása után.

Amikor első alkalommal futtatja a Storage Emulatort, a helyi tárolási környezet inicializálva lesz. Az inicializálási folyamat létrehoz egy adatbázist a LocalDB-ben, és minden helyi tárolási szolgáltatáshoz fenntart HTTP-portokat.

A Storage Emulator alapértelmezés szerint telepítve van `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> A [Microsoft Azure Storage Explorer](https://storageexplorer.com) használatával dolgozhat a helyi tároló-emulátor erőforrásaival. Miután telepítette és elindította a Storage Emulatort, keresse meg a "(Emulator-default ports) (kulcs)" lehetőséget a Storage Explorer erőforrások fájában a "helyi & csatolt" alatt.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>A Storage Emulator inicializálása másik SQL-adatbázis használatára

A Storage Emulator parancssori eszköz használatával inicializálhatja a Storage-emulátort úgy, hogy az az alapértelmezett LocalDB-példánytól eltérő SQL Database-példányra mutasson:

1. Nyissa meg a Storage Emulator Console ablakot a [Storage Emulator elindítása és inicializálása](#start-and-initialize-the-storage-emulator) című szakaszban leírtak szerint.
1. A konzol ablakban írja be a következő parancsot, ahol a a `<SQLServerInstance>` SQL Server példány neve. A LocalDB használatához `(localdb)\MSSQLLocalDb` a SQL Server példányként kell megadnia.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   A következő parancsot is használhatja, amely az emulátort az alapértelmezett SQL Server példány használatára irányítja:

   `AzureStorageEmulator.exe init /server .`

   Vagy használhatja a következő parancsot is, amely inicializálja az adatbázist az alapértelmezett LocalDB-példányra:

   `AzureStorageEmulator.exe init /forceCreate`

További információt ezekről a parancsokról a [Storage Emulator parancssori eszköz dokumentációjában](#storage-emulator-command-line-tool-reference)talál.

> [!TIP]
> A [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) használatával kezelheti SQL Server példányait, beleértve a LocalDB telepítését is. Az SMS- **kapcsolat a kiszolgálóhoz** párbeszédpanelen adja meg `(localdb)\MSSQLLocalDb` a **kiszolgáló neve:** mezőben, hogy csatlakozhasson a LocalDB-példányhoz.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Kérelmek hitelesítése a Storage Emulator használatával

Miután telepítette és elindította a Storage Emulatort, tesztelheti a kódját. A Storage-emulátoron végzett minden kérést engedélyezni kell, kivéve, ha névtelen kérést használ. Engedélyezheti a kérelmeket a Storage-emulátoron megosztott kulcsos hitelesítéssel vagy közös hozzáférésű aláírással (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Hitelesítés megosztott kulcsú hitelesítő adatokkal

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

További információ a kapcsolatok sztringekről: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](./storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Hitelesítés közös hozzáférésű aláírással

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Néhány Azure Storage-ügyfél kódtára, például a Xamarin-könyvtár, csak a közös hozzáférésű aláírás (SAS) jogkivonatával történő hitelesítést támogatja. Az SAS-tokent [Storage Explorer](https://storageexplorer.com/) vagy egy másik olyan alkalmazás használatával is létrehozhatja, amely támogatja a megosztott kulcsos hitelesítést.

A Azure PowerShell használatával is létrehozhat SAS-tokent. Az alábbi példa egy blob-tárolóra vonatkozó teljes körű engedélyekkel rendelkező SAS-tokent hoz létre:

1. Azure PowerShell telepítése, ha még nem tette meg (az Azure PowerShell-parancsmagok legújabb verziójának használata ajánlott). A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).
2. Nyissa meg Azure PowerShell és futtassa a következő parancsokat, és cserélje `CONTAINER_NAME` le a nevet a választott névre:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Az új tárolóhoz tartozó, közös hozzáférésű aláírás URI-azonosítójának a következőhöz hasonlónak kell lennie:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Az ezzel a példával létrehozott megosztott elérési aláírás egy napig érvényes. Az aláírás teljes hozzáférést biztosít (olvasási, írási, törlési, listázási) a tárolóban lévő blobokhoz.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Erőforrások kezelése a Storage Emulatorban

A Storage Emulator szolgáltatási végpontok eltérnek az Azure Storage-fiókok végpontjaitól. A helyi számítógép nem hajtja végre a tartománynevek feloldását, ami megköveteli, hogy a Storage Emulator-végpontok helyi címek legyenek.

Amikor egy Azure Storage-fiókban kezel egy erőforrást, a következő sémát kell használnia. A fiók neve az URI-állomásnév része, és a címzett erőforrás az URI elérési útja része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI például egy Azure Storage-fiókban található blob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Mivel a helyi számítógép nem hajtja végre a tartománynevek feloldását, a fiók neve az URI elérési útja része az állomásnév helyett. Használja a következő URI-formátumot egy erőforráshoz a Storage Emulatorban:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Például a következő címről lehet hozzáférni egy blobhoz a Storage Emulatorban:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

A Storage Emulator szolgáltatás-végpontok a következők:

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue szolgáltatás: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Másodlagos fiók kezelése RA-GRS

A 3,1-es verziótól kezdődően a Storage Emulator támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). A másodlagos helyet a fióknév hozzáfűzésével érheti el. Például a következő címről lehet hozzáférni egy blobhoz a Storage Emulator írásvédett másodlagos funkciójával:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> A másodlagos és a Storage-emulátor programozott eléréséhez használja a Storage ügyféloldali kódtárat a .NET 3,2-es vagy újabb verziójához. További részletekért tekintse meg a [.net-hez készült ügyféloldali kódtár Microsoft Azure Storage](/previous-versions/azure/dn261237(v=azure.100)) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>A Storage Emulator parancssori eszközének dokumentációja

Az 3,0-es verziótól kezdődően a rendszer a Storage Emulator indításakor megjeleníti a konzol ablakát. Az emulátor elindításához és leállításához használja a konzol ablakának parancssorát. Lekérdezheti az állapotot, és egyéb műveleteket is végrehajthat a parancssorból.

> [!NOTE]
> Ha a Microsoft Azure számítási emulátor telepítve van, a Storage Emulator indításakor megjelenik egy rendszertálca ikon. Kattintson a jobb gombbal az ikonra egy olyan menü megjelenítéséhez, amely grafikus módot biztosít a tároló-emulátor elindításához és leállításához.
>
>

### <a name="command-line-syntax"></a>Parancssori szintaxis

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Beállítások

A beállítások listájának megtekintéséhez írja be a `/help` parancsot a parancssorba.

| Beállítás | Leírás | Parancs | Argumentumok |
| --- | --- | --- | --- |
| **Kezdés** |Elindítja a Storage Emulatort. |`AzureStorageEmulator.exe start [-inprocess]` |*-Újrafeldolgozás*: indítsa el az emulátort az aktuális folyamatban az új folyamat létrehozása helyett. |
| **Leállítás** |Leállítja a Storage-emulátort. |`AzureStorageEmulator.exe stop` | |
| **Állapot** |A Storage Emulator állapotának kinyomtatása. |`AzureStorageEmulator.exe status` | |
| **Törlés** |Törli a parancssorban megadott összes szolgáltatásban lévő összes értéket. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: törli a blob-adatvesztést. <br/>*üzenetsor*: a várólista-adathalmazok törlése. <br/>*tábla*: a tábla tartalmának törlése. <br/>*összes*: törli az összes szolgáltatás összes adatértékét. |
| **Init** |Az emulátor beállításához egyszeri inicializálás szükséges. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Server serverName\instanceName*: az SQL-példányt üzemeltető kiszolgálót adja meg. <br/>*-Sqlinstance példánynév*: az alapértelmezett kiszolgálói példányban használandó SQL-példány nevét adja meg. <br/>*-forcecreate*: az SQL-adatbázis létrehozásának kényszerítése, még akkor is, ha már létezik. <br/>*-skipcreate*: kihagyja az SQL-adatbázis létrehozását. Ez elsőbbséget élvez a forcecreate szemben.<br/>*-reserveports*: megkísérli a szolgáltatásokhoz társított http-portok foglalását.<br/>*-unreserveports*: megkísérli eltávolítani a szolgáltatásokhoz társított http-portok foglalásait. Ez elsőbbséget élvez a reserveports szemben.<br/>*-InProcess*: az új folyamat létrehozása helyett az aktuális folyamat inicializálását hajtja végre. A jelenlegi folyamatot emelt szintű engedélyekkel kell elindítani, ha módosítja a portok foglalását. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>A Storage Emulator és az Azure Storage közötti különbségek

Mivel a Storage Emulator egy helyi emulált környezet, az emulátor és a felhőben lévő Azure Storage-fiók használata között különbségek vannak:

* A Storage Emulator csak egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat.
* A Storage Emulator nem méretezhető tárolási szolgáltatás, és nem támogatja nagy számú egyidejű ügyfél használatát.
* A [Storage Emulator erőforrásainak kezelése](#addressing-resources-in-the-storage-emulator)című témakörben leírtak szerint az erőforrásokat a Storage-emulátorban és egy Azure Storage-fiókban eltérően kezeli a rendszer. A különbség az, hogy a tartománynév-feloldás a felhőben érhető el, de a helyi számítógépen nem.
* A 3,1-es verziótól kezdődően a Storage Emulator-fiók támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). Az emulátorban minden fiókhoz engedélyezve van az RA-GRS, és az elsődleges és a másodlagos replikák között soha nem marad késés. A blob szolgáltatás statisztikáinak beolvasása, a várólista-szolgáltatás statisztikáinak beolvasása és a Table szolgáltatás statisztikáinak beolvasása támogatott a másodlagos fiókban, és a válasz elem értékét mindig a `LastSyncTime` mögöttes SQL-adatbázisnak megfelelő aktuális időpontra fogja visszaadni.
* A file Service és az SMB protokoll szolgáltatási végpontok jelenleg nem támogatottak a Storage Emulatorban.
* Ha az emulátor által nem támogatott tárolási szolgáltatások valamelyik verzióját használja, az emulátor VersionNotSupportedByEmulator-hibát ad vissza (HTTP-állapotkód: 400 – hibás kérés).

### <a name="differences-for-blob-storage"></a>BLOB Storage-beli különbségek

Az emulátorban a blob Storage-ra a következő különbségek érvényesek:

* A Storage Emulator legfeljebb 2 GB méretű blob-méreteket támogat.
* A Blobok nevének maximális hossza a Storage-emulátorban 256 karakter, míg az Azure Storage-beli blob-nevek maximális hossza 1024 karakter.
* A növekményes másolás lehetővé teszi a felülírt Blobok másolását, így a szolgáltatás hibáját adja vissza.
* Az oldal-tartományok diff beolvasása nem működik a növekményes másolási blob használatával másolt Pillanatképek között.
* Egy Put blob művelet egy olyan blobnál lehet sikeres, amely egy aktív bérlettel rendelkezik a Storage-emulátorban, még akkor is, ha a kérelemben nincs megadva a címbérlet azonosítója.
* Az emulátor nem támogatja a Blobok hozzáfűzését. Egy hozzáfűző blobon végrehajtott művelet egy FeatureNotSupportedByEmulator-hibát ad vissza (HTTP 400 – hibás kérés).

### <a name="differences-for-table-storage"></a>Táblázatos tárolással kapcsolatos különbségek

A következő eltérések vonatkoznak az emulátorban található Table Storage-ra:

* A Storage Emulator Table servicejában a dátum tulajdonságai csak a SQL Server 2005 által támogatott tartományokat támogatják (a 1753. január 1-től). A 1753. január 1. előtti összes dátum erre az értékre módosult. A dátumok pontossága a SQL Server 2005 pontosságára korlátozódik, ami azt jelenti, hogy a dátumok pontosak egy másodperc 1/300th.
* A Storage Emulator a partíciós kulcs és a sor kulcsa tulajdonságának értékét kevesebb mint 512 bájtra támogatja. A fióknév, a tábla neve és a kulcs tulajdonságainak neve összesen nem haladhatja meg a 900 bájtot.
* Egy tábla sorainak teljes mérete a Storage-emulátorban 1 MB-nál kevesebb értékre van korlátozva.
* A Storage-emulátorban az adattípus tulajdonságai `Edm.Guid` vagy `Edm.Binary` csak a `Equal (eq)` `NotEqual (ne)` lekérdezési szűrő sztringek és az összehasonlító operátorok támogatása.

### <a name="differences-for-queue-storage"></a>A várólista-tárolással kapcsolatos különbségek

Nincsenek eltérések az emulátorban található üzenetsor-tároláshoz.

## <a name="storage-emulator-release-notes"></a>A Storage Emulator kibocsátási megjegyzései

### <a name="version-510"></a>5,10-es verzió

* A Storage Emulator nem utasítja el a tárolási szolgáltatások 2019-07-07-es verzióját a blob-, üzenetsor-és Table service-végpontokon.

### <a name="version-59"></a>5,9-es verzió

* A Storage Emulator nem utasítja el a tárolási szolgáltatások 2019-02-02-es verzióját a blob-, üzenetsor-és Table service-végpontokon.

### <a name="version-58"></a>5,8-es verzió

* A Storage Emulator nem utasítja el a tárolási szolgáltatások 2018-11-09-es verzióját a blob-, üzenetsor-és Table service-végpontokon.

### <a name="version-57"></a>5,7-es verzió

* Kijavítva egy olyan hibát, amely összeomlást okoz, ha a naplózás engedélyezve volt.

### <a name="version-56"></a>5,6-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2018-03-28-os verzióját.

### <a name="version-55"></a>5,5-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-11-09-os verzióját.
* Támogatás lett hozzáadva a blob **által létrehozott** tulajdonsághoz, amely a blob létrehozási idejét adja vissza.

### <a name="version-54"></a>5,4-es verzió

* A telepítési stabilitás javítása érdekében az emulátor már nem próbálkozik a portok lefoglalására a telepítési időpontban. Ha a portok foglalását szeretné használni, használja az **init** parancs *-reserveports* kapcsolóját a megadásához.

### <a name="version-53"></a>5,3-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-07-29-os verzióját.

### <a name="version-52"></a>5,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-04-17-os verzióját.
* Kijavítva egy hiba, ahol a Table tulajdonság értékei nem lettek megfelelően kódolva.

### <a name="version-51"></a>5,1-es verzió

* Kijavítva egy olyan hibát, amelyben a Storage Emulator a `DataServiceVersion` fejlécet olyan válaszokban adta vissza, amelyekben a szolgáltatás nem volt elérhető.

### <a name="version-50"></a>5,0-es verzió

* A Storage Emulator telepítője már nem ellenőrzi a meglévő MSSQL-és .NET-keretrendszer telepítéseit.
* A Storage Emulator telepítője már nem hozza létre az adatbázist a telepítés részeként. A rendszer a rendszerindítás részeként továbbra is létrehozza az adatbázist.
* Az adatbázis létrehozása már nem igényel jogosultságszint-emelést.
* A portok foglalása már nem szükséges az indításhoz.
* A következő lehetőségeket adja hozzá a következőkhöz `init` : `-reserveports` (jogosultságszint emelése szükséges), `-unreserveports` (jogosultságszint-emelést igényel), `-skipcreate` .
* A rendszertálca ikonján a Storage Emulator felhasználói felülete lehetőség most elindítja a parancssori felületet. A régi GUI már nem érhető el.
* Egyes DLL-fájlok el lettek távolítva vagy átnevezve lettek.

### <a name="version-46"></a>4,6-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2016-05-31-os verzióját.

### <a name="version-45"></a>4,5-es verzió

* Kijavítva egy olyan hibát, amely miatt a telepítés és az inicializálás sikertelen volt, ha a háttér-adatbázis át lett nevezve.

### <a name="version-44"></a>4,4-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-12-11-os verzióját.
* A Storage Emulator blob-adatokhoz tartozó Garbage-gyűjteménye hatékonyabban működik, ha nagy mennyiségű blobot kezel.
* Kijavított egy hibát, amely miatt a tároló ACL XML-fájlja némileg másképpen ellenőrizhető, hogy a tárolási szolgáltatás hogyan működik.
* Kijavítva egy olyan hibát, amely néha a maximális és a minimális dátum/idő értéket okozta, hogy a rendszer a helytelen időzónában jelentsen.

### <a name="version-43"></a>4,3-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-07-08-os verzióját.

### <a name="version-42"></a>4,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-04-05-os verzióját.

### <a name="version-41"></a>4,1-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-02-21-os verzióját. Nem támogatja az új hozzáfűző blob-funkciókat.
* Az emulátor most egy értelmes hibaüzenetet ad vissza a tárolási szolgáltatások nem támogatott verzióihoz. Javasoljuk, hogy az emulátor legújabb verzióját használja. Ha VersionNotSupportedByEmulator hibaüzenetet kap (HTTP-állapotkód: 400 – hibás kérés), töltse le az emulátor legújabb verzióját.
* Kijavítva egy olyan hibát, amelyben a versenyhelyzet feltétele miatt helytelenek voltak a tábla entitások az egyidejű egyesítési műveletek során.

### <a name="version-40"></a>4,0-es verzió

* A Storage Emulator végrehajtható fájljának neve *AzureStorageEmulator.exe*.

### <a name="version-32"></a>3,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2014-02-14-os verzióját. A file Service-végpontok jelenleg nem támogatottak a Storage Emulatorban. Az 2014-02-14-es verzióval kapcsolatos részletekért tekintse meg [Az Azure Storage szolgáltatás verziószámozását](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>3,1-es verzió

* Az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS) mostantól támogatott a Storage Emulatorban. A `Get Blob Service Stats` , a `Get Queue Service Stats` és az API- `Get Table Service Stats` k támogatottak a másodlagos fiók esetében, és mindig a LastSyncTime válasz elemének értékét adják vissza, amely az alapul szolgáló SQL-adatbázisnak megfelelően aktuális idő lesz. A másodlagos és a Storage-emulátor programozott eléréséhez használja a Storage ügyféloldali kódtárat a .NET 3,2-es vagy újabb verziójához. A részletekért tekintse meg a .NET-hez készült Microsoft Azure Storage ügyféloldali kódtárat ismertető témakört.

### <a name="version-30"></a>3,0-es verzió

* Az Azure Storage-emulátor már nem ugyanarra a csomagba kerül, mint a Compute Emulator.
* A Storage Emulator grafikus felhasználói felülete elavult. Egy parancsfájl-parancssori felület váltotta fel. A parancssori felületen a további tudnivalókat lásd: Storage Emulator Command-Line Tool Reference. A grafikus felület továbbra is megtalálható a 3,0-es verzióban, de csak akkor érhető el, ha a számítási emulátor telepítve van, ha a jobb gombbal a rendszertálca ikonra kattint, és kiválasztja a Storage Emulator felhasználói felületének megjelenítése lehetőséget.
* Az Azure Storage-szolgáltatások 2013-08-15-es verziója már teljes mértékben támogatott. (Ezt a verziót korábban csak a Storage Emulator 2.2.1 előzetes verziója támogatja.)

## <a name="next-steps"></a>Következő lépések

* Értékelje ki a többplatformos, Közösség által karbantartott nyílt forráskódú Storage Emulator- [Azurite](https://github.com/azure/azurite). 
* A .NET-et [használó Azure Storage-minták](./storage-samples-dotnet.md) több, az alkalmazás fejlesztésekor felhasználható kódrészletre mutató hivatkozásokat is tartalmaznak.
* A [Microsoft Azure Storage Explorer](https://storageexplorer.com) használatával dolgozhat a felhőalapú Storage-fiókban található erőforrásokkal, és a Storage emulatorban is.

## <a name="see-also"></a>Lásd még:

* [Helyi Azure Storage-fejlesztés Azurite, Azure SDK-val és Azure Storage Explorer](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
