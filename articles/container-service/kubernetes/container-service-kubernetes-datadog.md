---
title: (ELAVULT) Azure-beli Kubernetes-fürt figyelése a Datadoggal
description: Figyelés a Datadoggal használata az Azure Container Service Kubernetes-fürtön
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712338"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(ELAVULT) A figyelő az Azure Container Service-fürt a Datadoggal

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Az útmutató feltételezi, hogy [egy Kubernetes-fürtöt az Azure Container Service használatával létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` az Azure cli és `kubectl` telepített eszközök.

Ha rendelkezik tesztelheti a `az` futtatásával telepített eszköz:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszközt telepítette, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).

Ha rendelkezik tesztelheti a `kubectl` futtatásával telepített eszköz:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadoggal egy olyan megfigyelő szolgáltatás, amely figyelési adatait gyűjti össze az Azure Container Service-fürtben található tárolókat. Datadoggal rendelkezik, ahol megtekintheti az adott mérőszámok belül a tárolókat a Docker integrációs irányítópult. A tárolók gyűjtött metrikák CPU, memória, hálózati és i/o szerint vannak rendezve. Datadoggal metrikák felosztja a tárolók és a képeket.

Először létre kell [-fiók létrehozása](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>A DaemonSet a Datadoggal ügynök telepítése
Kubernetes DaemonSets használják egy tárolót egyetlen példányát futtatni a fürt minden gazdagépen.
Azok a futó monitorozási ügynökök ideális.

Miután a Datadoggal bejelentkezett, a [Datadoggal utasításokat](https://app.datadoghq.com/account/settings#agent/kubernetes) Datadoggal ügynökök telepíthetők a fürtön a DaemonSet használatával.

## <a name="conclusion"></a>Összegzés
Ennyi az egész! Ha az ügynökök és megtekintheti a konzol adatainak néhány perc múlva. Keresse fel az integrált [kubernetes-irányítópult](https://app.datadoghq.com/screen/integration/kubernetes) a fürt összegzését.
