---
title: Feladatok futtatása felhasználói fiókok alatt
description: Megismeri a felhasználói fiókok típusait és konfigurálásuk mikéntját.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 02cad0bff9e76ec5db82c417f2439b12ef088045
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389281"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Feladatok futtatása felhasználói fiókok alatt a Batchben

> [!NOTE]
> A cikkben tárgyalt felhasználói fiókok biztonsági okokból különböznek a RDP protokoll (RDP) vagy a Secure Shell (SSH) felhasználói fiókjaitól.
>
> A Linux rendszerű virtuális gépek konfigurációját SSH-n keresztül futtató csomóponthoz való csatlakozáshoz lásd: [Install and configure xrdp to use Távoli asztal with Ubuntu](../virtual-machines/linux/use-remote-desktop.md)(Az xrdp telepítése és konfigurálása az Ubuntu Távoli asztal használatával). Ha RDP-n keresztül szeretne csatlakozni a Windowst futtató csomópontokhoz, tekintse meg a Windows rendszerű Azure-beli virtuális gépekhez való csatlakozást és [bejelentkezést.](../virtual-machines/windows/connect-logon.md)
>
> A felhőszolgáltatás konfigurációját RDP-n keresztül futtató csomóponthoz való csatlakozáshoz lásd: [Enable Távoli asztali kapcsolat for a Role in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

A feladat Azure Batch mindig felhasználói fiók alatt fut. Alapértelmezés szerint a feladatok normál felhasználói fiókokban, rendszergazdai engedélyek nélkül futnak. Bizonyos esetekben előfordulhat, hogy azt a felhasználói fiókot szeretné konfigurálni, amelyben futtatni szeretné a feladatot. Ez a cikk a felhasználói fiókok típusait és azok forgatókönyvhöz való konfigurálását ismerteti.

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

Azure Batch kétféle felhasználói fiókot biztosít a feladatok futtatásához:

- **Automatikus felhasználói fiókok.** Az automatikus felhasználói fiókok beépített felhasználói fiókok, amelyek automatikusan létrejönnek a Batch szolgáltatásban. Alapértelmezés szerint a tevékenységek automatikusan felhasználói fiókkal futnak. Egy feladat automatikus felhasználóspecifikációját konfigurálhatja úgy, hogy jelezze, melyik automatikus felhasználói fiók alatt fusson a feladat. Az automatikus felhasználóspecifikációval megadhatja a tevékenységet futtatni kívánt automatikus felhasználói fiók jogosultságszint-emelési szintjét és hatókörét.

- **Egy elnevezett felhasználói fiók.** A készlet létrehozásakor megadhat egy vagy több elnevezett felhasználói fiókot a készlethez. Minden felhasználói fiók a készlet minden csomópontján létrejön. A fiók neve mellett meg kell adnia a felhasználói fiók jelszavát, a jogosultságszintet, linuxos készleteknél pedig a titkos SSH-kulcsot. Feladat hozzáadásakor megadhatja azt a megnevezett felhasználói fiókot, amely alatt a feladat futni fog.

> [!IMPORTANT]
> A Batch szolgáltatás 2017-01-01.4.0-s verziója egy olyan friss változást vezetett be, amely miatt frissítenie kell a kódot a verzió vagy újabb hívásához. A [Batch-kód](#update-your-code-to-the-latest-batch-client-library) régebbi verzióról való frissítésével kapcsolatos gyors útmutatásért lásd: A kód frissítése a legújabb Batch ügyféloldali kódtárra.

## <a name="user-account-access-to-files-and-directories"></a>Felhasználói fiók hozzáférése fájlokhoz és könyvtárakhoz

Mind az automatikus felhasználói fiók, mind a megnevezett felhasználói fiók olvasási/írási hozzáféréssel rendelkezik a tevékenység munkakönyvtárához, megosztott könyvtárához és többpéldányos feladatok könyvtárához. Mindkét fióktípus olvasási hozzáféréssel rendelkezik az indítási és a feladat-előkészítési könyvtárhoz.

Ha egy tevékenység ugyanabban a fiókban fut, mint az indítási tevékenység futtatása, a tevékenység olvasási és írási hozzáféréssel rendelkezik az indítási tevékenység könyvtárához. Hasonlóképpen, ha egy tevékenység ugyanabban a fiókban fut, mint a feladat-előkészítési tevékenység futtatása, a tevékenység olvasási és írási hozzáféréssel rendelkezik a feladat-előkészítési tevékenység könyvtárához. Ha egy tevékenység az indítási tevékenységtől vagy a feladat-előkészítési tevékenységtől eltérő fiókban fut, akkor a tevékenység csak olvasási hozzáféréssel rendelkezik a megfelelő címtárhoz.

A fájlok és könyvtárak feladatból való elérésével kapcsolatos további információkért lásd: [Fájlok és könyvtárak.](files-and-directories.md)

## <a name="elevated-access-for-tasks"></a>Emelt szintű hozzáférés a feladatokhoz

A felhasználói fiók jogosultságszintje jelzi, hogy egy tevékenység emelt szintű hozzáféréssel fut-e. Az automatikus és a megnevezett felhasználói fiók emelt szintű hozzáféréssel is futtatható. A jogosultságszint-emelés két lehetőség közül választhat:

- **NonAdmin ::** A feladat normál felhasználóként, emelt szintű hozzáférés nélkül fut. A Batch felhasználói fiókok alapértelmezett jogosultságszintje mindig **NonAdmin.**
- **Rendszergazda:** A feladat emelt szintű hozzáféréssel rendelkező felhasználóként fut, és teljes körű rendszergazdai engedélyekkel működik.

## <a name="auto-user-accounts"></a>Automatikus felhasználói fiókok

Alapértelmezés szerint a tevékenységek automatikus felhasználói fiókkal, emelt szintű hozzáférés nélküli általános jogú felhasználóként és készlethatókörrel futnak a Batchben. A készlet hatóköre azt jelenti, hogy a feladat egy automatikus felhasználói fiók alatt fut, amely a készlet bármely tevékenysége számára elérhető. A készlet hatókörével kapcsolatos további információkért lásd: Feladat futtatása automatikus [felhasználóként készlethatókörrel.](#run-a-task-as-an-auto-user-with-pool-scope)

A készlet hatókörének alternatívája a tevékenységhatókör. Ha az automatikus felhasználóspecifikáció konfigurálva van a tevékenység hatókörében, a Batch szolgáltatás csak az adott feladathoz hoz létre automatikus felhasználói fiókot.

Az automatikus felhasználó specifikációjának négy lehetséges konfigurációja van, amelyek mind egy egyedi automatikus felhasználói fióknak felelnek meg:

- Nem rendszergazdai hozzáférés feladathatókörrel
- Rendszergazdai (emelt szintű) hozzáférés feladathatókörrel
- Nem rendszergazdai hozzáférés készlethatókörrel
- Rendszergazdai hozzáférés készlethatókörrel

> [!IMPORTANT]
> A tevékenységhatókörben futó tevékenységek valójában nem férnek hozzá a csomópont más feladataihoz. A fiókhoz hozzáféréssel rendelkező rosszindulatú felhasználók azonban a korlátozást úgy is elérhetik, hogy beküldik a rendszergazdai jogosultságokkal futó feladatokat, és más feladat-könyvtárakhoz férnek hozzá. Egy rosszindulatú felhasználó RDP vagy SSH használatával is csatlakozhat egy csomóponthoz. Az ilyen helyzetek megelőzése érdekében fontos a Batch-fiókkulcsok hozzáférésének védelme. Ha azt gyanítja, hogy a fiókját feltörték, mindenképpen újragenerálja a kulcsokat.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Feladat futtatása emelt szintű hozzáféréssel automatikusan felhasználóként

A rendszergazdai jogosultságok automatikus felhasználói specifikációját akkor konfigurálhatja, ha emelt szintű hozzáféréssel rendelkező feladatot kell futtatnia. Előfordulhat például, hogy egy indítási tevékenységnek emelt szintű hozzáférésre van szüksége a szoftverek telepítéséhez a csomóponton.

> [!NOTE]
> Csak szükség esetén használjon emelt szintű hozzáférést. Az ajánlott eljárások azt javasolják, hogy adja meg a kívánt eredmény eléréséhez szükséges minimális jogosultságot. Ha például egy indítási tevékenység szoftvert telepít az aktuális felhasználó számára az összes felhasználó helyett, előfordulhat, hogy nem kell emelt szintű hozzáférést ad a feladatokhoz. Konfigurálhatja a készlet hatókörének automatikus felhasználóspecifikációját és a nem rendszergazdai hozzáférést minden olyan feladathoz, amely ugyanazon a fiókon kell futnia, beleértve az indítási feladatot is.

Az alábbi kódrészletek mutatják be, hogyan konfigurálható az automatikus felhasználó specifikációja. A példák a jogosultságszint-emelési szintet, a `Admin` hatókört pedig a következőre állítják: `Task` .

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```

#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Feladat futtatása automatikus felhasználóként készlethatókörrel

Egy csomópont kiépítésekor két készletre kiterjedő automatikus felhasználói fiók jön létre a készlet minden csomópontján, egy emelt szintű hozzáféréssel, egy pedig emelt szintű hozzáférés nélkül. Ha az automatikus felhasználó hatókörét egy adott tevékenység készlethatóköreként be van adva, a rendszer a két készletre kiterjedő automatikus felhasználói fiók egyikében futtatja a feladatot.

Ha megadja az automatikus felhasználó készlethatókörét, a rendszergazdai hozzáféréssel futó összes feladat ugyanazon készletre kiterjedő automatikus felhasználói fiók alatt fut. Hasonlóképpen, a rendszergazdai engedélyek nélkül futó feladatok is egy készletre kiterjedő automatikus felhasználói fiókban futnak.

> [!NOTE]
> A két készletre kiterjedő automatikus felhasználói fiók külön fiók. A készletre kiterjedő rendszergazdai fiók alatt futó feladatok nem oszthatnak meg adatokat a standard fiókban futó feladatokkal, és fordítva.

Az azonos automatikus felhasználói fiókkal való futtatás előnye, hogy a tevékenységek képesek adatokat megosztani az ugyanazon a csomóponton futó más feladatokkal.

A titkos kulcsok feladatok közötti megosztása az egyik olyan forgatókönyv, amelyben hasznos, ha a tevékenységeket a két készletre kiterjedő automatikus felhasználói fiók egyikében futtatja. Tegyük fel például, hogy egy indítási tevékenységnek ki kell kiépítenie egy titkos secret-et a csomóponton, amit más tevékenységek használhatnak. Használhatja a Windows Data Protection API-t (DPAPI), de ehhez rendszergazdai jogosultság szükséges. Ehelyett felhasználói szinten is megvédheti a titkos titkos 80 000 000 000 000 000 00 Az azonos felhasználói fiókkal futó feladatok emelt szintű hozzáférés nélkül férhetnek hozzá a titkos eléréshez.

Egy másik forgatókönyv, amikor egy készlethatókörű automatikus felhasználói fiókban szeretne feladatokat futtatni, egy Message Passing Interface (MPI) fájlmegosztás. Az MPI-fájlmegosztás akkor hasznos, ha az MPI-feladat csomópontjainak ugyanazon fájladatokon kell dolgozniuk. Az főcsomópont létrehoz egy fájlmegosztást, amelyhez a gyermekcsomópontok hozzáférhetnek, ha ugyanazon automatikus felhasználói fiókkal futnak.

Az alábbi kódrészlet az automatikus felhasználó hatókörét a Batch .NET-hez tartozó tevékenységek készlethatóköreként állítja be. A szintszint nincs megadva, így a feladat a standard készletszintű automatikus felhasználói fiók alatt fut.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Elnevezett felhasználói fiókok

A készlet létrehozásakor elnevezett felhasználói fiókokat is definiálhat. Egy elnevezett felhasználói fiók rendelkezik egy Ön által megadott névvel és jelszóval. Megadhatja egy elnevezett felhasználói fiók jogosultságszint-emelési szintjét. Linux-csomópontok számára SSH titkos kulcsot is meg lehet adni.

A készlet összes csomópontja rendelkezik elnevezett felhasználói fiókkal, amely az adott csomópontokon futó összes tevékenység számára elérhető. A készlethez bármilyen számú elnevezett felhasználót definiálhat. Feladat- vagy feladatgyűjtemény hozzáadásakor megadhatja, hogy a feladat a készletben meghatározott elnevezett felhasználói fiókok egyikével fut.

Egy elnevezett felhasználói fiók akkor hasznos, ha egy feladat összes tevékenységét ugyanazon a felhasználói fiókon szeretné futtatni, de el szeretné különíteni azokat a más feladatokban egyidejűleg futó tevékenységektől. Létrehozhat például egy elnevezett felhasználót minden feladathoz, és futtathatja az egyes feladatok feladatait a megnevezett felhasználói fiók alatt. Ezután minden feladat megoszthatja a titkos okat a saját tevékenységével, de más feladatokban futó tevékenységekkel nem.

Elnevezett felhasználói fiók használatával olyan feladatokat is futtathat, amelyek külső erőforrásokra, például fájlmegosztásra vonatkozó engedélyeket állít be. Elnevezett felhasználói fiókkal Ön szabályozhatja a felhasználói identitást, és használhatja ezt a felhasználói identitást az engedélyek beállításához.  

A megnevezett felhasználói fiókok jelszóval nem rendelkező SSH-t engedélyeznek a Linux-csomópontok között. Elnevezett felhasználói fiókot használhat olyan Linux-csomópontokkal, amelyek többpéldányos feladatokat kell futtatnia. A készlet minden csomópontja a teljes készletben meghatározott felhasználói fiók alatt futtathat feladatokat. További információ a többpéldányos feladatokról: [Többpéldányos feladatok \- használata MPI-alkalmazások futtatásához.](batch-mpi.md)

### <a name="create-named-user-accounts"></a>Elnevezett felhasználói fiókok létrehozása

Ha elnevezett felhasználói fiókokat szeretne létrehozni a Batchben, adjon hozzá felhasználói fiókok gyűjteményét a készlethez. Az alábbi kódrészletek azt mutatják be, hogyan hozhatók létre elnevezett felhasználói fiókok a .NET-, Java- és Python-környezetben. Ezek a kódrészletek azt mutatják be, hogyan hozhatók létre rendszergazdai és nem rendszergazdai nevű fiókok egy készleten. A példák a felhőszolgáltatás konfigurációjának használatával hoznak létre készleteket, de ugyanez a módszer használható Windows- vagy Linux-készlet virtuálisgép-konfigurációval történő létrehozásakor is.

#### <a name="batch-net-example-windows"></a>Batch .NET-példa (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
    nodeAgentSkuId: "batch.node.windows amd64");

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET-példa (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "Standard_A1",
    virtualMachineConfiguration: virtualMachineConfiguration);
// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-java-example"></a>Batch Java-példa

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withVirtualMachineConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch – Python-példa

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Feladat futtatása emelt szintű hozzáféréssel elnevezett felhasználói fiók alatt

Egy feladat emelt szintű felhasználóként való futtatásához állítsa a tevékenység **UserIdentity** tulajdonságát egy elnevezett felhasználói fiókra, amely az **ElevationLevel** tulajdonságának beállításával lett létrehozva. `Admin`

Ez a kódrészlet azt határozza meg, hogy a feladatnak egy elnevezett felhasználói fiók alatt kell futnia. Ez a megnevezett felhasználói fiók a készlet létrehozásakor lett definiálva a készletben. Ebben az esetben a megnevezett felhasználói fiók rendszergazdai engedélyekkel lett létrehozva:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>A kód frissítése a legújabb Batch-ügyfélkódtárra

A Batch szolgáltatás 2017-01-01.4.0-s verziója feltörést vezetett be, amely a korábbi verziókban elérhető **runElevated** tulajdonságot a **userIdentity** tulajdonságra cserélte. Az alábbi táblázatok egyszerű leképezést biztosítanak, amely segítségével frissítheti a kódot az ügyfélkódtárak korábbi verzióiból.

### <a name="batch-net"></a>Batch .NET

| Ha a kód...                  | Frissítse a...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` nincs megadva | Nincs szükség frissítésre                                                                                               |

### <a name="batch-java"></a>Batch Java

| Ha a kód...                      | Frissítse a...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` nincs megadva | Nincs szükség frissítésre                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Ha a kód...                      | Frissítse a...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` nincs megadva | Nincs szükség frissítésre                                                                                                                                  |

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a Batch szolgáltatás munkafolyamatát és [az olyan elsődleges erőforrásokat,](batch-service-workflow-features.md) mint a készletek, csomópontok, feladatok és tevékenységek.
- További információ [a fájlokról](files-and-directories.md) és könyvtárakról a Azure Batch.
