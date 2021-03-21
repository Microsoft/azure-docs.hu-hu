---
title: Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le az Azure Red Hat-OpenShift és a Red Hat OpenShift 4-es verziójú, a Container-információkkal való felügyeletét.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731799"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Az Azure-és Red Hat OpenShift v4-fürt figyelésének leállítása

Miután engedélyezte az Azure Red Hat-OpenShift és a Red Hat OpenShift 4. x verziójú fürt figyelését, leállíthatja a fürt figyelését a tároló-megállapításokkal, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan valósítható meg.  

## <a name="how-to-stop-monitoring-using-helm"></a>A figyelés leállítása a Helm használatával

1. A következő Helm-parancs futtatásával azonosíthatja a fürtön telepített Container bepillantást az Helm chart kiadásban.

    ```
    helm list
    ```

    A kimenet a következőhöz hasonló lesz:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon – tárolók – a Release-1* a Helm chart kiadását jelöli a tárolók bepillantást.

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

Ha a Log Analytics munkaterület csak a fürt figyelésének támogatására lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../logs/delete-workspace.md)című témakört.