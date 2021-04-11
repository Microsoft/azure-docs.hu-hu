---
title: Alkalmazáscsomag üzembe helyezése számítási csomópontokon
description: A Azure Batch alkalmazáscsomag funkciójának használatával egyszerűen kezelhet több alkalmazást és verziót a Batch számítási csomópontokon történő telepítéshez.
ms.topic: how-to
ms.date: 03/24/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 17729b2b6a2c4faba0554339a382cad559e9894a
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210499"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Alkalmazások üzembe helyezése számítási csomópontokhoz batch-alkalmazási csomagokkal

Az alkalmazáscsomag leegyszerűsítheti a kódot a Azure Batch-megoldásban, és egyszerűbbé teheti a tevékenységek által futtatott alkalmazások kezelését. Az alkalmazáscsomag használatával feltölthet és kezelhet a feladatok által futtatott alkalmazások több verzióját, beleértve azok támogató fájljait is. Ezután automatikusan üzembe helyezhet egy vagy több alkalmazást a készlet számítási csomópontjain.

Az alkalmazáscsomag létrehozásának és kezelésének API-jai a [Batch Management .net](/dotnet/api/overview/azure/batch/management) könyvtár részét képezik. Az alkalmazáscsomag számítási csomópontra történő telepítésének API-jai a [Batch .net](/dotnet/api/overview/azure/batch/client) -könyvtár részei. Az összehasonlítható funkciók a más nyelvekhez elérhető batch API-k.

Ez a cikk az alkalmazáscsomag feltöltését és kezelését ismerteti a Azure Portalban. Azt is bemutatja, hogyan telepítheti őket a készlet számítási csomópontjain a [Batch .net](/dotnet/api/overview/azure/batch/client) -kódtár használatával.

## <a name="application-package-requirements"></a>Alkalmazáscsomag követelményei

Az alkalmazáscsomag használatához [egy Azure Storage-fiókot kell összekapcsolnia](#link-a-storage-account) a Batch-fiókkal.

A Batch-fiókokban és az alkalmazáscsomag maximális méretén belül korlátozások vonatkoznak az alkalmazások és az alkalmazási csomagok számára. További információ: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md).

> [!NOTE]
> A 2017. július 5. előtt létrehozott batch-készletek nem támogatják az alkalmazáscsomag használatát (kivéve, ha azokat az 2016. március 10. után hozták létre Cloud Services konfiguráció használatával). Az itt leírt alkalmazáscsomag-szolgáltatás felülbírálja a szolgáltatás korábbi verzióiban elérhető batch Apps szolgáltatást.

## <a name="understand-applications-and-application-packages"></a>Az alkalmazások és az alkalmazások csomagjainak ismertetése

Azure Batchon belül egy *alkalmazás* olyan verziószámú bináris fájlokra hivatkozik, amelyek automatikusan letöltődnek a készlet számítási csomópontjaira. Egy alkalmazás egy vagy több *alkalmazáscsomag-csomagot* tartalmaz, amelyek az alkalmazás különböző verzióit képviselik.

Minden *alkalmazáscsomag* egy. zip fájl, amely tartalmazza az alkalmazás bináris fájljait és a hozzá tartozó fájlokat. Csak a. zip formátumot támogatja a rendszer.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Az alkalmazások és az alkalmazáscsomag magas szintű nézetét bemutató ábra.":::

Az alkalmazáscsomag megadható a készlet vagy a feladat szintjén is.

- A **készlet alkalmazáscsomag** a készlet minden csomópontjára telepítve van. Az alkalmazások akkor lesznek telepítve, amikor egy csomópont egy készlethez csatlakozik, és újraindul, vagy alaphelyzetbe áll.
  
    A készlet alkalmazáscsomag akkor megfelelő, ha a készletben lévő összes csomópont végrehajtja a feladat feladatait. Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomag központi telepítését. Egy meglévő készlet csomagjait is hozzáadhat vagy frissíthet. Új csomag meglévő készletre való telepítéséhez újra kell indítania a csomópontokat.

- A **Task Application-csomagok** központi telepítése csak olyan számítási csomópontra történik, amely egy feladat futtatására van ütemezve, közvetlenül a Feladat parancssorának futtatása előtt. Ha a megadott alkalmazáscsomag és verzió már szerepel a csomóponton, a rendszer nem telepíti újra, és a meglévő csomagot használja.
  
    A Task Application-csomagok olyan megosztott készletű környezetekben hasznosak, ahol a különböző feladatok egy készleten futnak, és a készlet nem törlődik, ha a feladat befejeződik. Ha a feladat kevesebb feladatot tartalmaz, mint a készlet csomópontjai, a feladat-alkalmazás csomagjai csökkenthetik az adatátvitelt, mivel az alkalmazás csak a feladatokat futtató csomópontokra van telepítve.
  
    Más forgatókönyvek, amelyek kihasználhatják a feladat-alkalmazási csomagokat, olyan feladatok, amelyek nagy alkalmazást futtatnak, de csak néhány feladathoz. Előfordulhat például, hogy a feladatok alkalmazásai hasznosak lehetnek az előzetes feldolgozási fázishoz vagy egyesítési feladathoz.

Az alkalmazáscsomag esetében a készlet indítási feladatának nem kell megadnia a csomópontokon telepítendő egyes erőforrás-fájlok hosszú listáját. Az alkalmazás fájljainak több verzióját sem kell manuálisan kezelnie az Azure Storage-ban vagy a csomópontjain. Nem kell aggódnia a [sas URL-címeinek](../storage/common/storage-sas-overview.md) létrehozásával, hogy hozzáférést biztosítson a Storage-fiókban található fájlokhoz. A Batch az Azure Storage háttérben működik az alkalmazáscsomag tárolásához és a számítási csomópontokon való üzembe helyezéséhez.

> [!NOTE]
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Ha az indítási tevékenység meghaladja ezt a korlátot, az alkalmazáscsomag használata egy másik lehetőség. Létrehozhat egy. zip fájlt is, amely tartalmazza az erőforrás-fájlokat, feltöltheti blobként az Azure Storage-ba, majd kicsomagolhatja az indítási feladat parancssorából.

## <a name="upload-and-manage-applications"></a>Alkalmazások feltöltése és kezelése

A Batch-fiókban lévő alkalmazáscsomag kezeléséhez használhatja a [Azure Portal](https://portal.azure.com) vagy a Batch Management API-kat. A következő szakasz ismerteti a Storage-fiókok összekapcsolását, valamint az alkalmazások és alkalmazáscsomag hozzáadását és kezelését a Azure Portal.

> [!NOTE]
> Egy [ARM-sablon](quick-create-template.md) [Microsoft.BatCH/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) -erőforrásában megadhatja az alkalmazás értékeit, de jelenleg nem lehet ARM-sablonnal felvenni a Batch-fiókban használni kívánt alkalmazáscsomag-csomagokat. Fel kell töltenie őket a társított Storage-fiókba az [alább](#add-a-new-application)leírtak szerint.

### <a name="link-a-storage-account"></a>Storage-fiók csatolása

Az alkalmazáscsomag használatához egy [Azure Storage-fiókot](accounts.md#azure-storage-accounts) kell összekapcsolnia a Batch-fiókkal. A Batch szolgáltatás a társított Storage-fiókot fogja használni az alkalmazáscsomag tárolásához. Javasoljuk, hogy hozzon létre egy Storage-fiókot kifejezetten a Batch-fiókjával való használatra.

Ha még nem konfigurálta a Storage-fiókot, a Azure Portal figyelmeztetést jelenít meg, amikor első alkalommal kiválasztja az **alkalmazásokat** a Batch-fiókban. Ha egy Storage-fiókot szeretne csatolni a Batch-fiókhoz, válassza ki a **Storage-fiók** elemet a **Figyelmeztetési** ablakban, majd válassza ismét a **Storage-fiók** lehetőséget.

A két fiók összekapcsolása után a Batch automatikusan üzembe helyezheti a társított Storage-fiókban tárolt csomagokat a számítási csomópontokon.

> [!IMPORTANT]
> A [Tűzfalszabályok](../storage/common/storage-network-security.md)használatára konfigurált Azure Storage-fiókkal rendelkező alkalmazáscsomag nem használható, vagy ha a **hierarchikus névtér** engedélyezve értékre **van** állítva.

A Batch szolgáltatás az Azure Storage-t használja az alkalmazáscsomag blokkolási blobként való tárolására. A blob-adatblokkok esetében a [szokásos módon kell fizetni](https://azure.microsoft.com/pricing/details/storage/) , és az egyes csomagok mérete nem haladhatja meg a Blobok maximális méretét. További információ: [Azure Storage skálázhatósági és teljesítménybeli célok a Storage-fiókokhoz](../storage/blobs/scalability-targets.md). A költségek csökkentése érdekében ügyeljen rá, hogy az alkalmazáscsomag mérete és száma, valamint az elavult csomagok rendszeres eltávolítása történjen.

### <a name="view-current-applications"></a>Aktuális alkalmazások megtekintése

Ha szeretné megtekinteni az alkalmazásokat a Batch-fiókban, válassza az **alkalmazások** lehetőséget a bal oldali navigációs menüben.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Képernyőkép a Azure Portal alkalmazások menüjéről.":::

Ha ezt a menüelemet választja, megnyílik az **alkalmazások** ablak. Ez az ablak a fiókban lévő egyes alkalmazások AZONOSÍTÓját, valamint a következő tulajdonságokat jeleníti meg:

- **Csomagok**: az alkalmazáshoz társított verziók száma.
- **Alapértelmezett verzió**: ha alkalmazható, akkor az alkalmazás verziója lesz telepítve, ha az alkalmazás telepítésekor nem ad meg verziót.
- **Frissítések engedélyezése**: Megadja, hogy a csomagok frissítései és törlése engedélyezett-e.

[Az alkalmazáscsomag](files-and-directories.md) számítási csomóponton való megjelenítéséhez navigáljon a Batch-fiókjához a Azure Portal. Válassza a **készletek** lehetőséget. Ezután válassza ki a számítási csomópontot tartalmazó készletet. Válassza ki azt a számítási csomópontot, amelyen az alkalmazáscsomag telepítve van, és nyissa meg az **alkalmazások** mappát.

### <a name="view-application-details"></a>Alkalmazás részleteinek megtekintése

Egy alkalmazás részleteinek megtekintéséhez válassza ki azt az **alkalmazások** ablakban. Az alkalmazáshoz a következő beállításokat állíthatja be.

- **Frissítések engedélyezése**: azt jelzi, hogy lehet [-e frissíteni vagy törölni](#update-or-delete-an-application-package)az alkalmazáscsomag. Az alapértelmezett érték az **Igen**. Ha a **nem** értékre van állítva, a meglévő alkalmazáscsomag nem frissíthető és nem törölhető, de az új alkalmazáscsomag-verziók továbbra is hozzáadhatók.
- **Alapértelmezett verzió**: az alkalmazás telepítésekor használandó alapértelmezett alkalmazáscsomag, ha nincs megadva verzió.
- **Megjelenítendő név**: a Batch-megoldás által az alkalmazással kapcsolatos információk megjelenítésére használt rövid név. Ezt a nevet használhatja például egy olyan szolgáltatás felhasználói felületén, amelyet az ügyfelek számára a Batch használatával biztosít.

### <a name="add-a-new-application"></a>Új alkalmazás hozzáadása

Új alkalmazás létrehozásához vegyen fel egy alkalmazáscsomag-csomagot, és adjon meg egy egyedi alkalmazás-azonosítót.

A Batch-fiókban válassza az **alkalmazások** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Képernyőkép a Azure Portal új alkalmazás-létrehozási folyamatáról.":::

Adja meg a következő információkat:

- **Alkalmazás azonosítója**: az új alkalmazás azonosítója.
- **Version**": a feltöltött alkalmazáscsomag verziója.
- **Alkalmazáscsomag**: az alkalmazás végrehajtásához szükséges bináris fájlt és az azokat támogató fájlokat tartalmazó. zip fájl.

A megadott **alkalmazás-azonosítónak** és **verziónak** a következő követelményeknek kell megfelelnie:

- Windows-csomópontokon az azonosító alfanumerikus karakterek, kötőjelek és aláhúzások tetszőleges kombinációjával rendelkezhet. Linux-csomópontokon csak alfanumerikus karakterek és aláhúzások engedélyezettek.
- Legfeljebb 64 karakterből állhat.
- A Batch-fiókon belül egyedinek kell lennie.
- Az azonosítók a kis-és nagybetűk megkülönböztetése

Ha elkészült, válassza a **Küldés** lehetőséget. Miután feltöltötte a. zip-fájlt az Azure Storage-fiókjába, a portál megjelenít egy értesítést. A feltöltött fájl méretétől és a hálózati kapcsolatok sebességétől függően ez eltarthat egy ideig.

### <a name="add-a-new-application-package"></a>Új alkalmazáscsomag hozzáadása

Egy meglévő alkalmazás alkalmazáscsomag-verziójának hozzáadásához válassza ki az alkalmazást a Batch-fiók **alkalmazások** szakaszában, majd válassza a **Hozzáadás** lehetőséget.

Ahogy az új alkalmazásnál, adja meg az új csomag **verzióját** , töltse fel a. zip fájlt az **alkalmazáscsomag** mezőbe, majd válassza a **Küldés** lehetőséget.

### <a name="update-or-delete-an-application-package"></a>Alkalmazáscsomag frissítése vagy törlése

Meglévő alkalmazáscsomag frissítéséhez vagy törléséhez válassza ki az alkalmazást a Batch-fiók **alkalmazások** szakaszában. Válassza ki a módosítani kívánt alkalmazáscsomag sorában található három pontot, majd válassza ki a végrehajtani kívánt műveletet.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Képernyőfelvétel a Azure Portal alkalmazás csomagjainak frissítési és törlési lehetőségeiről.":::

Ha a **frissítés** lehetőséget választja, feltöltheti egy új. zip fájlt. Ez felülírja az adott verzióhoz feltöltött előző. zip fájlt.

Ha a **Törlés** lehetőséget választja, a rendszer felszólítja, hogy erősítse meg a verzió törlését. Ha **az OK gombra** kattint, a Batch törli a. zip fájlt az Azure Storage-fiókból. Ha törli az alkalmazás alapértelmezett verzióját, a rendszer eltávolítja az **alapértelmezett verzió** -beállítást az adott alkalmazáshoz.

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése számítási csomópontokon

Most, hogy megismerte, hogyan kezelheti az alkalmazás-csomagokat a Azure Portalban, megtudhatja, hogyan helyezheti üzembe őket a számítási csomópontokon, és hogyan futtathatja azokat batch-feladatokkal.

### <a name="install-pool-application-packages"></a>Készlet alkalmazáscsomag telepítése

Egy adott készlet összes számítási csomópontján lévő alkalmazáscsomag telepítéséhez válasszon egy vagy több alkalmazáscsomag-referenciát a készlethez. A készlethez megadott alkalmazáscsomag minden olyan számítási csomóponton telepítve van, amely összekapcsolja a készletet, illetve bármely olyan csomóponton, amely újra van indítva vagy rendszerképben van.

A Batch .NET-ben válasszon egy vagy több [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) , amikor új készletet hoz létre, vagy egy meglévő készlethez. A [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) osztály a készlet számítási csomópontjain telepítendő alkalmazás-azonosítót és verziót határozza meg.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Ha egy alkalmazáscsomag üzembe helyezése meghiúsul, a Batch szolgáltatás [használhatatlanként](/dotnet/api/microsoft.azure.batch.computenode.state)jelöli meg a csomópontot, és a csomóponton nem ütemezhető feladatok végrehajtásra. Ha ez történik, indítsa újra a csomópontot a csomag központi telepítésének újraindításához. A csomópont újraindítása azt is lehetővé teszi, hogy a feladatütemezés ismét elérhető legyen a csomóponton.

### <a name="install-task-application-packages"></a>Task Application-csomagok telepítése

A készlethez hasonlóan a tevékenységhez tartozó alkalmazáscsomag-referenciák is megadhatók. Ha egy adott tevékenység egy csomóponton való futásra van ütemezve, a csomag letöltése és kibontása közvetlenül a Feladat parancssorának végrehajtása előtt történik. Ha egy adott csomag és verzió már telepítve van a csomóponton, a csomag nem töltődik le, és a rendszer a meglévő csomagot használja.

A Task alkalmazáscsomag telepítéséhez konfigurálja a feladat [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) tulajdonságát:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>A telepített alkalmazások végrehajtása

A készlethez vagy feladathoz megadott csomagokat a rendszer letölti és kicsomagolja a csomóponton belüli megnevezett könyvtárba `AZ_BATCH_ROOT_DIR` . A Batch egy környezeti változót is létrehoz, amely a nevesített könyvtár elérési útját tartalmazza. A feladat parancssori sorai ezt a környezeti változót használják a csomóponton lévő alkalmazásra való hivatkozáskor.

Windows-csomópontokon a változó formátuma a következő:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux-csomópontokon a formátum némileg eltérő. A pont (.), a kötőjel (-) és a Number Signs (#) a környezeti változóban található aláhúzásra van leképezve. Azt is vegye figyelembe, hogy a rendszer megőrzi az alkalmazás AZONOSÍTÓjának esetét. Például:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` és `version` olyan értékek, amelyek megfelelnek az üzembe helyezéshez megadott alkalmazás-és csomag-verziónak. Ha például az alkalmazás- *turmixgép* 2,7-es verzióját kell telepíteni Windows-csomópontokon, a feladat parancssora ezt a környezeti változót fogja használni a fájlokhoz való hozzáféréshez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux-csomópontokon ebben a formátumban válassza a környezeti változót. A pontok (.), kötőjelek (-) és a számok jeleinek (#) lelapulása és az alkalmazás AZONOSÍTÓjának megőrzése:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Alkalmazáscsomag feltöltésekor megadhat egy alapértelmezett verziót a számítási csomópontokon való üzembe helyezéshez. Ha egy alkalmazáshoz alapértelmezett verziót adott meg, akkor kihagyhatja a verzió utótagját az alkalmazásra való hivatkozáskor. Megadhatja az alkalmazás alapértelmezett verzióját a Azure Portalban az **alkalmazások** ablakban, ahogyan az [alkalmazások feltöltése és kezelése](#upload-and-manage-applications)című részben látható.

Ha például a "2,7" értéket állítja be alapértelmezettként az Application *Blender* számára, és a feladatok a következő környezeti változóra hivatkoznak, akkor a Windows-csomópontok a 2,7-es verziót fogják végrehajtani:

`AZ_BATCH_APP_PACKAGE_BLENDER`

A következő kódrészlet egy példát mutat be, amely elindítja a *Blender* alkalmazás alapértelmezett verzióját:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> A számítási csomópont környezeti beállításaival kapcsolatos további információkért lásd: [környezeti beállítások a feladatokhoz](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése

Ha egy meglévő készlet már konfigurálva van egy alkalmazáscsomag használatával, megadhat egy új csomagot a készlethez. Ez a következőkkel jár:

- A Batch szolgáltatás telepíti az újonnan megadott csomagot az összes olyan új csomóponton, amely csatlakozik a készlethez, illetve az újraindított vagy rendszerképbe telepített meglévő csomópontokon.
- A csomagok hivatkozásainak frissítésekor a készletben már szereplő számítási csomópontok nem telepítik automatikusan az új alkalmazáscsomag-csomagot. Ezeket a számítási csomópontokat újra kell indítani vagy rendszerképbe kell állítani az új csomag fogadásához.
- Új csomag telepítésekor a létrehozott környezeti változók az új alkalmazáscsomag-hivatkozásokat tükrözik.

Ebben a példában a meglévő készlet a [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences)egyikének megfelelően konfigurált *blender* -alkalmazás 2,7-es verziójával rendelkezik. Ha frissíteni szeretné a készlet csomópontjait a b verziójú 2.76, egy új [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) kell megadnia az új verzióval, és véglegesíteni kell a változást.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Most, hogy az új verzió konfigurálva lett, a Batch szolgáltatás telepíti a 2.76-es verziót a készlethez csatlakozó új csomópontra. Ha a 2.76 b-t a készletben már szereplő csomópontokon szeretné telepíteni, indítsa újra vagy rendszerképbe. Vegye figyelembe, hogy az újraindított csomópontok megőrzik a fájlokat a korábbi csomagok központi telepítéséről.

## <a name="list-the-applications-in-a-batch-account"></a>Batch-fiókban lévő alkalmazások listázása

A Batch-fiókban lévő alkalmazásokat és azok csomagjait a [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) metódus használatával listázhatja.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A [Batch REST API](/rest/api/batchservice) Emellett támogatást biztosít az alkalmazáscsomag működéséhez. Tekintse meg például az [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) elemet a telepítendő csomagok megadásához [, valamint az alkalmazás adatainak](/rest/api/batchservice/application) beszerzéséhez.
- Ismerje meg, hogyan [felügyelheti Azure batch fiókokat és kvótákat a Batch Management .net-](batch-management-dotnet.md)tel. A [Batch Management .net](/dotnet/api/overview/azure/batch/management) -függvénytár lehetővé teheti a fiókok létrehozási és törlési funkcióit a Batch-alkalmazáshoz vagy-szolgáltatáshoz.
