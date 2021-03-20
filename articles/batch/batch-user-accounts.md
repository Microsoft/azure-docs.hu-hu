---
title: Feladatok futtatása a felhasználói fiókokban
description: Ismerje meg a felhasználói fiókok típusait és azok konfigurálásának módját.
ms.topic: how-to
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: cce374e7d7ffb513bed882b048ea54bcbad81b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719359"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Feladatok futtatása a Batch felhasználói fiókjai alatt

> [!NOTE]
> A cikkben tárgyalt felhasználói fiókok nem egyeznek meg a biztonsági okokból a RDP protokoll (RDP) vagy a Secure Shell (SSH) esetében használt felhasználói fiókokkal.
>
> Ha SSH-n keresztül szeretne csatlakozni egy linuxos virtuálisgép-konfigurációt futtató csomóponthoz, tekintse meg a Távoli asztal használata Linux rendszerű virtuális gépre [Az Azure-ban](../virtual-machines/linux/use-remote-desktop.md)című témakört. Ha RDP-n keresztül szeretne csatlakozni a Windows-csomópontokhoz, tekintse meg [a Kapcsolódás Windows Server rendszerű virtuális géphez](../virtual-machines/windows/connect-logon.md)című témakört.<br /><br />
> Ha a felhőalapú szolgáltatás konfigurációját RDP protokollon keresztül futtató csomóponthoz szeretne csatlakozni, tekintse meg a [Távoli asztali kapcsolat engedélyezése az Cloud Services Azure-beli szerepkörökhöz](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)című témakört.

Egy feladat Azure Batch mindig egy felhasználói fiók alatt fut. Alapértelmezés szerint a feladatok a normál felhasználói fiókok alatt, rendszergazdai jogosultságok nélkül futnak. Bizonyos esetekben előfordulhat, hogy konfigurálni szeretné azt a felhasználói fiókot, amelyben a feladatot futtatni kívánja. Ez a cikk a felhasználói fiókok típusait és a forgatókönyvhöz való konfigurálását ismerteti.

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

A Azure Batch két típusú felhasználói fiókot biztosít a feladatok futtatásához:

- **Automatikus felhasználói fiókok.** Az automatikus felhasználói fiókok beépített felhasználói fiókok, amelyeket a Batch szolgáltatás automatikusan hoz létre. Alapértelmezés szerint a tevékenységek egy automatikus felhasználói fiók alatt futnak. Megadhatja az automatikus felhasználó specifikációját, amely azt jelzi, hogy mely automatikus felhasználói fiókkal kell futtatni a feladatot. Az automatikus felhasználó specifikációja lehetővé teszi a feladat futtatására szolgáló automatikus felhasználói fiók jogosultságszint-emelési szintjének és hatókörének megadását.

- **Névvel ellátott felhasználói fiók.** A készlet létrehozásakor megadhat egy vagy több elnevezett felhasználói fiókot a készlethez. Minden felhasználói fiók létrejön a készlet minden egyes csomópontján. A fiók neve mellett adja meg a felhasználói fiók jelszavát, a jogosultságszint-emelési szintet, valamint a Linux-készletek esetében az SSH titkos kulcsot. Feladat hozzáadásakor megadhatja azt a elnevezett felhasználói fiókot, amely alatt a feladat futni fog.

> [!IMPORTANT]
> A Batch Service 2017 -01-01.4.0 verziója bevezet egy olyan megszakítási változást, amely megköveteli, hogy frissítse a kódot a verzió meghívásához. Ha a Batch egy régebbi verziójából telepíti át a kódot, vegye figyelembe, hogy a **runElevated** tulajdonság már nem támogatott a REST API vagy a Batch ügyféloldali kódtárakban. Jogosultságszint-emelési szint megadásához használja a feladat új **userIdentity** tulajdonságát. A Batch-kód frissítésével kapcsolatos gyors útmutatásért lásd: [a kód frissítése a legújabb batch-ügyféloldali függvénytárba](#update-your-code-to-the-latest-batch-client-library) .

## <a name="user-account-access-to-files-and-directories"></a>Felhasználói fiókhoz való hozzáférés a fájlokhoz és könyvtárakhoz

Az automatikus felhasználói fiók és a nevesített felhasználói fiók egyaránt rendelkezik írási/olvasási hozzáféréssel a tevékenység munkakönyvtárához, a megosztott könyvtárhoz és a többpéldányos feladatok könyvtárához. Mindkét típusú fiók olvasási hozzáféréssel rendelkezik az indítási és a feladatok előkészítési könyvtáraihoz.

Ha egy feladat ugyanabban a fiókban fut, amelyet az indítási tevékenység futtatásához használt, a feladat írási és olvasási hozzáféréssel rendelkezik a Start Task könyvtárhoz. Hasonlóképpen, ha egy feladat ugyanabban a fiókban fut, amelyet a feladat-előkészítési feladat futtatásához használt, a feladat írási és olvasási hozzáféréssel rendelkezik a feladat-előkészítési tevékenység könyvtárához. Ha egy feladat egy másik fiók alatt fut, mint a Start tevékenység vagy a feladat-előkészítési feladat, akkor a feladat csak olvasási hozzáféréssel rendelkezik a megfelelő címtárhoz.

A fájlok és könyvtárak egy feladatból való elérésével kapcsolatos további információkért tekintse meg a [fájlok és könyvtárak](files-and-directories.md)című témakört.

## <a name="elevated-access-for-tasks"></a>Emelt szintű hozzáférés a feladatokhoz

A felhasználói fiók jogosultságszint-emelési szintje azt jelzi, hogy egy feladat emelt szintű hozzáféréssel fut-e. Egy automatikus felhasználói fiók és egy nevesített felhasználói fiók is futtatható emelt szintű hozzáféréssel. A jogosultságszint-emelési szint két lehetősége a következő:

- Nem **rendszergazda:** A feladat emelt szintű hozzáférés nélküli általános jogú felhasználóként fut. Egy batch felhasználói fiók alapértelmezett jogosultságszint-emelési szintje mindig nem **rendszergazda**.
- **Rendszergazda:** A feladat emelt szintű hozzáféréssel rendelkező felhasználóként fut, és teljes körű rendszergazdai engedélyekkel működik.

## <a name="auto-user-accounts"></a>Automatikus felhasználói fiókok

Alapértelmezés szerint a tevékenységek a Batch szolgáltatásban egy automatikus felhasználói fiók alatt futnak, a normál felhasználó emelt szintű hozzáférés nélkül, a készlet hatókörével együtt. A készlet hatóköre azt jelenti, hogy a feladat egy automatikus felhasználói fiók alatt fut, amely a készlet bármely tevékenysége számára elérhető. A készlet hatókörével kapcsolatos további információkért lásd: [feladat futtatása automatikus felhasználóként készlet hatókörével](#run-a-task-as-an-auto-user-with-pool-scope).

A készlet hatókörének alternatívája a feladat hatóköre. Ha az automatikus felhasználó specifikációja feladat hatókörre van konfigurálva, a Batch szolgáltatás csak automatikusan felhasználói fiókot hoz létre ehhez a feladathoz.

Az automatikus felhasználó specifikációnak négy lehetséges konfigurációja van, amelyek mindegyike egy egyedi automatikus felhasználói fióknak felel meg:

- Nem rendszergazdai hozzáférés a feladat hatókörével
- Rendszergazdai (emelt szintű) hozzáférés a feladat hatókörével
- Nem rendszergazdai hozzáférés a készlet hatókörével
- Rendszergazdai hozzáférés a készlet hatókörével

> [!IMPORTANT]
> A feladat hatókörében futó feladatok nem rendelkeznek tényleges hozzáféréssel a csomóponton lévő egyéb feladatokhoz. Azonban egy olyan rosszindulatú felhasználó, aki hozzáféréssel rendelkezik a fiókhoz, megkerülheti ezt a korlátozást egy rendszergazdai jogosultságokkal rendelkező feladat elküldésével, és hozzáfér más feladatok könyvtáraihoz. Egy rosszindulatú felhasználó RDP vagy SSH használatával is csatlakozhat egy csomóponthoz. Fontos, hogy megvédje a Batch-fiók kulcsaihoz való hozzáférést egy ilyen forgatókönyv megelőzése érdekében. Ha azt gyanítja, hogy a fiókja sérült, akkor ne felejtse el újragenerálni a kulcsokat.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Feladat futtatása automatikus felhasználóként emelt szintű hozzáféréssel

Ha emelt szintű hozzáféréssel rendelkező feladatot szeretne futtatni, konfigurálhatja a rendszergazdai jogosultságok automatikus felhasználói specifikációját. Előfordulhat például, hogy egy indítási tevékenységhez emelt szintű hozzáférésre van szükség a szoftver telepítéséhez a csomóponton.

> [!NOTE]
> Emelt szintű hozzáférés használata csak akkor, ha szükséges. Az ajánlott eljárások a kívánt eredmény eléréséhez szükséges minimális jogosultság megadását ajánlják. Ha például egy indítási tevékenység az aktuális felhasználóhoz tartozó szoftvereket telepít, az összes felhasználó helyett előfordulhat, hogy el tudja kerülni az emelt szintű hozzáférés megadását a feladatokhoz. Konfigurálhatja a készlet hatókörének és a nem rendszergazdai hozzáférésnek az automatikus felhasználó-specifikációt minden olyan feladathoz, amelynek ugyanazon a fiókon kell futnia, beleértve az indítási tevékenységet is.

Az alábbi kódrészletek bemutatják, hogyan konfigurálhatja az automatikus felhasználó specifikációját. A példák a jogosultságszint-emelési szintet `Admin` és a hatókört határozzák meg `Task` .

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Feladat futtatása automatikus felhasználóként a készlet hatókörével

Csomópontok kiosztásakor a készlet minden egyes csomópontján két teljes készletre kiterjedő automatikus felhasználói fiók jön létre, egyet pedig emelt szintű hozzáféréssel, egyet pedig emelt szintű hozzáférés nélkül. Ha az automatikus felhasználó hatókörét egy adott feladathoz tartozó készlet hatókörére állítja be, akkor a feladat az alábbi két alkalmazáskészletre kiterjedő automatikus felhasználói fiókok valamelyikét futtatja.

Ha a készlet hatókörét adja meg az automatikus felhasználó számára, akkor a rendszergazdai hozzáféréssel futtatott összes feladat ugyanazon a teljes alkalmazáskészletre kiterjedő automatikus felhasználói fiók alatt fut. Hasonlóképpen, a rendszergazdai jogosultságok nélküli futtatású feladatok egyetlen, készletre kiterjedő automatikus felhasználói fiókkal is futnak.

> [!NOTE]
> A két teljes készletre kiterjedő automatikus felhasználói fiókok külön fiókok. A készletre kiterjedő rendszergazdai fiókban futó feladatok nem oszthatják meg az adatmegosztást a standard fiókban futó feladatokkal, és fordítva.

Az azonos automatikus felhasználói fiókkal való futtatás előnye, hogy a feladatok képesek megosztani az adatokkal az ugyanazon a csomóponton futó egyéb feladatokkal.

A feladatok közötti titkok megosztása egy olyan forgatókönyv, amelyben a két teljes készletre kiterjedő automatikus felhasználói fiókok valamelyike alatt a feladatok futtatása hasznos. Tegyük fel például, hogy egy indítási tevékenységnek egy titkos kulcsot kell kiépítenie a csomópontra, amelyet más feladatok használhatnak. Használhatja a Windows adatvédelmi API-t (DPAPI), de rendszergazdai jogosultságokat igényel. Ehelyett a titkos kulcsot a felhasználói szinten is védetté teheti. Az azonos felhasználói fiók alatt futó feladatok emelt szintű hozzáférés nélkül is hozzáférhetnek a titkos kulcshoz.

Egy másik forgatókönyv, ahol előfordulhat, hogy a készlet hatókörével rendelkező automatikus felhasználói fiókkal szeretne feladatokat futtatni, egy Message Passing Interface (MPI) fájlmegosztás. Az MPI-fájlmegosztás akkor hasznos, ha az MPI-feladat csomópontjainak ugyanazon a fájlban kell dolgozniuk. A fő csomópont olyan fájlmegosztást hoz létre, amelyet a gyermek csomópontok elérnek, ha ugyanazon az automatikus felhasználói fiókon futnak.

A következő kódrészlet az automatikus felhasználó hatókörét állítja be egy feladathoz a Batch .NET-ben. A jogosultságszint-emelési szint kimarad, így a feladat a szabványos, teljes készletre kiterjedő automatikus felhasználói fiók alatt fut.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Elnevezett felhasználói fiókok

Készlet létrehozásakor megadhatja az elnevezett felhasználói fiókokat. Egy névvel ellátott felhasználói fiók rendelkezik a megadott névvel és jelszóval. Megadhatja egy elnevezett felhasználói fiók jogosultságszint-emelési szintjét. Linux-csomópontok esetén a titkos SSH-kulcsot is megadhatja.

A készlet minden csomópontján létezik egy névvel ellátott felhasználói fiók, amely a csomópontokon futó összes tevékenység számára elérhető. Egy készlethez tetszőleges számú megnevezett felhasználót adhat meg. Ha felvesz egy feladatot vagy feladatsort, megadhatja, hogy a feladat a készletben meghatározott elnevezett felhasználói fiókok valamelyikében fusson.

Az elnevezett felhasználói fiókok akkor hasznosak, ha egy adott feladat összes feladatát ugyanazon felhasználói fiók alatt szeretné futtatni, de a többi feladatban futó feladatokból is elkülöníti azokat. Létrehozhat például egy névvel ellátott felhasználót az egyes feladatokhoz, és futtathatja az egyes feladatok feladatait az elnevezett felhasználói fiók alatt. Minden feladat ezután megoszthat egy titkos kulcsot a saját feladataival, de nem a más feladatokban futó tevékenységekkel.

Elnevezett felhasználói fiókot is használhat egy olyan feladat futtatásához, amely a külső erőforrásokra, például a fájlmegosztás engedélyeit állítja be. Egy névvel ellátott felhasználói fiókkal szabályozhatja a felhasználói identitást, és az adott felhasználói identitást használhatja az engedélyek beállításához.  

A nevesített felhasználói fiókok lehetővé teszik a jelszó nélküli SSH-t a Linux-csomópontok között. A többpéldányos feladatokat futtató Linux-csomópontokkal ellátott felhasználói fiókokat is használhat. A készlet minden csomópontja a teljes készletben definiált felhasználói fiókkal futtathat feladatokat. A többpéldányos feladatokkal kapcsolatos további információkért lásd: [ \- többpéldányos feladatok használata MPI-alkalmazások futtatásához](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Elnevezett felhasználói fiókok létrehozása

Elnevezett felhasználói fiókok létrehozásához a kötegben vegyen fel egy felhasználói fiókokat tartalmazó gyűjteményt a készletbe. A következő kódrészletek bemutatják, hogyan hozhat létre nevesített felhasználói fiókokat a .NET, a Java és a Python használatával. Ezek a kódrészletek bemutatják, hogyan hozhatók létre a rendszergazdák és a nem rendszergazda nevű fiókok a készleteken. A példák a Cloud Service-konfiguráció használatával hoznak létre készleteket, de a Windows-vagy Linux-készletek a virtuális gép konfigurációjával való létrehozásakor ugyanazt a megközelítést használják.

#### <a name="batch-net-example-windows"></a>Batch .NET-példa (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

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
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Példa a Batch Pythonra

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Feladat futtatása elnevezett felhasználói fiókkal emelt szintű hozzáféréssel

Ha emelt szintű felhasználóként szeretne futtatni egy feladatot, állítsa a feladat **UserIdentity** tulajdonságát egy elnevezett felhasználói fiókra, amelyet a **ElevationLevel** tulajdonsággal hoztak létre `Admin` .

Ez a kódrészlet azt adja meg, hogy a feladatnak egy elnevezett felhasználói fiók alatt kell futnia. Ez az elnevezett felhasználói fiók definiálva lett a készletben a készlet létrehozásakor. Ebben az esetben az elnevezett felhasználói fiók rendszergazdai engedélyekkel lett létrehozva:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>A kód frissítése a Batch-ügyfél legújabb könyvtárába

A Batch Service 2017 -01-01.4.0 bevezet egy megszakítási változást, és lecseréli a korábbi verziókban elérhető **runElevated** tulajdonságot a **userIdentity** tulajdonsággal. Az alábbi táblázatok egy egyszerű leképezést biztosítanak, amely segítségével frissítheti a kódot az ügyféloldali kódtárak korábbi verzióiból.

### <a name="batch-net"></a>Batch .NET

| Ha a kód használja...                  | Frissítés a következőre:....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` nincs megadva | Nincs szükség frissítésre                                                                                               |

### <a name="batch-java"></a>Batch Java

| Ha a kód használja...                      | Frissítés a következőre:....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` nincs megadva | Nincs szükség frissítésre                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Ha a kód használja...                      | Frissítés a következőre:....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, hol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, hol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` nincs megadva | Nincs szükség frissítésre                                                                                                                                  |

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Tudnivalók a Azure Batch található [fájlokról és könyvtárakról](files-and-directories.md) .
