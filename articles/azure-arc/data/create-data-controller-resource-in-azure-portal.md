---
title: Azure-beli ív-adatkezelő létrehozása a Azure Portalban
description: Azure-beli ív-adatkezelő létrehozása a Azure Portalban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 9c928040aa2ff5a6ebfb7102c03450d3d7297b59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686478"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Azure-beli ív-adatkezelő létrehozása a Azure Portalban

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Bevezetés

Az Azure Portal használatával létrehozhat egy Azure arc-adatkezelőt.

Az Azure arc többek között az Azure Portal, noha a létrehozandó vagy kezelt erőforrás az Azure-infrastruktúrán kívül esik. A felhasználói élmény mintája ezekben az esetekben, különösen akkor, ha nincs közvetlen kapcsolat az Azure és a környezet között, a Azure Portal használatával hozzon létre egy parancsfájlt, amely azután letöltheti és végrehajthatja a környezetében, hogy biztonságos kapcsolatot hozzon létre az Azure-ba. Az Azure arc-kompatibilis kiszolgálók például ezt a mintát követik az [ív használatára képes kiszolgálók létrehozásához](../servers/onboard-portal.md).

Egyelőre, mivel az előzetes verzió csak az Azure arc-kompatibilis adatszolgáltatások közvetett csatlakoztatott üzemmódját támogatja, a Azure Portal használatával létrehozhat egy jegyzetfüzetet, amely letölthető és futtatható Azure Data Studio a Kubernetes-fürtön. A későbbiekben, ha a közvetlenül csatlakoztatott mód elérhető, az adatkezelőt közvetlenül a Azure Portal is kiépítheti. További információt a [kapcsolati módokról](connectivity.md)itt olvashat.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure-beli ív-adatkezelő létrehozása a Azure Portal használatával

Az alábbi lépéseket követve hozzon létre egy Azure arc-adatkezelőt a Azure Portal és a Azure Data Studio használatával.

1. Először jelentkezzen be a [Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)-re.  A piactér keresési eredményei úgy lesznek szűrve, hogy megjelenjenek az "Azure arc adatkezelője".
2. Ha az első lépés nem adta meg a keresési feltételeket. Adja meg a keresési eredményeket, kattintson az Azure arc-adatkezelő elemre.
3. Válassza ki az Azure adatkezelő csempét a piactéren.
4. Kattintson a **Létrehozás** gombra.
5. Tekintse át az Azure arc-adatkezelő létrehozásának követelményeit, és telepítse a hiányzó előfeltételként szükséges szoftvereket, például a Azure Data Studio és a kubectl.
6. Kattintson az **adatkezelő adatai** gombra.
7. Válassza ki az előfizetést, az erőforráscsoportot és az Azure-beli helyet ugyanúgy, mint bármely más, a Azure Portalban létrehozott erőforrást. Ebben az esetben a kiválasztott Azure-beli hely lesz az erőforrás metaadatainak tárolása.  Maga az erőforrás minden Ön által választott infrastruktúrán létrejön. Nem kell az Azure-infrastruktúrán lennie.
8. Adja meg az adatvezérlő nevét.
9. Válassza ki a kapcsolati módot az adatkezelőhöz. További információ a [kapcsolódási módokról és a követelményekről](./connectivity.md). 

   > [!NOTE] 
   > Ha a **közvetlen** csatlakozási mód lehetőséget választja, ügyeljen arra, hogy az egyszerű szolgáltatás hitelesítő adatai a szolgáltatásnév [létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)című témakörben leírt környezeti változókon keresztül legyenek beállítva. 

1. Válasszon egy központi telepítési konfigurációs profilt.
1. Kattintson a **Megnyitás az Azure Studióban** gombra.
1. A következő képernyőn megjelenik a kiválasztott elemek és a létrehozott jegyzetfüzetek összegzése.  A jegyzetfüzet letöltéséhez kattintson a **kiépítési jegyzetfüzet letöltése** gombra.

   > [!IMPORTANT]
   > Az Azure Red Hat OpenShift vagy a Red Hat OpenShift Container platformon az adatkezelő létrehozása előtt a biztonsági környezeti korlátozást kell alkalmaznia. Kövesse a [biztonsági környezeti korlátozás alkalmazása az Azure arc-kompatibilis adatszolgáltatásokra a OpenShift-on](how-to-apply-security-context-constraint.md)című témakör utasításait.

1. Nyissa meg Azure Data Studio a jegyzetfüzetet, és kattintson a felül található **összes futtatása** gombra.
1. Az adatvezérlő létrehozásának befejezéséhez kövesse a jegyzetfüzetben megjelenő utasításokat és útmutatást.

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret az "arc" névvel.  Ha más névtér-vagy adatvezérlő-nevet használt, az "ív" kifejezés helyére a nevét használhatja.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).