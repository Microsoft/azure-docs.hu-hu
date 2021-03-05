---
title: Egyéni rendszerkép-készlet létrehozása a megosztott rendszerkép-katalógus használatával
description: Az egyéni képkészletek hatékony módszer a számítási csomópontok konfigurálására a Batch-munkaterhelések futtatásához.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 8623c47952540717ae50538fd7b0282c9c8629bb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124244"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Egyéni rendszerkép-készlet létrehozása a megosztott rendszerkép-katalógus használatával

Ha Azure Batch-készletet hoz létre a virtuális gép konfigurációjával, meg kell adnia egy virtuálisgép-rendszerképet, amely a készlet minden számítási csomópontja számára biztosítja az operációs rendszert. Létrehozhat egy virtuális gépek készletét vagy egy támogatott Azure Marketplace-lemezképpel, vagy létrehozhat egy egyéni rendszerképet egy [megosztott képtárat](../virtual-machines/shared-image-galleries.md)tartalmazó képpel.

## <a name="benefits-of-the-shared-image-gallery"></a>A megosztott képgyűjtemény előnyei

Ha az egyéni rendszerképhez a megosztott képtárat használja, az operációs rendszer típusát és konfigurációját, valamint az adatlemezek típusát is szabályozhatja. A megosztott rendszerkép tartalmazhat olyan alkalmazásokat és referenciákat, amelyek a Batch-készlet összes csomópontján elérhetővé válnak a kiépítés után.

A környezethez szükség szerint a rendszerkép több verzióját is használhatja. Ha rendszerkép-verziót használ a virtuális gép létrehozásához, a rendszer a rendszerkép verzióját használja a virtuális gép új lemezének létrehozásához.

A megosztott rendszerkép használatával időt takaríthat meg a készlet számítási csomópontjainak előkészítése a Batch-munkaterhelések futtatásához. A kiépítés után egy Azure Marketplace-rendszerkép is használható, és minden számítási csomóponton telepítheti a szoftvereket, de a megosztott rendszerképek használata általában hatékonyabb. Emellett több replikát is megadhat a megosztott rendszerképhez, így ha sok virtuális géppel (több mint 600 virtuális géppel) rendelkező készleteket hoz létre, akkor időt takaríthat meg a készlet létrehozásakor.

A forgatókönyvhöz konfigurált megosztott rendszerkép használata több előnyt is biztosíthat:

- **Ugyanazokat a lemezképeket használja a régiók között.** Létrehozhat megosztott rendszerkép-replikákat különböző régiók között, így az összes készlet ugyanazt a rendszerképet használja.
- **Konfigurálja az operációs rendszert (operációs rendszer).** Testreszabhatja a lemezkép operációsrendszer-lemezének konfigurációját.
- **Alkalmazások telepítése előtt.** Az operációsrendszer-lemezen lévő alkalmazások előzetes telepítése hatékonyabb és kevésbé hasonló, mint az alkalmazások telepítése a számítási csomópontok indítási tevékenységgel való kiépítés után.
- **Nagyméretű adatmennyiségek másolása egyszer.** A felügyelt megosztott rendszerkép statikus adatokból álló részét a felügyelt rendszerkép adatlemezére másolja. Ezt csak egyszer kell elvégezni, és a készlet minden csomópontja számára elérhetővé kell tenni az adatmennyiséget.
- **Növelje a készleteket nagyobb méretekre.** A megosztott képkatalógussal nagyobb készleteket hozhat létre a testreszabott rendszerképekkel együtt, és több megosztott lemezkép-replikát is használhat.
- **Jobb teljesítmény, mint a csak a felügyelt rendszerképek egyéni rendszerképként való használata.** Egy megosztott rendszerkép egyéni rendszerkép-készlete esetében az állandó állapot eléréséhez szükséges idő akár 25%-kal gyorsabb, a virtuális gép üresjárati késése pedig akár 30%-kal rövidebb.
- **A képek verziószámozása és csoportosítása az egyszerűbb felügyelet érdekében.** A képcsoportosítási definíció információt tartalmaz arról, hogy miért jött létre a rendszerkép, milyen operációs rendszerre és a rendszerképek használatára vonatkozó információkkal rendelkezik. A képek csoportosítása megkönnyíti a képek kezelését. További információ: [képdefiníciók](../virtual-machines/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Az Azure AD használatával kell hitelesítenie magát. Ha megosztott kulcsos hitelesítést használ, akkor hitelesítési hibaüzenetet kap.  

- **Egy Azure Batch-fiók.** Batch-fiók létrehozásához tekintse meg a Batch rövid útmutatóit a [Azure Portal](quick-create-portal.md) vagy az [Azure CLI](quick-create-cli.md)használatával.

- **Megosztott Képgyűjteményi rendszerkép** Megosztott rendszerkép létrehozásához felügyelt rendszerkép-erőforrást kell létrehoznia, vagy létre kell hoznia. A lemezképet létre kell hozni a virtuális gép operációsrendszer-lemezének pillanatképei és opcionálisan csatlakoztatott adatlemezei között.

> [!NOTE]
> Ha a megosztott rendszerkép nem ugyanabban az előfizetésben van, mint a Batch-fiók, [regisztrálnia kell a Microsoft.BatCH erőforrás-szolgáltatót](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) az előfizetéshez. A két előfizetésnek ugyanabban az Azure AD-bérlőben kell lennie.
>
> A rendszerkép egy másik régióban is lehet, amíg a Batch-fiókkal megegyező régióban található replikák vannak.

Ha egy Azure AD-alkalmazással hoz létre egyéni képtárat egy megosztott képkatalógus-lemezképpel, az alkalmazásnak rendelkeznie kell egy [Azure beépített szerepkörrel](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) , amely hozzáférést biztosít a megosztott rendszerképhez. Ezt a hozzáférést a Azure Portal a megosztott rendszerképre való navigálással, a **hozzáférés-vezérlés (iam)** lehetőség kiválasztásával, valamint az alkalmazáshoz tartozó szerepkör-hozzárendelés hozzáadásával adhatja meg.

## <a name="prepare-a-shared-image"></a>Megosztott rendszerkép előkészítése

Az Azure-ban létrehozhat egy megosztott rendszerképet egy felügyelt rendszerképből, amely a következő helyekről hozható létre:

- Azure-beli virtuális gép operációs rendszerének és adatlemezének pillanatképei
- Általános Azure-beli virtuális gép felügyelt lemezekkel
- A felhőbe feltöltött, általánosított helyszíni VHD

> [!NOTE]
> A Batch csak általánosított megosztott rendszerképeket támogat; egy speciális megosztott rendszerkép nem használható készlet létrehozásához.

A következő lépések bemutatják, hogyan készíthet elő egy virtuális gépet, pillanatfelvételt készíthet, és létrehozhat egy rendszerképet a pillanatképből.

### <a name="prepare-a-vm"></a>Virtuális gép előkészítése

Ha új virtuális gépet hoz létre a rendszerképhez, használja a Batch által támogatott első féltől származó Azure Marketplace-rendszerképet a felügyelt rendszerkép alaprendszerképének megfelelően. Alaprendszerképként csak az első féltől származó képek használhatók. Az Azure Batch által támogatott Azure Marketplace-rendszerkép-referenciák teljes listájának megjelenítéséhez tekintse meg a [csomópont-ügynök SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -azonosítóinak listázása műveletet.

> [!NOTE]
> Alaprendszerképként nem használhat olyan külső gyártótól származó rendszerképet, amely további licenccel és vásárlási feltételekkel rendelkezik. További információ ezekről a Piactéri lemezképekről: [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) vagy [Windows rendszerű](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) virtuális gépek útmutatója.

Virtuális gépek létrehozásakor kövesse az alábbi irányelveket:

- Győződjön meg arról, hogy a virtuális gép felügyelt lemezzel lett létrehozva. A virtuális gép létrehozásakor ez az alapértelmezett tárolási beállítás.
- Ne telepítse az Azure-bővítményeket, például az egyéni szkriptek bővítményét a virtuális gépen. Ha a lemezkép előre telepített bővítményt tartalmaz, az Azure problémákba ütközhet a Batch-készlet telepítésekor.
- Csatolt adatlemezek használatakor a lemezeket csatlakoztatnia kell a virtuális gépről, és formázni kell őket a használatuk során.
- Győződjön meg arról, hogy az Ön által megadott alap operációsrendszer-rendszerkép az alapértelmezett ideiglenes meghajtót használja. A Batch-csomópont ügynöke jelenleg az alapértelmezett ideiglenes meghajtót várja.
- Győződjön meg arról, hogy az operációsrendszer-lemez nincs titkosítva.
- Ha a virtuális gép fut, csatlakozzon hozzá RDP-kapcsolaton keresztül (Windows rendszeren) vagy SSH-n keresztül (Linux rendszeren). Telepítse a szükséges szoftvereket, vagy másolja a kívánt fájlokat.
- A készlet gyorsabb üzembe helyezéséhez használja a virtuális gép operációsrendszer-lemezének [READWRITE-gyorsítótár-beállítását](../virtual-machines/premium-storage-performance.md#disk-caching) .

### <a name="create-a-vm-snapshot"></a>Virtuális gép pillanatképének létrehozása

A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. A virtuális gép operációs rendszerének vagy adatlemezének pillanatképének létrehozásához használhatja a Azure Portal vagy a parancssori eszközöket. A pillanatképek létrehozásához szükséges lépéseket és beállításokat a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) vagy a [Windows rendszerű](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuális gépek útmutatója tartalmazza.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Rendszerkép létrehozása egy vagy több pillanatképből

Felügyelt rendszerkép pillanatképből való létrehozásához használja az Azure parancssori eszközeit, például az az [rendszerkép Create](/cli/azure/image) parancsot. Hozzon létre egy lemezképet egy operációsrendszer-lemez pillanatképének megadásával és opcionálisan egy vagy több adatlemez-pillanatképtel.

### <a name="create-a-shared-image-gallery"></a>Shared Image Gallery létrehozása

Miután sikeresen létrehozta a felügyelt rendszerképet, létre kell hoznia egy megosztott képtárat, hogy elérhetővé tegye az egyéni rendszerképet. Ha meg szeretné tudni, hogyan hozhat létre megosztott képtárat a rendszerképekhez, tekintse meg a megosztott lemezkép-katalógus [létrehozása az Azure CLI-vel](../virtual-machines/shared-images-cli.md) vagy [a közös rendszerkép-katalógus létrehozása a Azure Portal használatával](../virtual-machines/linux/shared-images-portal.md)című témakört.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Készlet létrehozása megosztott rendszerképből az Azure CLI használatával

Ha az Azure CLI használatával szeretne létrehozni egy készletet a megosztott rendszerképből, használja az `az batch pool create` parancsot. Határozza meg a megosztott rendszerkép AZONOSÍTÓját a `--image` mezőben. Győződjön meg arról, hogy az operációs rendszer típusa és az SKU megfelel a következő által megadott verzióknak: `--node-agent-sku-id`

> [!NOTE]
> Az Azure AD használatával kell hitelesítenie magát. Ha megosztott kulcsos hitelesítést használ, akkor hitelesítési hibaüzenetet kap.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Készlet létrehozása megosztott rendszerképből C használatával #

Azt is megteheti, hogy létrehoz egy készletet egy megosztott rendszerképből a C# SDK használatával.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Készlet létrehozása megosztott rendszerképből a Python használatával

A Python SDK használatával egy megosztott rendszerképből is létrehozhat készletet: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Készlet létrehozása megosztott rendszerképből a Azure Portal használatával

A következő lépésekkel hozhat létre készletet egy megosztott rendszerképből a Azure Portal.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg a **Batch-fiókokat** , és válassza ki a fiókját.
1. Válassza a **készletek** lehetőséget, majd a **Hozzáadás** gombra kattintva hozzon létre egy új készletet.
1. A **rendszerkép típusa** szakaszban válassza a **megosztott képgyűjtemény** lehetőséget.
1. Hajtsa végre a többi szakaszt a felügyelt lemezképpel kapcsolatos információkkal.
1. Válassza az **OK** lehetőséget.

![Hozzon létre egy készletet egy megosztott rendszerképből a portálon.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>A nagyméretű készletek szempontjai

Ha egy megosztott rendszerkép használatával több száz vagy több ezer virtuális géppel rendelkező készletet szeretne létrehozni, kövesse az alábbi útmutatást.

- **A megosztott képgyűjtemény replikáinak száma.**  A legfeljebb 300 példánnyal rendelkező készletek esetében javasoljuk, hogy tartsa meg legalább egy replikát. Ha például egy 3000 virtuális géppel rendelkező készletet hoz létre, akkor legalább 10 replikát kell tartania a rendszerképből. Javasoljuk, hogy a jobb teljesítmény érdekében mindig több replikát őrizzen meg, mint a minimális követelmények.

- **Átméretezési időtúllépés.** Ha a készlet rögzített számú csomópontot tartalmaz (ha nem rendelkezik az autoskálázással), növelje a `resizeTimeout` készlet tulajdonságát a készlet méretétől függően. Minden 1000 virtuális gép esetében az ajánlott átméretezési időkorlát legalább 15 percet vesz igénybe. Például egy 2000 virtuális géppel rendelkező készlet ajánlott átméretezési időtúllépése legalább 30 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések

- A Batch részletes áttekintését lásd: [Batch szolgáltatás munkafolyamata és erőforrásai](batch-service-workflow-features.md).
- Ismerje meg a [megosztott képtárat](../virtual-machines/shared-image-galleries.md).