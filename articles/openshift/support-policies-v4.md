---
title: Azure Red Hat OpenShift 4 fürt támogatási szabályzata
description: A Red Hat OpenShift 4 támogatási szabályzatával kapcsolatos követelmények ismertetése.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: ec27d054055866c72148ad6eb024d4324f063ce8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83774392"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift-támogatási szabályzat

Az Azure Red Hat OpenShift 4 fürtök bizonyos konfigurációi befolyásolhatják a fürt támogatását. Az Azure Red Hat OpenShift 4 lehetővé teszi, hogy a fürt rendszergazdája módosításokat hajtson végre a belső fürt összetevőin, de nem minden módosítás támogatott. Az alábbi támogatási szabályzat megosztja azokat a módosításokat, amelyek sértik a szabályzatot, és érvénytelenítik a Microsoft és a Red Hat támogatását.

> [!NOTE]
> Az Azure Red Hat OpenShift nem támogatja az OpenShift-tároló platformon megjelenő technológiai előnézet funkciókat.

## <a name="cluster-configuration-requirements"></a>A fürt konfigurációs követelményei

* Az összes OpenShift-fürtnek felügyelt állapotban kell maradnia. A fürtözött operátorok listája a futtatásával adható vissza `oc get clusteroperators` .
* Ne távolítsa el vagy módosítsa a fürt Prometheus-és Alertmanager-szolgáltatásait.
* Ne távolítsa el a szolgáltatás Alertmanager szabályait.
* Ne módosítsa a OpenShift-fürt verzióját.
* Ne távolítsa el vagy módosítsa az Azure Red Hat OpenShift szolgáltatás naplózását (MDSD hüvely).
* Ne távolítsa el vagy módosítsa a "arosvc.azurecr.io" fürt lekérési titkát.
* Minden fürt virtuális gépnek közvetlen kimenő internet-hozzáféréssel kell rendelkeznie, legalább a Azure Resource Manager (ARM) és a Service Logging (Genf) végpontokhoz.  A HTTPS-proxyk egyetlen formája sem támogatott.
* Ne módosítsa a fürt virtuális hálózatának DNS-konfigurációját. Az alapértelmezett Azure DNS feloldót kell használni.
* Semmilyen módon ne bírálja felül a fürt MachineConfig-objektumait (például a kubelet konfigurációját).
* Az Azure Red Hat OpenShift szolgáltatás privát kapcsolati szolgáltatáson keresztül fér hozzá a fürthöz.  Ne távolítsa el vagy módosítsa a szolgáltatás elérését.
* A nem RHCOS számítási csomópontok nem támogatottak. Nem használhat például RHEL számítási csomópontot.

## <a name="supported-virtual-machine-sizes"></a>Támogatott virtuális gépek méretei

Az Azure Red Hat OpenShift 4 a következő virtuálisgép-méreteken támogatja a feldolgozó csomópontok példányait:

### <a name="general-purpose"></a>Általános célú

|Adatsorozat|Méret|vCPU|Memória: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Memóriaoptimalizált

|Adatsorozat|Méret|vCPU|Memória: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Számításra optimalizált

|Adatsorozat|Méret|vCPU|Memória: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Főcsomópontok

|Adatsorozat|Méret|vCPU|Memória: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
