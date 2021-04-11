---
title: Azure RBAC – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure RBAC képességének az Azure arc-kompatibilis Kubernetes kapcsolatos fogalmi áttekintését tartalmazza
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451013"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC az Azure arc-kompatibilis Kubernetes

A Kubernetes [ClusterRoleBinding és a RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) típusú objektumtípusok segítségével natív módon definiálhatja az Kubernetes-hitelesítést. Az Azure RBAC Azure Active Directory (Azure AD) és szerepkör-hozzárendeléseket is használhat az Azure-ban a fürt engedélyezési ellenőrzésének vezérléséhez.

Ezzel a szolgáltatással az Azure-beli szerepkör-hozzárendelések összes előnyét, például az Azure-erőforrás összes Azure RBAC-módosítását megjelenítő tevékenység-naplókat az Azure arc-kompatibilis Kubernetes-fürtre is érvényes lesz.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architektúra – Azure RBAC az Azure arc-kompatibilis Kubernetes

[![Azure RBAC architektúra ](./media/conceptual-azure-rbac.png)](./media/conceptual-azure-rbac.png#lightbox)

Annak érdekében, hogy az összes engedélyezési hozzáférés ellenőrzése az Azure-beli engedélyezési szolgáltatásba történjen, egy webhook-kiszolgáló ([Guard](https://github.com/appscode/guard)) van telepítve a fürtön.

A `apiserver` fürt úgy van konfigurálva, hogy a [webhook-jogkivonat hitelesítését](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) és a [webhook engedélyezését](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) használja, hogy a rendszer `TokenAccessReview` `SubjectAccessReview` a kéréseket az őr webhook-kiszolgáló felé irányítsa. A `TokenAccessReview` és a `SubjectAccessReview` kérelmeket a szolgáltatásnak küldött Kubernetes-erőforrásokra vonatkozó kérések aktiválják `apiserver` .

A Guard Ezután `checkAccess` meghívja az Azure-beli engedélyezési szolgáltatást, hogy ellenőrizze, hogy a kérelmező Azure ad-entitás hozzáfér-e a problémás erőforráshoz. 

Ha a hozzárendelésben egy olyan szerepkör található, amely engedélyezi ezt a hozzáférést, akkor `allowed` a rendszer választ küld az engedélyezési szolgáltatás guardtól. Az őr pedig `allowed` választ küld a `apiserver` szolgáltatásnak, amely lehetővé teszi a hívó entitás számára a kért Kubernetes-erőforrás elérését.


Ha nem létezik olyan szerepkör a hozzárendelésben, amely engedélyezi ezt a hozzáférést, akkor `denied` az engedélyezési szolgáltatástól kap választ a védelemre. Az őr `denied` választ küld a számára `apiserver` , így a hívó entitás 403 Tiltott hibát jelez a kért erőforráson.

## <a name="next-steps"></a>Következő lépések

* A rövid útmutató segítségével [Kubernetes-fürtöt létesíthet az Azure-ív](./quickstart-connect-cluster.md)használatával.
* [Azure-RBAC beállítása](./azure-rbac.md) az Azure arc-kompatibilis Kubernetes-fürtön.