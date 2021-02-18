---
title: Az Azure arc-kompatibilis Kubernetes áttekintése
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ez a cikk az Azure arc-kompatibilis Kubernetes áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652530"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Mi az Azure Arc-kompatibilis Kubernetes?

Az Azure arc-kompatibilis Kubernetes az Azure-on belüli vagy kívül található Kubernetes-fürtöket csatlakoztathatja és konfigurálhatja. Ha egy Kubernetes-fürtöt az Azure arc-hoz csatlakozik, az a következő lesz:
* Megjelenik a Azure Portal egy Azure Resource Manager-AZONOSÍTÓval és egy felügyelt identitással. 
* Legyen csatolva a standard Azure-előfizetésekhez.
* Egy erőforráscsoporthoz kerül.
* A címkéket ugyanúgy fogadhatja, mint bármely más Azure-erőforrást. 

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök:
* Futtassa a parancsot a `azure-arc` Kubernetes névtérben normál Kubernetes-telepítésként.
* Az Azure-hoz való kapcsolódás kezelése.
* Azure-beli ív-naplók és-metrikák gyűjtése.
* Tekintse meg a konfigurációs kérelmeket. 

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Ezek az adatok titkosítva tárolódnak egy Azure Cosmos DB adatbázisban, és biztosítják az adatok titkosságát.
 
## <a name="supported-kubernetes-distributions"></a>Támogatott Kubernetes-disztribúciók

Az Azure arc-kompatibilis Kubernetes bármilyen Felhőbeli natív számítástechnikai alaprendszer (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürttel működik, például:
* AK – Azure-beli motor
* AK-motor Azure Stack hub-on
* GKE
* EKS
* VMware vSphere

Az Azure arc-kompatibilis Kubernetes funkcióit az arc csapata tesztelte a következő disztribúciókban:
* RedHat OpenShift 4,3
* Farmer RKE 1.0.8
* Canonical Charmed Kubernetes 1,18
* AKS-motor
* AK-motor Azure Stack hub-on
* AK Azure Stack HCI-ben
* Azure-beli fürt API-szolgáltatója

## <a name="supported-scenarios"></a>Támogatott esetek 

Az Azure arc-kompatibilis Kubernetes a következő forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes-ket a leltár, csoportosítás és címkézés céljából is összekapcsolhatjuk.

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával. 

* Megtekintheti és figyelheti a fürtöket a tárolók Azure Monitor használatával. 

* Házirendek alkalmazása a Kubernetes Azure Policy használatával. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója 
* Nyugat-Európa

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése](./connect-cluster.md)
