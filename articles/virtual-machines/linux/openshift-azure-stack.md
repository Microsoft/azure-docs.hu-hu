---
title: OpenShift üzembe helyezése Azure Stack
description: OpenShift üzembe helyezése Azure Stackban.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667222"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>OpenShift-tároló platform vagy OKD üzembe helyezése Azure Stack

A OpenShift Azure Stack-ben is üzembe helyezhetők. Az Azure és a Azure Stack között fontos különbségek vannak, így a központi telepítés némileg eltérő lesz, és a képességek is némileg eltérőek.

Az Azure Cloud Provider jelenleg nem működik Azure Stackban. Ezért nem fogja tudni használni a lemez csatlakoztatását a Azure Stack állandó tárolásához. Ehelyett más tárolási beállításokat is konfigurálhat, például NFS-t, iSCSI-t, GlusterFS stb. Alternatív megoldásként engedélyezheti a CNS használatát, és használhatja a GlusterFS-t az állandó tároláshoz. Ha a CNS engedélyezve van, három további csomópont lesz telepítve a GlusterFS-használat további tárhelyével.

A OpenShift-tároló platformjának vagy OKD a következő számos módszer egyikével telepítheti Azure Stackban:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, majd követheti a [OpenShift-tároló platformjának dokumentációját](https://docs.openshift.com/container-platform) vagy a [OKD dokumentációját](https://docs.okd.io).
- Használhat egy meglévő [Resource Manager-sablont](https://github.com/Microsoft/openshift-container-platform/) is, amely leegyszerűsíti a OpenShift-tároló platform fürt üzembe helyezését.
- Használhat egy meglévő [Resource Manager-sablont](https://github.com/Microsoft/openshift-origin) is, amely leegyszerűsíti a OKD-fürt üzembe helyezését.

Ha a Resource Manager-sablont használja, válassza ki a megfelelő ágat (azurestack-Release-3. x). Az Azure-sablonok nem működnek, mivel az API-verziók eltérnek az Azure és a Azure Stack között. A RHEL-rendszerkép hivatkozása jelenleg változóként van kódolva a fájlban lévő azuredeploy.jsban, és módosítani kell a rendszerképnek megfelelően.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Az összes beállításhoz szükség van egy Red Hat-előfizetésre. Az üzembe helyezés során a Red Hat Enterprise Linux példány regisztrálva van a Red Hat-előfizetésben, és ahhoz a készlet-AZONOSÍTÓhoz van csatolva, amely tartalmazza a jogosultságokat a OpenShift-tároló platformhoz.
Győződjön meg arról, hogy rendelkezik érvényes Red Hat Subscription Manager-(RHSM-) felhasználónévvel, jelszóval és készlet-AZONOSÍTÓval. Alternatív megoldásként használhatja az aktiválási kulcsot, a szervezeti azonosítót és a készlet AZONOSÍTÓját is.  Ezen információk ellenőrzéséhez jelentkezzen be a alkalmazásba https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Előfeltételek Azure Stack

Egy OpenShift-fürt üzembe helyezéséhez fel kell venni egy RHEL-lemezképet (OpenShift-tároló platform) vagy CentOS-lemezképet (OKD) a Azure Stack-környezetbe. A rendszerképek hozzáadásához forduljon a Azure Stack rendszergazdájához. Az utasítások itt találhatók:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Üzembe helyezés a OpenShift Container platform vagy a OKD Resource Manager-sablon használatával

A Resource Manager-sablonnal történő üzembe helyezéshez egy Parameters-fájl segítségével adja meg a bemeneti paramétereket. Az üzembe helyezés további testreszabásához a GitHub-tárházat kell megváltoztatnia, és módosítani a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Megerősített VM-méret (változó azuredeploy.json)
- Elnevezési konvenciók (változók a azuredeploy.json)
- OpenShift-fürtök, a Hosts fájlon keresztül módosítva (deployOpenShift.sh)
- RHEL képhivatkozása (változó azuredeploy.json)

Az Azure CLI használatával történő üzembe helyezés lépéseinek végrehajtásához kövesse a [OpenShift Container platform](./openshift-container-platform-3x.md) szakasz vagy a [OKD](./openshift-okd.md) szakasz megfelelő szakaszát.

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [A OpenShift üzembe helyezésének hibája az Azure-ban](./openshift-container-platform-3x-troubleshooting.md)