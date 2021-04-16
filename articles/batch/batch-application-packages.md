---
title: Alkalmazáscsomagok üzembe helyezése számítási csomópontokon
description: Az alkalmazáscsomagok szolgáltatásának Azure Batch több alkalmazás és verzió egyszerű kezeléséhez a Batch számítási csomópontokon való telepítéshez.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 9c4b40f0e99475fc0b19ec94a14f67af131e5f59
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389383"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Alkalmazások üzembe helyezése számítási csomópontokon Batch-alkalmazáscsomagokkal

Az alkalmazáscsomagok leegyszerűsítik a Azure Batch, és megkönnyítik a tevékenységek által futtatott alkalmazások kezelését. Az alkalmazáscsomagokkal a tevékenységek által futtatott alkalmazások több verzióját töltheti fel és kezelheti, beleértve a támogató fájlokat is. Ezután automatikusan üzembe helyezhet egy vagy több ilyen alkalmazást a készlet számítási csomópontjaiban.

Az alkalmazáscsomagok létrehozására és kezelésére szolgáló API-k a [Batch Management .NET-kódtár részét](/dotnet/api/overview/azure/batch/management) képezi. Az alkalmazáscsomagok számítási csomópontra való telepítéséhez szükséges API-k a [Batch .NET-kódtár részét](/dotnet/api/overview/azure/batch/client) képezi. Hasonló funkciók érhetők el az elérhető Batch API-kban más nyelvekhez.

Ez a cikk azt ismerteti, hogyan tölthet fel és kezelhet alkalmazáscsomagokat a Azure Portal. Azt is bemutatja, hogyan telepítheti őket a készlet számítási csomópontjaira a [Batch .NET-kódtára](/dotnet/api/overview/azure/batch/client) segítségével.

## <a name="application-package-requirements"></a>Alkalmazáscsomagokkal kapcsolatos követelmények

Alkalmazáscsomagok használata esetén egy Azure Storage-fiókot kell [összekapcsolni](#link-a-storage-account) a Batch-fiókkal.

A Batch-fiókban található alkalmazások és alkalmazáscsomagok száma és az alkalmazáscsomagok maximális mérete korlátozva van. További információ: A Azure Batch [kvótái és korlátai.](batch-quota-limit.md)

> [!NOTE]
> A 2017. július 5. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat (kivéve, ha 2016. március 10. után hozták létre őket az Cloud Services Configuration használatával). Az itt leírt alkalmazáscsomag-szolgáltatás a szolgáltatás korábbi verzióiban elérhető Batch Apps szolgáltatást írja le.

## <a name="understand-applications-and-application-packages"></a>Az alkalmazások és alkalmazáscsomagok

A Azure Batch alkalmazás verziószámokkal rendelkezik bináris fájlok halmazára, amelyek automatikusan letölthetők a készlet számítási csomópontjaira.  Az alkalmazások egy vagy több *alkalmazáscsomagot tartalmaznak,* amelyek az alkalmazás különböző verzióit képviselik.

Minden *alkalmazáscsomag egy* .zip-fájl, amely tartalmazza az alkalmazás bináris fájljait és a támogató fájlokat. Csak a .zip formátum támogatott.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Az alkalmazások és alkalmazáscsomagok magas szintű nézetét bemutató ábra.":::

Az alkalmazáscsomagokat a készlet vagy a feladat szintjén adhatja meg.

- **A készlet alkalmazáscsomagjai** a készlet minden csomópontján üzembe vannak helyezni. Az alkalmazások üzembe helyezése akkor történik, amikor egy csomópont csatlakozik egy készlethez, és amikor újraindítják vagy rendszerképét újraalkotják.
  
    A készlet alkalmazáscsomagjai akkor megfelelőek, ha a készlet összes csomópontja végrehajtja egy feladat feladatait. A készlet létrehozásakor egy vagy több alkalmazáscsomagot is telepíthet. Meglévő készlet csomagjait is hozzáadhatja vagy frissítheti. Új csomag meglévő készletbe való telepítéséhez újra kell indítania a csomópontokat.

- **A tevékenységalkalmazás-csomagokat** csak egy tevékenység futtatására ütemezett számítási csomóponton telepíti a rendszer, a tevékenység parancssorának futtatása előtt. Ha a megadott alkalmazáscsomag és verzió már a csomóponton van, akkor az nem lesz újra üzembe adva, és a meglévő csomagot használja.
  
    A tevékenységalkalmazás-csomagok megosztott készletű környezetekben hasznosak, ahol a különböző feladatok egy készleten futnak, és a rendszer nem törli a készletet a feladat befejezésekor. Ha a feladat kevesebb tevékenységet tartalmaz, mint a csomópontok a készletben, a tevékenységalkalmazás-csomagok minimalizálják az adatátvitelt, mivel az alkalmazás csak a tevékenységeket futtató csomópontokra van telepítve.
  
    A tevékenységalkalmazás-csomagok által előnyös egyéb forgatókönyvek olyan feladatok, amelyek nagy méretű alkalmazást futtatnak, de csak néhány tevékenységhez. A tevékenységalkalmazások például hasznosak lehetnek nagy terhelésű előfeldolgozási vagy egyesítési feladatokhoz.

Alkalmazáscsomagok esetén a készlet indítási feladatának nem kell hosszú listát megadnia az egyes erőforrásfájlokról, hogy telepítve legyen a csomópontokon. Nem kell manuálisan kezelnie az alkalmazásfájlok több verzióját az Azure Storage-ban vagy a csomópontokon. Nem kell aggódnia az SAS [URL-címek](../storage/common/storage-sas-overview.md) generálása miatt, hogy hozzáférést biztosítson a Tárfiókban lévő fájlokhoz. A Batch a háttérben dolgozik az Azure Storage-ral az alkalmazáscsomagok tárolásához és számítási csomópontokon való üzembe helyezéséhez.

> [!NOTE]
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Ha az indítási tevékenység túllépi ezt a korlátot, az alkalmazáscsomagok használata egy másik lehetőség. Létrehozhat egy .zip-fájlt is, amely tartalmazza az erőforrásfájlokat, feltöltheti blobként az Azure Storage-ba, majd kicsomagolhatja az indítási feladat parancssorában.

## <a name="upload-and-manage-applications"></a>Alkalmazások feltöltése és kezelése

A [Batch-Azure Portal](https://portal.azure.com) a Batch Management API-k használatával kezelheti az alkalmazáscsomagokat a Batch-fiókban. A következő szakaszok ismertetik a tárfiókok csatolásának, valamint az alkalmazások és alkalmazáscsomagok hozzáadásának és Azure Portal.

> [!NOTE]
> Bár az [ARM-sablon](quick-create-template.md) [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) erőforrásában alkalmazásértékeket határozhat meg, jelenleg nem lehet ARM-sablonnal feltölteni a Batch-fiókban használni kívánt alkalmazáscsomagokat. Ezeket fel kell töltenie a csatolt tárfiókba az alább [leírtak szerint.](#add-a-new-application)

### <a name="link-a-storage-account"></a>Tárfiók csatolása

Az alkalmazáscsomagok használatának érdekében egy [Azure Storage-fiókot](accounts.md#azure-storage-accounts) kell összekapcsolni a Batch-fiókkal. A Batch szolgáltatás a társított tárfiókkal fogja tárolni az alkalmazáscsomagokat. Javasoljuk, hogy hozzon létre egy kifejezetten a Batch-fiókkal használható tárfiókot.

Ha még nem konfigurált tárfiókot, a Azure Portal figyelmeztetést jelenít  meg, amikor először választja ki az Alkalmazások lehetőséget a Batch-fiókban. Ha tárfiókot a Batch-fiókhoz csatol,  válassza a Figyelmeztetés ablakban a Tárfiók lehetőséget, majd válassza ismét a **Tárfiók** lehetőséget. 

A két fiók összekapcsolása után a Batch automatikusan üzembe tudja helyezni a csatolt Storage-fiókban tárolt csomagokat a számítási csomópontokon.

> [!IMPORTANT]
> Nem használhat alkalmazáscsomagokat tűzfalszabályokkal konfigurált [](../storage/common/storage-network-security.md)Azure Storage-fiókokkal, vagy ha a **Hierarchikus névtér** engedélyezve **van.**

A Batch szolgáltatás az Azure Storage használatával blokkblobként tárolja az alkalmazáscsomagokat. A [blokkblobadatokért](https://azure.microsoft.com/pricing/details/storage/) a szokásos módon kell fizetni, és az egyes csomagok mérete nem haladhatja meg a blokkblobok maximális méretét. További információ: Az Azure Storage tárfiókok skálázhatósági és [teljesítménycéljai.](../storage/blobs/scalability-targets.md) A költségek minimalizálása érdekében vegye figyelembe az alkalmazáscsomagok méretét és számát, és rendszeresen távolítsa el az elavult csomagokat.

### <a name="view-current-applications"></a>Aktuális alkalmazások megtekintése

A Batch-fiókban található alkalmazások megtekintéséhez válassza az **Alkalmazások lehetőséget** a bal oldali navigációs menüben.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Képernyőkép az alkalmazások menüelemről a Azure Portal.":::

Ha ezt a menüpontot választja, megnyílik az **Alkalmazások** ablak. Ez az ablak a fiókban található alkalmazások azonosítóját és a következő tulajdonságokat jeleníti meg:

- **Csomagok:** Az alkalmazáshoz társított verziók száma.
- **Alapértelmezett verzió:** Ha van ilyen, az alkalmazás telepítésekor telepített alkalmazásverzió, ha nincs megadva verzió.
- **Frissítések engedélyezése:** Meghatározza, hogy a csomagfrissítések és -törlések engedélyezettek-e.

Az alkalmazáscsomag [fájlstruktúrájának](files-and-directories.md) egy számítási csomóponton való megnyitásához lépjen a Batch-fiókra a Azure Portal. Válassza a **Készletek lehetőséget.** ezután válassza ki a számítási csomópontot tartalmazó készletet. Válassza ki azt a számítási csomópontot, amelyre az alkalmazáscsomag telepítve van, és nyissa meg az **applications mappát.**

### <a name="view-application-details"></a>Alkalmazás részleteinek megtekintése

Egy alkalmazás részleteinek az Alkalmazások ablakban való kiválasztásával **megjelenik.** Az alábbi beállításokat konfigurálhatja az alkalmazáshoz.

- **Frissítések engedélyezése:** Azt jelzi, hogy az alkalmazáscsomagok frissíthetők vagy [törölhetők.](#update-or-delete-an-application-package) Az alapértelmezett érték az **Igen.** Ha a **Nem** értéket adja meg, a meglévő alkalmazáscsomagok nem frissíthetők vagy törölhetők, de továbbra is hozzáadhatóak új alkalmazáscsomag-verziók.
- **Alapértelmezett verzió:** Az alkalmazás telepítésekor használt alapértelmezett alkalmazáscsomag, ha nincs megadva verzió.
- **Megjelenített név:** A Batch-megoldás által az alkalmazásra vonatkozó információk megjelenítésekor használható rövid név. Ez a név használható például egy szolgáltatás felhasználói felületén, amit a Batch szolgáltatáson keresztül ad meg az ügyfeleknek.

### <a name="add-a-new-application"></a>Új alkalmazás hozzáadása

Új alkalmazás létrehozásához adjon hozzá egy alkalmazáscsomagot, és adjon meg egy egyedi alkalmazásazonosítót.

A Batch-fiókban válassza az **Alkalmazások,** majd a Hozzáadás **lehetőséget.**

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Képernyőkép az Új alkalmazás létrehozásának folyamatról a Azure Portal.":::

Adja meg a következő információkat:

- **Alkalmazásazonosító:** Az új alkalmazás azonosítója.
- **Version**: A feltöltött alkalmazáscsomag verziója.
- **Alkalmazáscsomag:** Az alkalmazás bináris fájljait és az alkalmazás végrehajtásához szükséges támogató fájlokat tartalmazó .zip fájl.

A **beírott** **alkalmazásazonosítónak** és verziónak az alábbi követelményeknek kell megfelelnie:

- Windows-csomópontokon az azonosító alfanumerikus karakterek, kötőjelek és aláhúzásjelek bármilyen kombinációját tartalmazhatja. Linux-csomópontokon csak alfanumerikus karakterek és aláhúzásjel használata engedélyezett.
- Legfeljebb 64 karakterből állhat.
- Egyedinek kell lennie a Batch-fiókon belül.
- A kis- és a kis- és nagy- és a kis- és a nagy- és a kis- és nagy-

Ha elkészült, válassza a Küldés **lehetőséget.** Miután feltöltötte a .zip fájlt az Azure Storage-fiókjába, a portál megjelenít egy értesítést. A feltöltött fájl méretétől és a hálózati kapcsolat sebességétől függően ez némi időt is eltelhet.

### <a name="add-a-new-application-package"></a>Új alkalmazáscsomag hozzáadása

Ha egy meglévő alkalmazáshoz szeretne alkalmazáscsomag-verziót  hozzáadni, válassza ki az alkalmazást a Batch-fiók Alkalmazások szakaszában, majd válassza a Hozzáadás **lehetőséget.**

Ahogy az új alkalmazáshoz is  tette, adja meg az új csomag Version (Verzió) beállítását, töltse fel a .zip fájlt az **Alkalmazáscsomag** mezőbe, majd válassza a Submit (Küldés) **lehetőséget.**

### <a name="update-or-delete-an-application-package"></a>Alkalmazáscsomag frissítése vagy törlése

Meglévő alkalmazáscsomag frissítéséhez vagy törléséhez válassza ki az alkalmazást a Batch-fiók **Alkalmazások** szakaszában. Válassza a módosítani kívánt alkalmazáscsomag sorában található három pontokat, majd válassza ki a végrehajtani kívánt műveletet.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Képernyőkép az alkalmazáscsomagok frissítési és törlési beállításairól a Azure Portal.":::

Ha a **Frissítés lehetőséget** választja, feltölthet egy új .zip fájlt. Ez lecseréli az előző .zip-fájlt, amely ehhez a verzióhoz volt feltöltve.

Ha a **Törlés lehetőséget** választja, a rendszer kérni fogja a verzió törlésének megerősítését. Az **OK gombra való kiválasztása** után a Batch törli a .zip fájlt az Azure Storage-fiókból. Ha törli egy alkalmazás alapértelmezett verzióját, az alkalmazás alapértelmezett verzióbeállítása el lesz távolítva. 

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése számítási csomópontokra

Most, hogy megtanulta, hogyan kezelheti az alkalmazáscsomagokat a Azure Portal, átveheti, hogyan helyezheti üzembe azokat számítási csomópontokon, és hogyan futtathatja azokat Batch-tevékenységekkel.

### <a name="install-pool-application-packages"></a>Készlet alkalmazáscsomagjainak telepítése

Ha egy adott készlet összes számítási csomópontjára telepíteni szeretné az alkalmazáscsomagot, adjon meg egy vagy több alkalmazáscsomag-hivatkozást a készlethez. A készlethez megadott alkalmazáscsomagok a készlethez csatlakozó összes számítási csomópontra, illetve bármely újraindított vagy rendszerképből újraindított csomópontra telepítve vannak.

A Batch .NET-en adjon meg egy vagy több [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) értéket egy új készlet létrehozásakor, vagy egy meglévő készlethez. Az [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) osztály egy alkalmazásazonosítót és -verziót ad meg a készlet számítási csomópontjaira való telepítéshez.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

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
> Ha egy alkalmazáscsomag üzembe helyezése sikertelen, [](/dotnet/api/microsoft.azure.batch.computenode.state)a Batch szolgáltatás használhatatlanként jelöli meg a csomópontot, és nincsenek ütemezve tevékenységek a csomóponton való végrehajtásra. Ha ez történik, indítsa újra a csomópontot a csomagtelepítés újraindításához. A csomópont újraindítása lehetővé teszi a tevékenységütemezést is a csomóponton.

### <a name="install-task-application-packages"></a>Feladatalkalmazás-csomagok telepítése

A készlethez hasonlóan alkalmazáscsomag-hivatkozásokat is megadhat egy feladathoz. Ha egy tevékenység egy csomóponton való futásra van ütemezve, a rendszer letölti és kibontja a csomagot a tevékenység parancssorának végrehajtása előtt. Ha egy megadott csomag és verzió már telepítve van a csomóponton, a rendszer nem letölti a csomagot, és a meglévő csomagot használja.

Feladatalkalmazás-csomag telepítéséhez konfigurálja a tevékenység [CloudTask.ApplicationPackageReferences tulajdonságát:](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences)

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

A készlethez vagy feladathoz megadott csomagokat a rendszer letölti és kibontja egy elnevezett könyvtárba a `AZ_BATCH_ROOT_DIR` csomóponton belül. A Batch létrehoz egy környezeti változót is, amely tartalmazza a megnevezett könyvtár elérési útját. A feladat parancssorai ezt a környezeti változót használják, amikor az alkalmazásra hivatkoznak a csomóponton.

Windows-csomópontokon a változó formátuma a következő:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux-csomópontokon a formátum kissé eltérő. A rendszer a pontokat (.), a kötőjeleket (-) és a számjeleket (#) aláhúzásjelekre simítsa a környezeti változóban. Azt is vegye figyelembe, hogy az alkalmazásazonosító esete megmarad. Például:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` A és az olyan értékek, amelyek megfelelnek az üzembe helyezéshez megadott `version` alkalmazásnak és csomagverziónak. Ha például azt adja meg, hogy az alkalmazás *blender* 2.7-es verzióját Windows-csomópontokra kell telepíteni, a feladat parancssorai ezt a környezeti változót használják a fájlok eléréséhez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux-csomópontokon adja meg a környezeti változót ebben a formátumban. A pontokat (.), a kötőjeleket (-) és a számjeleket (#) aláhúzásjelekre simítsa, és őrizze meg az alkalmazásazonosító kis- és nagybetűit:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Alkalmazáscsomag feltöltésekor megadhat egy alapértelmezett verziót, amely a számítási csomópontokon lesz üzembe állítva. Ha egy alkalmazáshoz alapértelmezett verziót adott meg, kihagyhatja a verzió utótagját, amikor az alkalmazásra hivatkozik. Az alapértelmezett alkalmazásverziót a Azure Portal alkalmazások ablakában  adhatja meg, ahogyan az [alkalmazások feltöltése és kezelése oldalon látható.](#upload-and-manage-applications)

Ha például a "2.7" értéket adja meg az alkalmazás *blender* alapértelmezett verziójaként, és a tevékenységek a következő környezeti változóra hivatkoznak, akkor a Windows-csomópontok a 2.7-es verziót fogják végrehajtani:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Az alábbi kódrészlet egy példa a feladat parancssorára, amely elindítja a *blender* alkalmazás alapértelmezett verzióját:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> További információ a számítási csomópontok környezeti beállításairól: [Környezeti beállítások tevékenységekhez.](jobs-and-tasks.md#environment-settings-for-tasks)

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése

Ha egy meglévő készlet már konfigurálva van egy alkalmazáscsomaggal, megadhat egy új csomagot a készlethez. Ez a következőkkel jár:

- A Batch szolgáltatás telepíti az újonnan megadott csomagot az összes új csomópontra, amely csatlakozik a készlethez, illetve bármely meglévő csomópontra, amely újraindult vagy rendszerképét újraindította.
- A csomaghivatkozások frissítésekkor már a készletben lévő számítási csomópontok nem telepítik automatikusan az új alkalmazáscsomagot. Az új csomag fogadása előtt ezeket a számítási csomópontokat újra kell indítani, vagy újra kell rendszerképet indítani.
- Új csomag telepítésekor a létrehozott környezeti változók az új alkalmazáscsomag-hivatkozásokat tükrözik.

Ebben a példában a meglévő készletben a *blender* alkalmazás 2.7-es verziója van konfigurálva a [CloudPool.ApplicationPackageReferences egyikeként.](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) A készlet csomópontjainak 2.76b-os verzióval való frissítéséhez adjon meg egy új [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) értéket az új verzióval, és véglegesolja a változást.

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

Most, hogy konfigurálta az új verziót, a Batch szolgáltatás a 2.76b-os verziót telepíti a készlethez csatlakozó új csomópontra. A 2.76b telepítéséhez a már a készletben lévő csomópontokon indítsa újra a készletet, vagy indítsa újra a rendszerüket. Vegye figyelembe, hogy az újraindított csomópontok megőrzik a korábbi csomagtelepítések fájljait.

## <a name="list-the-applications-in-a-batch-account"></a>Batch-fiókban található alkalmazások felsorolása

Az alkalmazásokat és azok csomagjait az [ApplicationOperations.ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) metódussal listolhatja egy Batch-fiókban.

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

- A [Batch REST API](/rest/api/batchservice) is támogatást nyújt az alkalmazáscsomagokkal való munkához. Például tekintse meg az [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) elemet a telepíteni kívánt csomagok megadásához, az [Alkalmazások](/rest/api/batchservice/application) elemet pedig az alkalmazásinformációk beszerzéséhez.
- Megtudhatja, hogyan kezelheti programozott módon a Azure Batch fiókokat és [kvótákat a Batch Management .NET segítségével.](batch-management-dotnet.md) A [Batch Management .NET-kódtár](/dotnet/api/overview/azure/batch/management) lehetővé teszi a fiókok létrehozását és törlését a Batch-alkalmazás vagy -szolgáltatás számára.
