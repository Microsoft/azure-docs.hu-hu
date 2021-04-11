---
title: Fürthöz csatlakozó – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes fürt csatlakozási funkciójának fogalmi áttekintését tartalmazza
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451010"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Fürthöz való kapcsolat az Azure arc-kompatibilis Kubernetes

Az Azure arc-kompatibilis Kubernetes- *fürt csatlakoztatási* funkciója lehetővé teszi a fürthöz való kapcsolódást `apiserver` anélkül, hogy a tűzfalon engedélyezni kellene a bejövő portok használatát. A fürtön futó fordított proxy ügynök kimenő módon képes biztonságosan elindítani egy munkamenetet az Azure arc szolgáltatással. 

A fürthöz való kapcsolódás lehetővé teszi a fejlesztők számára, hogy bárhonnan hozzáférjenek a fürtökhöz az interaktív fejlesztéshez és hibakereséshez. Azt is lehetővé teszi, hogy a fürtöket használó felhasználók és rendszergazdák bárhonnan hozzáférjenek a fürtökhöz, vagy felügyelhetik azokat. Az Azure-folyamatok, a GitHub-műveletek, vagy bármely más üzemeltetett CI/CD-szolgáltatás üzemeltetett ügynökeit vagy más, az alkalmazások helyszíni fürtökön való üzembe helyezését is igénybe veheti, anélkül, hogy a saját üzemeltetésű ügynököket kellene használnia.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektúra

[![Fürt csatlakozási architektúrája ](./media/conceptual-cluster-connect.png)](./media/conceptual-cluster-connect.png#lightbox)

A fürt oldalán az `clusterconnect-agent` ügynök Helm diagram részeként üzembe helyezett fordított proxy ügynök az Azure arc Service-be kimenő hívásokat kezdeményez a munkamenet létrehozásához.

A felhasználó a `az connectedk8s proxy` következőket hívja:
1. Az Azure arc proxy bináris fájlja le van töltve, és folyamatként megpördült az ügyfélszámítógépen. 
1. Az Azure arc-proxy beolvassa `kubeconfig` Az Azure arc-kompatibilis Kubernetes-fürthöz társított fájlt, amelyen a `az connectedk8s proxy` meghívásra kerül.
    * Az Azure arc proxy a hívó Azure-hozzáférési tokenjét és a Azure Resource Manager azonosító nevét használja. 
1. Az `kubeconfig` Azure arc proxy által a gépre mentett fájl a kiszolgáló URL-címét az Azure arc proxy folyamatának egyik végpontján tárolja.

Ha a felhasználó a következő fájllal küld egy kérelmet `kubeconfig` :
1. Az Azure arc-proxy leképezi a kérést az Azure arc szolgáltatásnak fogadó végpontot. 
1. Az Azure arc Service ezután továbbítja a kérést a `clusterconnect-agent` fürtön futónak. 
1. A `clusterconnect-agent` kérések átadása az `kube-aad-proxy` összetevőnek, amely az Azure ad-hitelesítést hajtja végre a hívó entitáson. 
1. Az Azure AD-hitelesítés után `kube-aad-proxy` a Kubernetes [felhasználó megszemélyesítési](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) funkciójával továbbítja a kérést a fürtnek `apiserver` .

## <a name="next-steps"></a>Következő lépések

* A rövid útmutató segítségével [Kubernetes-fürtöt létesíthet az Azure-ív](./quickstart-connect-cluster.md)használatával.
* Biztonságosan [férhet hozzá a fürthöz](./cluster-connect.md) bárhonnan a fürt csatlakoztatásával.