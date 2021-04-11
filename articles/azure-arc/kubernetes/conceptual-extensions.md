---
title: Fürt bővítményei – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes fürtszolgáltatási képességeinek fogalmi áttekintését tartalmazza
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451175"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Fürt bővítményei az Azure arc-kompatibilis Kubernetes

A [Helm-diagramok](https://helm.sh/) segítenek a Kubernetes-alkalmazások kezelésében azáltal, hogy a legbonyolultabb Kubernetes alkalmazások definiálásához, telepítéséhez és frissítéséhez szükséges építőelemeket biztosítanak. A fürtszolgáltatás a Helm csomagolási összetevőire épül. Ezt úgy teszi, hogy Azure Resource Manager-alapú felhasználói élményt biztosít a fürt bővítményeinek, például a Azure Monitor és az Azure Defender for Kubernetes telepítésének és életciklusának kezeléséhez. A fürtszolgáltatások szolgáltatás a következő extra előnyöket nyújtja a Helm-diagramokkal natív módon már elérhetőnél:

- Az összes fürt és a rájuk telepített bővítmények leltárának beolvasása.
- A Azure Policy segítségével automatizálhatja a fürt bővítményeinek központi telepítését.
- Fizessen elő minden bővítmény kiadási szerelvényéhez.
- A bővítmények automatikus verziófrissítésének beállítása.
- A bővítmény-példányok létrehozásának és életciklus-kezelési eseményeinek támogatása a frissítés és törlés során.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektúra

[![Fürt bővítményeinek architektúrája ](./media/conceptual-extensions.png)](./media/conceptual-extensions.png#lightbox)

A fürt kiterjesztési példánya Azure Resource Manager erőforrásként () van létrehozva `Microsoft.KubernetesConfiguration/extensions` Az Azure arc-kompatibilis Kubernetes-erőforrás (amelyet a `Microsoft.Kubernetes/connectedClusters` ) a Azure Resource Managerban. A Azure Resource Managerban való képviselet lehetővé teszi olyan szabályzat létrehozását, amely az összes Azure arc-kompatibilis Kubernetes-erőforrást ellenőrzi egy adott fürthöz vagy anélkül. Miután meghatározta, hogy mely fürtök nem rendelkeznek a kívánt tulajdonságértékek kibővítésével, a nem megfelelő erőforrásokat a Azure Policy használatával javíthatja.

A `config-agent` fürtön futó alkalmazás az Azure arc-kompatibilis Kubernetes-erőforrás új vagy frissített bővítmény-erőforrásait követi nyomon. A `extensions-manager` fürtben futó futtatása lekéri a Helm diagramot Azure Container Registry vagy Microsoft Container Registryról, és telepíti azt a fürtön. 

Mind a `config-agent` , mind a `extensions-manager` fürtben futó összetevők és bővítmények frissítési és kiterjesztési példányának törlése.

> [!NOTE]
> * `config-agent` az új vagy frissített bővítmény-erőforrások elérhetővé tétele az arc-kompatibilis Kubernetes-erőforráson. Így az ügynököknek kapcsolódniuk kell a kívánt állapothoz a fürtre való lekéréshez. Ha az ügynökök nem tudnak csatlakozni az Azure-hoz, a rendszer késlelteti a kívánt állapotnak a fürtre való továbbítását.
> * A bővítmény védett konfigurációs beállításait az Azure arc-kompatibilis Kubernetes-szolgáltatások akár 48 óráig tárolják. Ennek eredményeképpen, ha a fürt a bővítmény erőforrásának az Azure-ban való létrehozása után 48 órán belül megszakad a kapcsolat, a bővítmény az `Pending` állapotról `Failed` állapotra vált. Javasoljuk, hogy a fürtöket a lehető legrendszeresen online állapotba hozzuk.

## <a name="next-steps"></a>Következő lépések

* A rövid útmutató segítségével [Kubernetes-fürtöt létesíthet az Azure-ív](./quickstart-connect-cluster.md)használatával.
* [Fürt-bővítmények üzembe helyezése](./extensions.md) az Azure arc-kompatibilis Kubernetes-fürtön.