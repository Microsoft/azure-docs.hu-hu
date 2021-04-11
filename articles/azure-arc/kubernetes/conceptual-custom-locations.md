---
title: Egyéni helyszínek – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes egyéni helyeinek általános áttekintését tartalmazza
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451026"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Az Azure arc-kompatibilis Kubernetes egyéni helyei

Az Azure Location-konstrukció kiterjesztéseként az *Egyéni helyek* lehetővé teszik a bérlői rendszergazdák számára, hogy az Azure-szolgáltatások példányainak üzembe helyezéséhez célként megadott helyként használják az Azure arc-kompatibilis Kubernetes-fürtöket. Az Azure-erőforrások példái közé tartozik az Azure arc használatára képes SQL felügyelt példány és az Azure arc engedélyezve PostgreSQL nagy kapacitású.

Az Azure-beli helyekhez hasonlóan a bérlőn belül az egyéni helyekhez való hozzáféréssel rendelkező végfelhasználók is üzembe helyezhetik az erőforrásokat a vállalat saját számítási szolgáltatásával.

[![Arc platform rétegek ](./media/conceptual-arc-platform-layers.png)](./media/conceptual-arc-platform-layers.png#lightbox)

Egyéni helyet jeleníthet meg absztrakt rétegként az Azure arc-kompatibilis Kubernetes-fürtön, a fürtben való kapcsolaton és a fürt bővítményein. Az egyéni helyszínek létrehozzák a további Azure-szolgáltatásokhoz szükséges részletes [RoleBindings és ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) a fürt eléréséhez. Ezeknek az egyéb Azure-szolgáltatásoknak a fürthöz való hozzáférésre van szükségük ahhoz, hogy kezelje az ügyfél által a fürtökön telepítendő erőforrásokat.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektúra

Ha a rendszergazda engedélyezi a fürt egyéni helyei szolgáltatást, létrejön egy ClusterRoleBinding a fürtön, amely engedélyezi az egyéni telephelyek erőforrás-szolgáltatója (RP) által használt Azure AD-alkalmazást. Az engedélyezést követően a többi Azure RPs-beli egyéni helyszínek ClusterRoleBindings vagy RoleBindings hozhatnak létre a fürtön található egyéni erőforrások létrehozásához. A fürtön telepített fürt-bővítmények határozzák meg az engedélyezéshez szükséges RPs-listát.

[![Egyéni helyszínek ](./media/conceptual-custom-locations-usage.png) használata](./media/conceptual-custom-locations-usage.png#lightbox)

Amikor a felhasználó létrehoz egy adatszolgáltatási példányt a fürtön: 
1. A PUT-kérést a rendszer a Azure Resource Managerba küldi.
1. A PUT kérelmet az Azure arc engedélyezve Data Services RP-re továbbítja a rendszer. 
1. Az RP beolvassa az `kubeconfig` Azure arc-kompatibilis Kubernetes-fürthöz társított fájlt, amelyen az egyéni hely létezik. 
   * Az egyéni hely hivatkozása `extendedLocation` az eredeti Put-kérelemben szerepel. 
1. Az Azure arc engedélyezve Data Services RP a `kubeconfig` használatával kommunikál a fürttel az Azure arc-kompatibilis Data Services típus egyéni erőforrásának létrehozásához az egyéni helyre leképezett névtérben. 
   * Az Azure arc engedélyezve Data Services operátort a fürt bővítményének létrehozásával telepítettük az egyéni hely létezése előtt. 
1. Az Azure arc engedélyezve Data Services operátor beolvassa a fürtön létrehozott új egyéni erőforrást, és létrehozza az adatvezérlőt, amely a kívánt állapotnak a fürtön való megvalósítására van lefordítva. 

A felügyelt SQL-példány és a PostgreSQL-példány létrehozásának lépései a fent ismertetett lépések sorrendjével azonosak.

## <a name="next-steps"></a>Következő lépések

* A rövid útmutató segítségével [Kubernetes-fürtöt létesíthet az Azure-ív](./quickstart-connect-cluster.md)használatával.
* [Hozzon létre egy egyéni helyet](./custom-locations.md) az Azure arc-kompatibilis Kubernetes-fürtön.