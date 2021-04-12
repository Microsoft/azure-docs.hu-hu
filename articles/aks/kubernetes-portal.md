---
title: Kubernetes-erőforrások elérése a Azure Portal
description: Ismerje meg, hogyan kezelheti a Kubernetes-erőforrásokkal az Azure Kubernetes szolgáltatás (ak) fürtjét a Azure Portal.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578680"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Kubernetes-erőforrások elérése a Azure Portal

A Azure Portal tartalmaz egy Kubernetes-erőforrás nézetet, amely megkönnyíti az Azure Kubernetes szolgáltatás (ak) fürtjében található Kubernetes-erőforrások elérését. A Kubernetes-erőforrások megtekintése a Azure Portal csökkenti a Azure Portal és a parancssori eszköz közötti helyi váltást `kubectl` , így egyszerűsítve a Kubernetes-erőforrások megtekintésének és szerkesztésének élményét. Az erőforrás-megjelenítő jelenleg több erőforrástípust tartalmaz, például központi telepítéseket, hüvelyeket és replikákat.

A Azure Portal Kubernetes-erőforrás nézete lecseréli az [AK-irányítópult bővítményt][kubernetes-dashboard], amely elavult.

## <a name="prerequisites"></a>Előfeltételek

A Azure Portal Kubernetes-erőforrásainak megtekintéséhez AK-fürtre van szükség. Bármely fürt támogatott, de ha Azure Active Directory (Azure AD) integrációt használ, a fürtnek az [AK által felügyelt Azure ad-integrációt][aks-managed-aad]kell használnia. Ha a fürt örökölt Azure AD-t használ, a fürtöt a portálon vagy az [Azure CLI][cli-aad-upgrade]-vel is frissítheti. [Az Azure Portal használatával][portal-cluster] új AK-fürtöt is létrehozhat.

## <a name="view-kubernetes-resources"></a>Kubernetes-erőforrások megtekintése

A Kubernetes-erőforrások megtekintéséhez navigáljon az AK-fürthöz a Azure Portal. A bal oldali navigációs ablaktábla az erőforrások elérésére szolgál. Az erőforrások a következők:

- A **névterek** a fürt névtereit jelenítik meg. A névtér lista tetején található szűrő gyors módszert kínál a névtér erőforrásainak szűrésére és megjelenítésére.
- A **munkaterhelések** a fürtön üzembe helyezett központi telepítések, hüvelyek, replikakészlet, állapot-nyilvántartó készletek, démon-készletek, feladatok és cron-feladatok adatait jelenítik meg. Az alábbi képernyőképen egy példa AK-fürt alapértelmezett rendszerhüvelyei láthatók.
- A **szolgáltatások és a ingresses** megjeleníti a fürt összes szolgáltatását és a bejövő erőforrásokat.
- A **Storage** az Azure Storage-osztályait és az állandó mennyiségi adatokat mutatja.
- A **konfiguráció** a fürt konfigurációs térképeit és a titkos kulcsokat jeleníti meg.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="A Azure Portalban megjelenő Kubernetes-Pod-információ." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Ebben a példában a minta AK-fürtöt használjuk az Azure vote-alkalmazás üzembe helyezéséhez az [AK][portal-quickstart]gyors útmutatójában.

1. Válassza a **Hozzáadás** tetszőleges erőforrás-nézetből (névtér, munkaterhelések, szolgáltatások és Ingresses, tárterület vagy konfiguráció) lehetőséget.
1. Illessze be az Azure vote-alkalmazás YAML az [AK][portal-quickstart]rövid útmutatójában.
1. Válassza a **Hozzáadás** lehetőséget a YAML-szerkesztő alján az alkalmazás telepítéséhez. 

A YAML-fájl hozzáadása után az erőforrás-megjelenítő mindkét létrehozott Kubernetes-szolgáltatást megjeleníti: a belső szolgáltatást (Azure-vote-back) és a külső szolgáltatást (Azure-vote-oldal) az Azure vote alkalmazás eléréséhez. A külső szolgáltatás egy csatolt külső IP-címet tartalmaz, így egyszerűen megtekintheti az alkalmazást a böngészőben.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Az Azure vote alkalmazás információi megjelennek a Azure Portalban." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Üzembe helyezési áttekintések figyelése

A [tárolók számára Azure monitor][enable-monitor] rendelkező AK-fürtök gyorsan megtekinthetik az üzembe helyezést és az egyéb ismereteket. A Kubernetes-erőforrások nézetben a felhasználók láthatják az egyes központi telepítések élő állapotát, beleértve a processzor-és memóriahasználat, valamint az Azure monitorra való áttérést az adott csomópontokkal és tárolókkal kapcsolatos részletesebb információkhoz. Íme egy példa egy minta AK-fürt üzembe helyezésére:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="A Azure Portalban megjelenő üzembe helyezési eredmények." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML szerkesztése

A Kubernetes erőforrás nézet egy YAML-szerkesztőt is tartalmaz. A beépített YAML-szerkesztő azt jelenti, hogy a portálon belül frissítheti vagy létrehozhatja a szolgáltatásokat és a központi telepítéseket, és azonnal alkalmazhatja a módosításokat.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="A Azure Portalban megjelenő Kubernetes-szolgáltatás YAML-szerkesztője.":::

A YAML szerkesztése után a módosításokat a **felülvizsgálat + mentés** lehetőség kiválasztásával, a módosítások megerősítésével, majd újbóli mentésével alkalmazza a rendszer.

>[!WARNING]
> A közvetlen üzemi változások felhasználói felületen vagy CLI-n keresztüli végrehajtása nem ajánlott, a [folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) ajánlott eljárásainak](kubernetes-action.md)használata. Az Azure Portal Kubernetes-felügyeleti képességei és a YAML-szerkesztő az új üzemelő példányok fejlesztéséhez és teszteléséhez készült.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz a gyakori problémákkal és a hibaelhárítási lépésekkel foglalkozik.

### <a name="unauthorized-access"></a>Jogosulatlan hozzáférés

A Kubernetes erőforrásainak eléréséhez hozzáféréssel kell rendelkeznie az AK-fürthöz, a Kubernetes API-hoz és a Kubernetes-objektumokhoz. Győződjön meg arról, hogy Ön vagy egy Fürtfelügyelő vagy egy olyan felhasználó, aki rendelkezik a megfelelő engedélyekkel az AK-fürt eléréséhez. A fürt biztonságával kapcsolatos további információkért lásd: [hozzáférési és identitási beállítások az AK][concepts-identity]-hoz.

>[!NOTE]
> Az Azure Portal kubernetes-erőforrás nézetét csak a [felügyelt HRE-kompatibilis fürtök](managed-aad.md) vagy nem HRE-kompatibilis fürtök támogatják. Ha felügyelt HRE engedélyezett fürtöt használ, a HRE-felhasználónak vagy-identitásnak rendelkeznie kell a megfelelő szerepkörökkel/szerepkör-kötésekkel a kubernetes API eléréséhez, a [felhasználó `kubeconfig` ](control-kubeconfig-access.md)lekérésére vonatkozó engedély mellett.

### <a name="enable-resource-view"></a>Erőforrás nézet engedélyezése

A meglévő fürtök esetében előfordulhat, hogy engedélyeznie kell a Kubernetes erőforrás-nézetet. Az erőforrás nézet engedélyezéséhez kövesse a fürtben található portálon megjelenő utasításokat.

> [!TIP]
> Az [**API-kiszolgáló által engedélyezett IP-tartományokhoz**](api-server-authorized-ip-ranges.md) tartozó AK funkció hozzáadható az API-kiszolgáló hozzáférésének korlátozásához csak a tűzfal nyilvános végpontja számára. Egy másik lehetőség, hogy az ilyen fürtök frissítése a `--api-server-authorized-ip-ranges` helyi ügyfélszámítógép vagy az IP-címtartomány (amelyről a portál böngészése) hozzáférését is tartalmazza. A hozzáférés engedélyezéséhez a számítógép nyilvános IPv4-címe szükséges. Ezt a címet megkeresheti az alábbi paranccsal, vagy a "mi az IP-cím" kifejezéssel az Internet böngészőben.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan érheti el a Kubernetes-erőforrásokat az AK-fürthöz. A YAML és a Kubernetes erőforrás-megjelenítővel elért YAML-fájlok mélyebb megismeréséhez tekintse meg a [központi telepítések és a jegyzékfájlok][deployments] című témakört.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md