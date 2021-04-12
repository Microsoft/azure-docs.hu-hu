---
title: Adatkezelő létrehozása
description: Hozzon létre egy Azure arc-adatkezelőt egy tipikus, több csomópontos Kubernetes-fürtön, amelyet már telepített.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 329df78bb5829695b95fcca5b7ed7e1439ced821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688365"
---
# <a name="create-the-azure-arc-data-controller"></a>Az Azure arc-adatkezelő létrehozása

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Az Azure-beli ív-adatkezelő létrehozásának áttekintése

Az Azure arc-kompatibilis adatszolgáltatások több különböző típusú Kubernetes-fürtön és felügyelt Kubernetes-szolgáltatásokon is létrehozhatók, több különböző módszer használatával.

Jelenleg a Kubernetes-szolgáltatások és-disztribúciók támogatott listája a következő:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service Engine (ak motor) Azure Stack
- Azure Kubernetes szolgáltatás Azure Stack HCI-on
- Azure RedHat OpenShift (ARO)
- OpenShift-tároló platformja (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Nyílt forráskódú, általában a kubeadm használatával üzembe helyezett fölérendelt Kubernetes

> [!IMPORTANT]
> * A Kubernetes minimális támogatott verziója v 1.17. További információ: [ismert problémák](./release-notes.md#known-issues) . 
> * A OCP minimális támogatott verziója 4,5.
> * A [kapcsolati követelményekből](connectivity.md) megtudhatja, milyen kapcsolatra van szükség a környezet és az Azure között.
> * Tekintse meg a [tárolási konfigurációs útmutatót](storage-configuration.md) , amelyből megtudhatja, hogyan konfigurálhatja az állandó tárterületet.
> * Ha az Azure Kubernetes szolgáltatást használja, a fürt feldolgozói csomópontjának virtuálisgép-méretének legalább **Standard_D8s_v3nak** kell lennie, és **prémium szintű lemezeket** kell használnia. A fürt nem terjedhet több rendelkezésre állási zónára. 
> * Ha más Kubernetes-eloszlást vagy-szolgáltatást használ, győződjön meg arról, hogy a csomópont minimális mérete 8 GB RAM és 4 mag, valamint az összes Kubernetes-csomóponton elérhető teljes 32 GB RAM-kapacitás. Előfordulhat például, hogy 1 csomóponttal rendelkezik 32 GB RAM-mal és 4 maggal, vagy 2 csomóponttal rendelkezik, amelyekhez 16GB RAM és 4 mag tartozik.

> [!NOTE]
> Ha a Red Hat OpenShift Container platformot használja az Azure-ban, a legújabb elérhető verziót ajánlott használni.

A választott lehetőségtől függően bizonyos eszközökre lesz _szükség_, de ajánlott az [összes ügyféleszközök telepítése](./install-client-tools.md) , mielőtt megkezdené az Azure arc-adatkezelő létrehozását.

A létrehozási folyamat során a választott lehetőségtől függetlenül a következő információkat kell megadnia:

- **Adatkezelő neve** – az adatvezérlő leíró neve – például "éles adatkezelő", "Seattle adatkezelő".
- **Adatkezelő felhasználóneve** – az adatkezelő rendszergazda felhasználójának felhasználóneve.
- **Adatvezérlő jelszava** – az adatkezelő rendszergazda felhasználójának jelszava.
- A **Kubernetes-névtér neve** – annak a Kubernetes-névtérnek a neve, amelyben az adatvezérlőt létre kívánja hozni.
- **Kapcsolati mód** – a kapcsolati mód határozza meg az Azure arc-kompatibilis adatszolgáltatási környezet és az Azure közötti kapcsolat mértékét. Az előzetes verzió jelenleg csak a közvetve csatlakoztatott és a közvetlenül csatlakoztatott üzemmódokat támogatja.  További információ: [kapcsolati mód](./connectivity.md). 
- **Azure-előfizetés azonosítója** – az Azure-előfizetés GUID azonosítója, ahol az Azure-beli adatkezelő erőforrást létre kívánja hozni.
- **Azure-erőforráscsoport neve** – annak az erőforráscsoportnak a neve, amelyhez az Azure-beli adatkezelő erőforrást létre kívánja hozni.
- **Azure Location** – az Azure-beli hely, ahol az adatkezelő erőforrás-metaadatai az Azure-ban lesznek tárolva. Az elérhető régiók listájáért lásd: [Azure globális infrastruktúra/termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Következő lépések

Több lehetőség is létezik az Azure arc-adatkezelő létrehozásához:

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.
> 
- [Adatvezérlő létrehozása a szolgáltatással [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Adatvezérlő létrehozása Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Adatkezelő létrehozása a Azure Portal Jupyter notebookon keresztül Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Adatvezérlő létrehozása Kubernetes eszközökkel, például kubectl vagy oC](create-data-controller-using-kubernetes-native-tools.md)
- [Adatvezérlő létrehozása Azure arc Jumpstart-vel a tesztelési célú üzembe helyezés gyorsított élményéhez](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
