---
title: Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le az Azure Red Hat-OpenShift és a Red Hat OpenShift 4-es verziójú, valamint a Azure Monitor for containers szolgáltatással történő figyelését.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621205"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Az Azure-és Red Hat OpenShift v4-fürt figyelésének leállítása

Miután engedélyezte az Azure Red Hat-OpenShift és a Red Hat OpenShift 4. x verziójú fürt figyelését, leállíthatja a fürt figyelését Azure Monitor for containers használatával, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan valósítható meg.  

## <a name="how-to-stop-monitoring-using-helm"></a>A figyelés leállítása a Helm használatával

1. A fürtön telepített containers Helm-diagram Azure Monitor azonosításához futtassa a következő Helm-parancsot.

    ```
    helm list
    ```

    A kimenet a következőhöz hasonló lesz:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers – a Release-1* a Azure monitor for containers esetében a Helm diagram kiadását jelenti.

2. A diagram kiadásának törléséhez futtassa a következő Helm-parancsot.

    `helm delete <releaseName>`

    Példa:

    `helm delete azmon-containers-release-1`

    Ezzel törli a kiadást a fürtből. A parancs futtatásával ellenőrizheti a `helm list` következőket:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A konfiguráció módosítása több percet is igénybe vehet. Mivel a Helm a törlése után is nyomon követi a kiadásokat, naplózhatja a fürt előzményeit, és visszavonhatja a kiadás törlését is `helm rollback` .

## <a name="next-steps"></a>Következő lépések

Ha a Log Analytics munkaterület csak a fürt figyelésének támogatására lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../platform/delete-workspace.md)című témakört.
