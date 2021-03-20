---
title: Linux futtatása virtuális gépek számítási csomópontjain
description: Ismerje meg, hogyan dolgozhat párhuzamos számítási feladatokkal a Linux rendszerű virtuális gépek készletei között Azure Batchban.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683700"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linuxos számítási csomópontok kiépítése a Batch-készletekben

A Azure Batch használatával párhuzamos számítási feladatokat futtathat Linux és Windows rendszerű virtuális gépeken is. Ez a cikk részletesen ismerteti, hogyan hozhat létre Linux számítási csomópontokat a Batch szolgáltatásban a [Batch Python](https://pypi.python.org/pypi/azure-batch) és a [Batch .net](/dotnet/api/microsoft.azure.batch) ügyféloldali kódtárak használatával. 

## <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja

Amikor számítási csomópontok készletét hozza létre a Batchben, két lehetőség közül választhat, amelyekből kiválaszthatja a csomópont méretét és operációs rendszerét: Cloud Services konfigurációt és a virtuális gép konfigurációját. A [virtuálisgép-konfigurációs](nodes-and-pools.md#virtual-machine-configuration) készletek olyan Azure-beli virtuális gépekből állnak, amelyek Linux-vagy Windows-lemezképből is létrehozhatók. Amikor virtuálisgép-konfigurációval rendelkező készletet hoz létre, meg kell adnia egy [rendelkezésre álló számítási csomópont méretét](../virtual-machines/sizes.md), a csomópontokra telepítendő virtuálisgép-rendszerképet, valamint a Batch-csomóponti ügynök SKU-t (az egyes csomópontokon futó programot, és a csomópont és a Batch szolgáltatás közötti felületet biztosít).

### <a name="virtual-machine-image-reference"></a>Virtuális gép képének referenciája

A Batch szolgáltatás [virtuálisgép-méretezési csoportokat](../virtual-machine-scale-sets/overview.md) használ a számítási csomópontok biztosításához a virtuális gép konfigurációjában. Megadhat egy rendszerképet az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1), vagy [használhatja a megosztott képkatalógust az Egyéni rendszerképek előkészítéséhez](batch-sig-images.md).

Amikor létrehoz egy virtuálisgép-rendszerkép-referenciát, meg kell adnia a következő tulajdonságokat:

| **Képhivatkozás tulajdonsága** | **Példa** |
| --- | --- |
| Publisher |Canonical |
| Ajánlat |UbuntuServer |
| Termékváltozat |18,04 – LTS |
| Verzió |legújabb |

> [!TIP]
> További információt ezekről a tulajdonságokról és a Marketplace-lemezképek megadásáról az [Azure Marketplace-en, az Azure parancssori](../virtual-machines/linux/cli-ps-findimage.md)felületén található linuxos virtuálisgép-rendszerképeket ismertető témakörben találhat. Vegye figyelembe, hogy egyes Piactéri lemezképek jelenleg nem kompatibilisek a Batch szolgáltatással.

### <a name="list-of-virtual-machine-images"></a>Virtuálisgép-lemezképek listája

Nem minden Piactéri rendszerkép kompatibilis a jelenleg elérhető batch Node-ügynökökkel. A Batch szolgáltatáshoz és a hozzájuk tartozó csomópont-ügynökhöz tartozó összes támogatott Piactéri virtuálisgép-rendszerkép listázásához használja a [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), a [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .net) vagy a megfelelő API-t egy másik nyelvi SDK-ban.

### <a name="node-agent-sku"></a>Csomópont-ügynök SKU

A [Batch-csomóponti ügynök](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) egy olyan program, amely a készlet minden egyes csomópontján fut, és a parancs-és vezérlési felületet biztosítja a csomópont és a Batch szolgáltatás között. A csomópont-ügynök különböző implementációkat mutat be különböző operációs rendszerekhez. Alapvetően a virtuálisgép-konfiguráció létrehozásakor először meg kell adnia a virtuális gép rendszerképének hivatkozását, majd meg kell adnia a rendszerképre telepítendő csomópont-ügynököt. Az egyes csomópontok ügynökei általában több virtuálisgép-lemezképtel kompatibilisek. Íme néhány példa a Node Agent-SKU-ra:

- Batch. Node. Ubuntu 18,04
- Batch. Node. CentOS 7
- Batch. Node. Windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Linux-készlet létrehozása: batch Python

A következő kódrészlet azt mutatja be, hogyan használható a [Python Microsoft Azure batch ügyféloldali kódtára](https://pypi.python.org/pypi/azure-batch) az Ubuntu Server számítási csomópontok készletének létrehozásához. A Batch Python-modullal kapcsolatos további részletekért tekintse meg a [dokumentációt](/python/api/overview/azure/batch).

Ez a kódrészlet explicit módon létrehoz egy [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) , és meghatározza a hozzá tartozó tulajdonságokat (közzétevő, ajánlat, SKU, verzió). Az éles kódban azonban azt javasoljuk, hogy a [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) metódus használatával válassza ki a rendelkezésre álló rendszerkép-és csomópont-ügynök SKU-kombinációkat futásidőben.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Ahogy korábban említettük, javasoljuk, hogy a [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) módszer használatával dinamikusan válassza ki a jelenleg támogatott csomópont-ügynök/piactér képkombinációkat (ahelyett, hogy explicit módon létrehoz egy [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) ). A következő Python-kódrészlet a módszer használatát mutatja be.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux-készlet létrehozása: Batch .NET

A következő kódrészlet azt mutatja be, hogyan használható a [Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) ügyféloldali kódtár az Ubuntu Server számítási csomópontok készletének létrehozásához. A Batch .NET-tel kapcsolatos további részletekért tekintse meg a [dokumentációt](/dotnet/api/microsoft.azure.batch).

A következő kódrészlet a [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) metódust használja a jelenleg támogatott Piactéri rendszerkép és a csomópont-ügynök SKU-kombinációinak listájából való kiválasztáshoz. Ez a módszer ajánlott, mert a támogatott kombinációk listája időről időre változhat. A rendszer leggyakrabban a támogatott kombinációkat adja hozzá.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Bár az előző kódrészlet a [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) metódussal dinamikusan listáz és választ a támogatott rendszerkép és csomópont-ügynök SKU-kombinációi alapján (ajánlott), a [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) explicit módon is konfigurálhatja:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Kapcsolódás Linux-csomópontokhoz SSH használatával

A fejlesztés során vagy a hibaelhárítás során előfordulhat, hogy be kell jelentkeznie a készletben lévő csomópontokra. A Windows számítási csomópontjaitól eltérően az RDP protokoll (RDP) nem használható Linux-csomópontokhoz való kapcsolódáshoz. Ehelyett a Batch szolgáltatás engedélyezi az SSH-hozzáférést az egyes csomópontokon a távoli kapcsolathoz.

A következő Python-kódrészlet létrehoz egy felhasználót a készlet minden egyes csomópontján, amely a távoli kapcsolatok esetében szükséges. Ezután kinyomtatja a Secure Shell-(SSH-) kapcsolatok adatait az egyes csomópontokhoz.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Ez a kód az alábbi példához hasonló kimenettel fog rendelkezni. Ebben az esetben a készlet négy Linux-csomópontot tartalmaz.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Jelszó helyett egy nyilvános SSH-kulcsot is megadhat, amikor felhasználót hoz létre egy csomóponton. A Python SDK-ban használja a **ssh_public_key** paramétert a [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). A .NET-ben használja a [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) tulajdonságot.

## <a name="pricing"></a>Díjszabás

Azure Batch az Azure Cloud Servicesra és az Azure Virtual Machines technológiára épül. Maga a Batch szolgáltatás díjmentesen érhető el, ami azt jelenti, hogy kizárólag a Batch-megoldások által felhasznált számítási erőforrások (és a hozzájuk kapcsolódó költségek) után kell fizetni. A **virtuális gép konfigurációjának** kiválasztásakor a [Virtual Machines díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/) struktúra alapján kell fizetnie.

Ha [alkalmazáscsomag](batch-application-packages.md)használatával helyezi üzembe az alkalmazásokat a Batch-csomópontokon, akkor az alkalmazáscsomag által felhasznált Azure Storage-erőforrásokért is fizetnie kell.

## <a name="next-steps"></a>Következő lépések

- Az [Azure-batch-Samples GitHub-adattárában](https://github.com/Azure/azure-batch-samples) megismerheti a [Python-kód mintáit](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) , amelyből megtudhatja, hogyan hajtható végre az általános batch-műveletek, például a készlet, a feladat és a feladat létrehozása. A Python-mintákhoz tartozó [readme](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) tartalmazza a szükséges csomagok telepítésének részleteit.
- Ismerje meg, hogyan használhatja az [alacsony prioritású virtuális gépeket](batch-low-pri-vms.md) a Batch használatával.
