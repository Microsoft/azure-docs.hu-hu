---
title: Tervezett karbantartás használata az Azure Kubernetes Service (ak) fürthöz (előzetes verzió)
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a tervezett karbantartást az Azure Kubernetes szolgáltatásban (ak).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: deeb8375e2c1d30a71b0791886362bfb045ef6d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727824"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Tervezett karbantartás használata az Azure Kubernetes Service (ak) fürt karbantartási időszakának megtervezéséhez (előzetes verzió)

Az AK-fürtön a rendszeres karbantartás automatikusan történik. Alapértelmezés szerint ez a munka bármikor megtörténhet. A tervezett karbantartás lehetővé teszi a heti karbantartási időszakok bevezetését, amelyekkel frissítheti a vezérlési síkot, valamint a Kube-rendszer Hüvelyeit egy VMSS-példányon, és csökkentheti a számítási feladatok hatását. Az ütemezést követően az összes karbantartás a kiválasztott ablakon fog történni. Egy vagy több heti Windows-t ütemezhet a fürtön egy nap-vagy időtartomány egy adott napon történő megadásával. A karbantartási időszakok az Azure CLI használatával konfigurálhatók.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Korlátozások

Tervezett karbantartás használata esetén a következő korlátozások érvényesek:

- Az AK fenntartja a jogot arra, hogy a Windowst olyan nem tervezett/reaktív karbantartási műveletekre szüntesse meg, amelyek sürgős vagy kritikus fontosságúak.
- Jelenleg a karbantartási műveletek végrehajtása csak a *legalkalmasabb* , és nem garantált, hogy a megadott időszakon belül történnek.
- A frissítések több mint hét napja nem tiltható le.

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Szüksége lesz az *AK-előnézeti* Azure CLI-bővítmény 0.5.4 vagy újabb verziójára is. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Karbantartás engedélyezése minden hétfőn, 13:00 és 2 között

Karbantartási időszak hozzáadásához használhatja az `az aks maintenanceconfiguration add` parancsot.

> [!IMPORTANT]
> A tervezett karbantartási időszakok az egyezményes világidő (UTC) szerint vannak megadva.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

A következő példa kimenete a karbantartási időszakot jeleníti meg az 1. és 2. közötti időszakban, minden hétfőn.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Ha egy nap folyamán bármikor engedélyezni szeretné a karbantartást, hagyja ki a *Start-Hour* paramétert. A következő parancs például beállítja a karbantartási időszakot a teljes nap minden hétfőn:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Karbantartási konfiguráció hozzáadása JSON-fájllal

A paraméterek használata helyett használhat egy JSON-fájlt is a karbantartási időszak létrehozásához. Hozzon létre egy `test.json` fájlt a következő tartalommal:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

A fenti JSON-fájl megadja a karbantartási időszakokat keddenként (1:13:00 – 3. és szerdánként), a következő időpontnál: 13:00 – 2: és 6 órakor, 13:00 – 7:18 órakor. Kivétel keletkezik a *2021-05-26T03:00:00Z* és *2021-05-30T12:00:00Z* között, ahol a karbantartás nem engedélyezett, még akkor sem, ha átfedésben van egy karbantartási időszaktal. A következő parancs hozzáadja a karbantartási időszakokat a rendszerből `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Meglévő karbantartási időszak frissítése

Meglévő karbantartási konfiguráció frissítéséhez használja az `az aks maintenanceconfiguration update` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Meglévő fürt összes karbantartási ablakának listázása

Az AK-fürt összes jelenlegi karbantartási konfigurációs ablakának megtekintéséhez használja az `az aks maintenanceconfiguration list` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Az alábbi kimenetben láthatja, hogy két karbantartási ablak van konfigurálva a myAKSCluster. Az egyik ablak hétfőnként 1 órakor, a másik pedig pénteken, a következő időpontban: 13:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Adott karbantartási konfigurációs ablak megjelenítése egy AK-fürtben

Ha meg szeretné tekinteni az AK-fürt egy adott karbantartási konfigurációs ablakát, használja az `az aks maintenanceconfiguration show` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

A következő példa kimenetében az *alapértelmezett* karbantartási időszak látható:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Egy adott karbantartási konfigurációs ablak törlése egy meglévő AK-fürtben

Ha törölni szeretne egy bizonyos karbantartási konfigurációs ablakot az AK-fürtben, használja az `az aks maintenanceconfiguration delete` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Következő lépések

- Az AK-fürt frissítésének megkezdéséhez lásd: [AK-fürt frissítése][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
