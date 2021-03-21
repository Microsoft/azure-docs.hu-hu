---
title: DNS-továbbítás konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
description: DNS-továbbítás konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633869"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>DNS-továbbítás konfigurálása Azure Red Hat OpenShift 4 fürtön

A DNS-továbbítás Azure Red Hat OpenShift-fürtön való konfigurálásához módosítania kell a DNS-kezelőt. Ez a módosítás lehetővé teszi, hogy az alkalmazás-hüvelyek a fürtön belül fussanak a fürtön kívüli privát DNS-kiszolgálón tárolt nevek feloldásához. Ezeket a lépéseket a 4,6-es OpenShift [itt](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)találja.

Ha például a *. example.com összes DNS-kérelmét továbbítani kívánja egy DNS-kiszolgáló 192.168.100.10, az operátor konfigurációját a következő futtatásával módosíthatja:
 
```bash
oc edit dns.operator/default
```
 
Ez elindítja a szerkesztőt, és a következővel cserélhető le `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="next-steps"></a>Következő lépések
A DNS-továbbítással kapcsolatos további információkért tekintse meg a következőt: [OpenShift 4,6.](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)