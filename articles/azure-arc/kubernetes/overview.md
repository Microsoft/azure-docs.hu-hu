---
title: Az Azure arc-kompatibilis Kubernetes áttekintése
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ez a cikk az Azure arc-kompatibilis Kubernetes áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560278"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Mi az Azure Arc-kompatibilis Kubernetes előzetes verziója?

Az Azure arc-kompatibilis Kubernetes előzetes verziójának használatával csatlakoztathat és konfigurálhat Kubernetes-fürtöket az Azure-on belül vagy kívül is. Ha egy Kubernetes-fürt csatlakozik az Azure arc-hoz, akkor az megjelenik a Azure Portalban. Azure Resource Manager azonosítója és felügyelt identitása lesz. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoport alatt találhatók, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást. 

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök egy nevű Kubernetes-névtérben futnak `azure-arc` , és szabványos Kubernetes üzemelő példányok. Az ügynökök felelősek az Azure-hoz való csatlakozásért, az Azure arc-naplók és-metrikák összegyűjtéséhez, valamint a konfigurációs kérések figyeléséhez. 

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Az adatok titkosságának biztosítása érdekében a rendszer a Azure Cosmos DB adatbázisában tárolja az adatok titkosítását is.
 
> [!NOTE]
> Az Azure arc-kompatibilis Kubernetes előzetes verzióban érhető el. Nem ajánlott éles környezetben üzemelő számítási feladatokhoz.

## <a name="supported-kubernetes-distributions"></a>Támogatott Kubernetes-disztribúciók

Az Azure arc-kompatibilis Kubernetes bármilyen Felhőbeli natív számítástechnikai alaprendszer (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürttel működik, mint például az AK-motor az Azure-ban, az AK-motor Azure Stack hub, a GKE, a EKS és a VMware vSphere-fürthöz.

Az Azure arc-kompatibilis Kubernetes funkcióit az arc csapata tesztelte a következő disztribúciókban:
* RedHat OpenShift 4,3
* Farmer RKE 1.0.8
* Canonical Charmed Kubernetes 1,18
* AKS-motor
* AK-motor Azure Stack hub-on
* AK Azure Stack HCI-ben
* Azure-beli fürt API-szolgáltatója

## <a name="supported-scenarios"></a>Támogatott esetek 

Az Azure arc-kompatibilis Kubernetes az alábbi forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes-ket a leltár, csoportosítás és címkézés céljából is összekapcsolhatjuk.

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával. 

* A fürtök megtekintéséhez és figyeléséhez használjon Azure Monitor tárolókat. 

* Házirendek alkalmazása a Kubernetes Azure Policy használatával. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója 
* Nyugat-Európa

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése](./connect-cluster.md)
