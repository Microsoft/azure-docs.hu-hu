---
title: A hibrid infrastruktúra kezelése az Azure arc skálával
description: Ismerje meg, hogyan kezelheti hatékonyan az ügyfelek számítógépeit és a Kubernetes-fürtöket az Azure-on kívül.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: ec32389cd7444405580530a00c8b7c5bc48bcd56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419320"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>A hibrid infrastruktúra kezelése az Azure arc skálával

Szolgáltatóként több ügyfél-bérlőt is felkészített az [Azure világítótoronyba](../overview.md). Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több Azure Active Directory (Azure AD) bérlőre kiterjedő műveleteket hajtsanak végre, így hatékonyabbá téve a felügyeleti feladatokat.

Az [Azure arc](../../azure-arc/overview.md) megkönnyíti az összetett és elosztott környezetek helyszíni, Edge-és többfelhőbeli üzembe helyezését, és lehetővé teszi az Azure-szolgáltatások bárhonnan történő telepítését és az Azure-felügyelet bármely infrastruktúrára való kiterjesztését.

Az [Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/overview.md)esetében az ügyfelek az Azure-on kívül üzemeltetett Windows-és Linux-gépeket is kezelhetik a vállalati hálózaton, ugyanúgy, mint a natív Azure-beli virtuális gépeket. Ha egy hibrid gépet az Azure-hoz csatolunk, az csatlakoztatva lesz, és Azure-erőforrásként lesz kezelve. A szolgáltatók ezután kezelhetik ezeket a nem Azure-beli gépeket az ügyfelek Azure-erőforrásaival együtt.

Az [Azure arc-kompatibilis Kubernetes](../../azure-arc/kubernetes/overview.md) lehetővé teszi, hogy az ügyfelek az Azure-on belüli vagy kívül Kubernetes-fürtöket csatlakoztassanak és konfiguráljanak Ha egy Kubernetes-fürt csatlakozik az Azure arc-hoz, akkor az a Azure Portal fog megjelenni, egy Azure Resource Manager AZONOSÍTÓval és egy felügyelt identitással. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoport alatt találhatók, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást.

Ez a témakör áttekintést nyújt arról, hogy a szolgáltatók hogyan használhatják az Azure arc-kompatibilis kiszolgálókat és az Azure arc-kompatibilis Kubernetes méretezhető módon az ügyfelek hibrid környezetének kezeléséhez az összes felügyelt ügyfél-bérlőn.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, ez az útmutató az [Azure Lighthouse-t használó vállalatoknak is vonatkozik több bérlő kezelésére](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Hibrid kiszolgálók kezelése nagy léptékű Azure arc-kompatibilis kiszolgálókkal

Szolgáltatóként az Azure-on kívüli helyszíni Windows Server-vagy Linux-gépeket is kezelhet, amelyekkel az ügyfelek az [Azure Connected Machine Agent](../../azure-arc/servers/agent-overview.md)használatával csatlakoztak az előfizetéséhez.

Ha a Azure Portalben lévő delegált előfizetéshez tartozó erőforrásokat tekinti meg, akkor ezek az **Azure arc** nevű csatlakoztatott gépeket fogják látni. Ezeket a csatlakoztatott gépeket az Azure-konstrukciók, például a Azure Policy és a címkézés használatával kezelheti, ugyanúgy, mint az ügyfél Azure-erőforrásainak kezeléséhez. A kapcsolódó hibrid gépeket együtt is kezelheti az ügyfél-bérlők között.

Megadhatja például, hogy [ugyanazok a házirendek legyenek alkalmazva az ügyfelek hibrid gépei között](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). A Azure Security Center használatával figyelheti az összes ügyfél hibrid környezetének megfelelőségét, vagy a [Azure monitor használatával adatokat gyűjthet közvetlenül a hibrid gépekről](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) egy log Analytics munkaterületre. A [virtuálisgép-bővítmények](../../azure-arc/servers/manage-vm-extensions.md) üzembe helyezhetők a nem Azure-beli Windows-és Linux-alapú virtuális gépeken, leegyszerűsítve az ügyfél hibrid gépei felügyeletét.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Hibrid Kubernetes-fürtök kezelése az Azure arc-kompatibilis Kubernetes

Olyan Kubernetes-fürtöket kezelhet, amelyek az [Azure arc használatával csatlakoztak az ügyfél előfizetéséhez](../../azure-arc/kubernetes/connect-cluster.md), ugyanúgy, mintha az Azure-ban futnak.

Ha az ügyfél létrehozta a [Kubernetes-fürtöket az Azure arc szolgáltatásba](../../azure-arc/kubernetes/create-onboarding-service-principal.md), akkor ehhez a egyszerű szolgáltatásnév-fiókhoz férhet hozzá, és kezelheti a fürtöket. Ezt olyan felhasználók is elvégezhetik, akik a "Kubernetes-fürt – Azure arc bevezetése" Azure beépített szerepkört kaptak, ha a szolgáltatásnevet tartalmazó előfizetést az [Azure Lighthouse-be állították be](onboard-customer.md).

A GitOps használatával konfigurálhatja a [konfigurációkat](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) és a [Helm-diagramokat](../../azure-arc/kubernetes/use-gitops-with-helm.md) a csatlakoztatott fürtökhöz.

A csatlakoztatott fürtöket Azure Monitor is figyelheti, és a [Azure Policy használatával méretezhetővé teheti a fürt konfigurációját](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a Jumpstart és a mintákat az [Azure arc GitHub-adattárában](https://github.com/microsoft/azure_arc).
- Ismerje meg [Az Azure arc-kompatibilis kiszolgálók támogatott forgatókönyveit](../../azure-arc/servers/overview.md#supported-scenarios).
- Ismerje meg az [Azure arc által támogatott Kubernetes-disztribúciókat](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Megtudhatja, hogyan [helyezhet üzembe egy házirendet a skálán](policy-at-scale.md).
- Megtudhatja, hogyan [használhatja a Azure monitor-naplókat a skálán](monitor-at-scale.md).
