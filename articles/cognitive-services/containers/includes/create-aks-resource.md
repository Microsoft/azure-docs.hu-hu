---
title: Azure Kubernetes Service-fürt erőforrásának létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure Kubernetes-szolgáltatás (ak) erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017860"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes Service-fürt erőforrásának létrehozása

1. Nyissa meg az [Azure Kubernetes szolgáltatást](https://ms.portal.azure.com/#create/microsoft.aks), és válassza a **Létrehozás** lehetőséget.

1. Az **Alapvető beállítások** lapon adja meg a következőket:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport|Válasszon ki egy elérhető erőforráscsoportot.|
    |Kubernetes-fürt neve|Adjon meg egy nevet (kisbetűs).|
    |Region|Válasszon egy közeli helyet.|
    |Kubernetes verziója|Bármilyen érték van megjelölve **(alapértelmezett)**.|
    |DNS-név előtagja|A rendszer automatikusan létrehozta, de felülbírálhatja.|
    |Csomópont mérete|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|Hagyja meg a csúszkát az alapértelmezett értéknél.|

1. A **csomópont-készletek** lapon hagyja meg a **virtuális csomópontokat** és a virtuálisgép- **méretezési csoportokat** az alapértelmezett értékekre.
1. A **hitelesítés** lapon hagyja változatlanul az **egyszerű szolgáltatásnév** beállítást, és **engedélyezze a RBAC** alapértelmezett értékre való beállítását.
1. A **hálózatkezelés** lapon adja meg a következő beállításokat:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazások útválasztása|No|
    |Hálózatkezelési konfiguráció|Alapszintű|

1. Az **integrációk** lapon győződjön meg arról, hogy a **tároló figyelése** **engedélyezve** értékre van állítva, és hagyja meg **log Analytics munkaterület** alapértelmezett értékeként.
1. A **címkék** lapon hagyja üresen a név/érték párokat.
1. Válassza **a felülvizsgálat és létrehozás** lehetőséget.
1. Az érvényesítési fázisok után válassza a **Létrehozás** lehetőséget.

> [!NOTE]
> Ha az érvényesítés sikertelen, akkor előfordulhat, hogy egy "egyszerű szolgáltatásnév" hiba történt. Lépjen vissza a **hitelesítés** lapra, és térjen vissza az **ellenőrzés + létrehozás** elemre, ahol az érvényesítésnek futnia kell, majd át kell lépnie.
