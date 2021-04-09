---
title: Felügyelt rendszerkép használata egyéni rendszerkép-készlet létrehozásához
description: Hozzon létre egy batch egyéni rendszerkép-készletet egy felügyelt rendszerképből, hogy kiépítse a számítási csomópontokat az alkalmazás szoftverével és adataival.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9baa65c0f1c1844ea10e3d5b4f0b48924912d233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023877"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Felügyelt rendszerkép használata egyéni rendszerkép-készlet létrehozásához

Ha egyéni képkészletet szeretne létrehozni a Batch-készlet virtuális gépei (VM-EK) számára, felügyelt rendszerkép használatával létrehozhat egy megosztott képkatalógus- [rendszerképet](batch-sig-images.md). A csak a felügyelt rendszerképek használata támogatott, de csak az API-verziók esetében, amelyek akár a 2019-08-01-es verzióra is kiterjednek.

> [!IMPORTANT]
> A legtöbb esetben egyéni lemezképeket kell létrehoznia a megosztott képkatalógus használatával. A megosztott képtárat használva gyorsabban kiépítheti a készleteket, méretezheti nagyobb mennyiségű virtuális gépet, és megbízhatóbb megbízhatóságot biztosít a virtuális gépek kiépítésekor. További információ: [a megosztott rendszerkép-katalógus használata egyéni készlet létrehozásához](batch-sig-images.md).

Ez a témakör azt ismerteti, hogyan hozhat létre egyéni képkészletet csak felügyelt rendszerkép használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Felügyelt rendszerkép erőforrása**. Ha egyéni rendszerkép használatával kívánja létrehozni a virtuális gépek készletét, akkor a Batch-fiókkal azonos Azure-előfizetésben és régióban kell létrehoznia egy felügyelt rendszerkép-erőforrást, vagy létre kell hoznia azt. A lemezképet létre kell hozni a virtuális gép operációsrendszer-lemezének pillanatképei és opcionálisan csatlakoztatott adatlemezei között.
  - Használjon egyedi egyéni rendszerképet a létrehozott készletekhez.
  - Ha a Batch API-k használatával szeretne készletet létrehozni a képpel, akkor az űrlaphoz tartozó rendszerkép **erőforrás-azonosítóját** kell megadnia `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - A felügyelt rendszerkép-erőforrásnak léteznie kell a készlet élettartamához a felskálázás engedélyezéséhez, és a készlet törlése után eltávolítható.

- **Azure Active Directory (Azure ad) hitelesítés**. A Batch-ügyfél API-nak az Azure AD-hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört.

## <a name="prepare-a-managed-image"></a>Felügyelt rendszerkép előkészítése

Az Azure-ban felügyelt rendszerképeket készíthet a következő helyekről:

- Azure-beli virtuális gép operációs rendszerének és adatlemezének pillanatképei
- Általános Azure-beli virtuális gép felügyelt lemezekkel
- A felhőbe feltöltött, általánosított helyszíni VHD

A Batch-készletek felügyelt képpel való megbízható méretezéséhez javasoljuk, hogy *csak* az első módszer használatával hozzon létre egy felügyelt képet: a virtuális gép lemezei Pillanatképek használatával. A következő lépések bemutatják, hogyan készíthet elő egy virtuális gépet, pillanatfelvételt készíthet, és hogyan hozhat létre felügyelt rendszerképet a pillanatképből.

### <a name="prepare-a-vm"></a>Virtuális gép előkészítése

Ha új virtuális gépet hoz létre a rendszerképhez, használja a Batch által támogatott első féltől származó Azure Marketplace-rendszerképet a felügyelt rendszerkép alaprendszerképének megfelelően. Alaprendszerképként csak az első féltől származó képek használhatók. Az Azure Batch által támogatott Azure Marketplace-rendszerkép-referenciák teljes listájának megjelenítéséhez tekintse meg a [csomópont-ügynök SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -azonosítóinak listázása műveletet.

> [!NOTE]
> Alaprendszerképként nem használhat olyan külső gyártótól származó rendszerképet, amely további licenccel és vásárlási feltételekkel rendelkezik. További információ ezekről a Piactéri lemezképekről: [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) vagy [Windows rendszerű](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties)virtuális gépek útmutatója.

- Győződjön meg arról, hogy a virtuális gép felügyelt lemezzel lett létrehozva. A virtuális gép létrehozásakor ez az alapértelmezett tárolási beállítás.
- Ne telepítse az Azure-bővítményeket, például az egyéni szkriptek bővítményét a virtuális gépen. Ha a lemezkép előre telepített bővítményt tartalmaz, az Azure problémákba ütközhet a Batch-készlet telepítésekor.
- Csatolt adatlemezek használatakor a lemezeket csatlakoztatnia kell a virtuális gépről, és formázni kell őket a használatuk során.
- Győződjön meg arról, hogy az Ön által megadott alap operációsrendszer-rendszerkép az alapértelmezett ideiglenes meghajtót használja. A Batch-csomópont ügynöke jelenleg az alapértelmezett ideiglenes meghajtót várja.
- Győződjön meg arról, hogy az operációsrendszer-lemez nincs titkosítva.
- Ha a virtuális gép fut, csatlakozzon hozzá RDP-kapcsolaton keresztül (Windows rendszeren) vagy SSH-n keresztül (Linux rendszeren). Telepítse a szükséges szoftvereket, vagy másolja a kívánt fájlokat.  

### <a name="create-a-vm-snapshot"></a>Virtuális gép pillanatképének létrehozása

A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. A virtuális gép operációs rendszerének vagy adatlemezének pillanatképének létrehozásához használhatja a Azure Portal vagy a parancssori eszközöket. A pillanatképek létrehozásához szükséges lépéseket és beállításokat a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) vagy a [Windows rendszerű](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuális gépek útmutatója tartalmazza.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Rendszerkép létrehozása egy vagy több pillanatképből

Felügyelt rendszerkép pillanatképből való létrehozásához használja az Azure parancssori eszközeit, például az az [rendszerkép Create](/cli/azure/image) parancsot. Rendszerkép létrehozásához az operációsrendszer-lemez pillanatképének megadásával és opcionálisan egy vagy több adatlemez-pillanatképet kell megadnia.

## <a name="create-a-pool-from-a-managed-image"></a>Készlet létrehozása felügyelt rendszerképből

Miután megtalálta a felügyelt rendszerkép erőforrás-AZONOSÍTÓját, hozzon létre egy egyéni rendszerkép-készletet a rendszerképből. A következő lépések bemutatják, hogyan hozhat létre egyéni képkészletet a Batch szolgáltatással vagy a kötegelt felügyelettel.

> [!NOTE]
> Győződjön meg arról, hogy az Azure AD-hitelesítéshez használt identitás rendelkezik a rendszerkép erőforrásához szükséges engedélyekkel. Lásd: [a Batch szolgáltatással kapcsolatos megoldások hitelesítése Active Directory](batch-aad-auth.md)használatával.
>
> A készlet élettartamához a felügyelt rendszerkép erőforrásának léteznie kell. Ha a mögöttes erőforrás törölve lett, a készlet nem méretezhető.

### <a name="batch-service-net-sdk"></a>Batch szolgáltatás .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>A Batch Management REST API

REST API URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Kérelem törzse

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>A nagyméretű készletek szempontjai

Ha több száz virtuális géppel vagy más egyéni rendszerkép használatával szeretne létrehozni egy készletet, fontos, hogy kövesse az előző útmutatást a virtuálisgép-pillanatképből létrehozott rendszerképek használatához.

Vegye figyelembe az alábbi szempontokat is:

- **Méretkorlát** – a Batch korlátozza a készlet méretét 2500 dedikált számítási csomópontokra, vagy 1000 alacsony prioritású csomópontokra, amikor egyéni rendszerképet használ.

  Ha ugyanazt a lemezképet (vagy ugyanazon mögöttes pillanatkép alapján több lemezképet) használja több készlet létrehozásához, a készletekben lévő összes számítási csomópont nem lépheti túl az előző korlátozásokat. Nem javasoljuk, hogy egy rendszerképet vagy annak mögöttes pillanatképét egyetlen készletnél ne használja.

  A határértékek csökkenhetnek, ha a készletet [bejövő NAT-készletekkel](pool-endpoint-configuration.md)konfigurálja.

- **Átméretezés időtúllépése** – ha a készlet rögzített számú csomópontot tartalmaz (nem automatikus méretezés), növelje a készlet resizeTimeout tulajdonságát egy értékre (például 20-30 percre). Ha a készlet nem éri el a célként megadott méretet az időtúllépési időszakon belül, hajtson végre egy másik [átméretezési műveletet](/rest/api/batchservice/pool/resize).

  Ha több mint 300 számítási csomóponttal rendelkező készletet tervez, előfordulhat, hogy a készlet méretét többször át kell méreteznie a cél méretének eléréséhez.
  
A [megosztott képtárat](batch-sig-images.md)használva nagyobb készleteket hozhat létre a testreszabott rendszerképekkel együtt, és több megosztott lemezkép-replikát is használhat. A megosztott rendszerképek használatával a készlethez szükséges idő akár 25%-kal gyorsabb, a virtuális gép üresjárati késése pedig akár 30%-kal rövidebb is lehet.

## <a name="considerations-for-using-packer"></a>A csomagoló használatának szempontjai

Felügyelt rendszerkép-erőforrás közvetlenül a Csomagolóval való létrehozása csak a felhasználói előfizetés módú batch-fiókokkal végezhető el. A Batch Service Mode-fiókok esetében először létre kell hoznia egy virtuális merevlemezt, majd importálnia kell a virtuális merevlemezt egy felügyelt rendszerkép-erőforrásba. A készlet lefoglalási módjától (felhasználói előfizetés vagy batch szolgáltatás) függően a felügyelt rendszerkép-erőforrások létrehozásának lépései eltérőek lesznek.

Győződjön meg arról, hogy a felügyelt rendszerkép létrehozásához használt erőforrás létezik az egyéni rendszerképre hivatkozó bármely készlet élettartama tekintetében. Ennek elmulasztása esetén a készlet lefoglalási hibáit és/vagy az átméretezési hibákat okozhatják.

Ha a rendszerkép vagy a mögöttes erőforrás el lett távolítva, a következőhöz hasonló hibaüzenetet kaphat: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Ha ezt a hibaüzenetet kapja, győződjön meg arról, hogy a mögöttes erőforrás nem lett eltávolítva.

További információ a virtuális gép létrehozásáról a csomagoló használatával: Linux- [rendszerkép létrehozása a csomagolóval](../virtual-machines/linux/build-image-with-packer.md) vagy Windows- [rendszerkép készítése a csomagolóval](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre egyéni készletet a [megosztott rendszerkép](batch-sig-images.md) -katalógus használatával.
- A Batch részletes áttekintését lásd: [Batch szolgáltatás munkafolyamata és erőforrásai](batch-service-workflow-features.md).
