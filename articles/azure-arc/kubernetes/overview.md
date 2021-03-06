---
title: Az Azure arc-kompatibilis Kubernetes áttekintése
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ez a cikk az Azure arc-kompatibilis Kubernetes áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók
ms.custom: references_regions
ms.openlocfilehash: 69e9886f214d0076c8e66231fd6ad15bb060828f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449647"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Mi az Azure Arc-kompatibilis Kubernetes?

Az Azure arc-kompatibilis Kubernetes az Azure-on belüli vagy kívül található Kubernetes-fürtöket csatlakoztathatja és konfigurálhatja. Ha egy Kubernetes-fürtöt az Azure arc-hoz csatlakozik, az a következő lesz:
* Megjelenik a Azure Portal egy Azure Resource Manager-AZONOSÍTÓval és egy felügyelt identitással. 
* Egy Azure-előfizetésbe és-erőforráscsoporthoz kerülnek.
* A címkéket ugyanúgy fogadhatja, mint bármely más Azure-erőforrást. 

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök:
* Futtassa a parancsot a `azure-arc` Kubernetes névtérben normál Kubernetes-telepítésként.
* Az Azure-hoz való kapcsolódás kezelése.
* Azure-beli ív-naplók és-metrikák gyűjtése.
* Tekintse meg a konfigurációs kérelmeket. 

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Az adatok titkosságának biztosítása érdekében a rendszer a Azure Cosmos DB adatbázisában tárolja az adatok titkosítását is.

## <a name="supported-kubernetes-distributions"></a>Támogatott Kubernetes-disztribúciók

Az Azure arc-kompatibilis Kubernetes bármilyen Felhőbeli natív számítástechnikai alaprendszer (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürttel működik. Az Azure arc csapata a [fő iparági partnerekkel együttműködve érvényesíti](./validation-program.md) a Kubernetes-eloszlások megfelelőségét az Azure arc-kompatibilis Kubernetes.

## <a name="supported-scenarios"></a>Támogatott esetek 

Az Azure arc-kompatibilis Kubernetes a következő forgatókönyveket támogatja: 

* Az Azure-on kívül futó Kubernetes-ket a leltár, csoportosítás és címkézés céljából is összekapcsolhatjuk.

* Alkalmazások telepítése és konfiguráció alkalmazása a GitOps-alapú konfiguráció-kezelés használatával. 

* Megtekintheti és figyelheti a fürtöket a tárolók Azure Monitor használatával.

* A fenyegetések elleni védelem kikényszeríthető az Azure Defender for Kubernetes használatával.

* Házirendek alkalmazása a Kubernetes Azure Policy használatával.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók 

Az Azure arc-kompatibilis Kubernetes jelenleg a következő régiókban támogatott: 

* USA keleti régiója
* Nyugat-Európa
* USA nyugati középső régiója
* USA déli középső régiója
* Délkelet-Ázsia
* Az Egyesült Királyság déli régiója
* USA 2. nyugati régiója
* Kelet-Ausztrália
* USA 2. keleti régiója
* Észak-Európa

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan csatlakoztatható a fürt az Azure arc szolgáltatáshoz.
> [!div class="nextstepaction"]
> [Fürt összekötése az Azure-ív használatával](./quickstart-connect-cluster.md)
