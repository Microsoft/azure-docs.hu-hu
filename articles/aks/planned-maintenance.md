---
title: Tervezett karbantartás használata a Azure Kubernetes Service (AKS) fürthöz (előzetes verzió)
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a tervezett karbantartást a Azure Kubernetes Service (AKS) használatával.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782908"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Tervezett karbantartás használata a Azure Kubernetes Service (AKS) fürt karbantartási időszakának ütemezéséhez (előzetes verzió)

Az AKS-fürtön rendszeres karbantartást végeznek automatikusan. Alapértelmezés szerint ez a munka bármikor megtörténhet. A tervezett karbantartással heti karbantartási időszakokat ütemezhet, amelyek frissítik a vezérlősíkot és a Kube-rendszer podokat egy VMSS-példányon, és minimálisra csökkentik a számítási feladatokra gyakorolt hatást. Az ütemezést választva az összes karbantartás a kiválasztott időszakban történik. A fürtön egy vagy több heti időt is ütemezhet úgy, hogy egy adott napon egy napot vagy időtartományt ad meg. A karbantartási időszakokat az Azure CLI használatával lehet konfigurálni.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Korlátozások

Tervezett karbantartás használata esetén a következő korlátozások érvényesek:

- Az AKS fenntartja a jogot arra, hogy megszakítsa ezeket az ablakokat a sürgős vagy kritikus fontosságú, nem tervezett/reaktív karbantartási műveletek esetén.
- A karbantartási műveletek végrehajtása jelenleg csak a legjobb *lehetőség,* és nem garantált, hogy egy adott időszakon belül történik.
- A frissítések hét napnál tovább nem tilthatóak le.

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Szüksége lesz az *aks-preview Azure CLI-bővítmény* 0.5.4-es vagy újabb verziójára is. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add paranccsal.][az-extension-add] Vagy telepítse az elérhető frissítéseket az [az extension update paranccsal.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Karbantartás engedélyezése minden hétfőn 13:00 és 02:00 között

Karbantartási időszak hozzáadásához használhatja az `az aks maintenanceconfiguration add` parancsot.

> [!IMPORTANT]
> A tervezett karbantartási időszakokat a következő időpontban egyezményes világidő meg: (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Az alábbi példakimeneten a karbantartási időszak minden hétfőn 13:00 és 02:00 között látható.

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

Ha egy nap során bármikor engedélyeznie kell a karbantartást, hagyja ki a *start-hour* paramétert. A következő parancs például minden hétfőre beállítja a teljes napra vonatkozó karbantartási időszakokat:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Karbantartási konfiguráció hozzáadása JSON-fájllal

A paraméterek használata helyett használhat JSON-fájlt is karbantartási időszak létrehozásához. Hozzon `test.json` létre egy fájlt a következő tartalommal:

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

A fenti JSON-fájl minden kedd 13:00-tól 03:00-ig és minden szerda 13:00-00-14:00-kor és 6:00-07:00-kor határozza meg a karbantartási időszakokat. A *2021-05-26T03:00:00Z* és a *2021-05-30T12:00:00Z* közötti kivétel is van, ahol a karbantartás akkor sem engedélyezett, ha átfedésben van egy karbantartási időszakkal. A következő parancs hozzáadja a karbantartási időszakokat a `test.json` következőből: .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Meglévő karbantartási időszak frissítése

Meglévő karbantartási konfiguráció frissítéséhez használja az `az aks maintenanceconfiguration update` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Meglévő fürt összes karbantartási időszakának felsorolása

Az AKS-fürt összes aktuális karbantartási konfigurációs ablakának az paranccsal `az aks maintenanceconfiguration list` használhatja.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Az alábbi kimenetben látható, hogy két karbantartási időszak van konfigurálva a myAKSClusterhez. Az egyik ablak hétfőn 13:00-kor, a másik pedig péntek 04:00-kor van.

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

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Adott karbantartási konfigurációs időszak megjelenítése egy AKS-fürtben

Az AKS-fürt egy adott karbantartási konfigurációs ablakának az paranccsal `az aks maintenanceconfiguration show` megjelenik.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Az alábbi példakimenet az alapértelmezett karbantartási időszakát *mutatja be:*

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

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Egy meglévő AKS-fürt bizonyos karbantartási konfigurációs időszakának törlése

Ha törölni szeretne egy bizonyos karbantartási konfigurációs ablakot az AKS-fürtben, használja az `az aks maintenanceconfiguration delete` parancsot.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Következő lépések

- Az AKS-fürt frissítésének első lépésekért lásd: [AKS-fürt frissítése][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
