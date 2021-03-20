---
title: Magas rendelkezésre állás az Azure SQL Edge-tárolók számára – Azure SQL Edge
description: Ismerje meg az Azure SQL Edge-tárolók magas rendelkezésre állását
keywords: SQL Edge, tárolók, magas rendelkezésre állás
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935219"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Magas rendelkezésre állás az Azure SQL Edge-tárolók számára

Az Azure SQL Edge-példányokat natív módon hozhatja létre és kezelheti a Kubernetes-ben. Az Azure SQL Edge üzembe helyezése a [Kubernetes](https://kubernetes.io/)által felügyelt Docker-tárolókban. A Kubernetes-ben egy Azure SQL Edge-példánnyal rendelkező tároló automatikusan helyreállítható abban az esetben, ha egy fürtcsomópont meghibásodik. Az SQL Edge-tároló rendszerképét Kubernetes állandó mennyiségi jogcím (PVC) használatával konfigurálhatja. A Kubernetes figyeli az Azure SQL Edge folyamatát a tárolóban. Ha a folyamat, a hüvely, a tároló vagy a csomópont meghibásodik, a Kubernetes automatikusan elindít egy másik példányt, és újracsatlakozik a tárolóhoz.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Azure SQL Edge-tárolók a Kubernetes-on

A Kubernetes 1,6-es és újabb verzióiban a [*tárolási osztályok*](https://kubernetes.io/docs/concepts/storage/storage-classes/)és az [*állandó mennyiségi jogcímek*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)is támogatottak.

Ebben a konfigurációban a Kubernetes a tároló Orchestrator szerepét játssza le. 

![Az Azure SQL Edge diagramja egy Kubernetes-fürtben](media/deploy-kubernetes/kubernetes-sql-edge.png)

Az előző ábrán egy `azure-sql-edge` [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)tároló található. A Kubernetes összehangolja a fürt erőforrásait. Egy [replikakészlet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) biztosítja, hogy a pod automatikusan helyreálljon a csomópont meghibásodása után. Az alkalmazások a szolgáltatáshoz csatlakoznak. Ebben az esetben a szolgáltatás olyan terheléselosztó, amely egy olyan IP-címet tárol, amely a meghibásodása után azonos marad `azure-sql-edge` .

A következő ábrán a `azure-sql-edge` tároló nem sikerült. A Kubernetes garantálja a Orchestrator található kifogástalan állapotú példányok helyes számát, és a konfigurációnak megfelelően elindítja az új tárolót. A Orchestrator egy új Pod-t indít el ugyanazon a csomóponton, és `azure-sql-edge` újra csatlakozik ugyanahhoz az állandó tárolóhoz. A szolgáltatás csatlakozik az újból létrehozott létrehozáshoz `azure-sql-edge` .

![Azure SQL Edge egy Kubernetes-fürtön a pod-meghibásodás után](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

A következő ábrán a tárolót üzemeltető csomópont `azure-sql-edge` meghiúsult. A Orchestrator elindítja az új Pod-t egy másik csomóponton, és `azure-sql-edge` újra csatlakozik ugyanahhoz az állandó tárolóhoz. A szolgáltatás csatlakozik az újból létrehozott létrehozáshoz `azure-sql-edge` .

![Azure SQL Edge egy Kubernetes-fürtben a csomópont meghibásodása után](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Az Kubernetes-tárolók létrehozásával kapcsolatban lásd: [Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben](deploy-Kubernetes.md)

## <a name="next-steps"></a>Következő lépések

Az Azure SQL Edge-tárolók üzembe helyezéséhez az Azure Kubernetes szolgáltatásban (ak) tekintse meg a következő cikkeket:
- [Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben](deploy-Kubernetes.md)
- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével](tutorial-deploy-azure-resources.md).
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)