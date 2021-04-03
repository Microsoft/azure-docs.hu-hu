---
title: A kubectl használata az Azure dev Spaces használatával
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a kubectl-parancsokat egy Azure Kubernetes Service-fürt fejlesztői területén az Azure dev Spaces használatával
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960160"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata az Azure fejlesztői területtel

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A Kubernetes-fürtöt egy Azure fejlesztői tárhelyen belül érheti el, és használhatja a meglévő Kubernetes-eszközöket, például a következőt: `kubectl` .

A futó `az aks use-dev-spaces` parancs automatikusan hozzáadja a `kubectl` konfigurációs környezetet, így a kubectl már csatlakoznia kell az Azure dev Spaces Kubernetes-fürthöz. Angol nyelvű Példák:
- Az aktuális környezet megerősítése: `kubectl config current-context`
- Az összes elérhető kontextus listázása: `kubectl config get-contexts` . 
- Környezet módosítása: `kubectl config use-context <context-name>`
- Tekintse meg a Kubernetes irányítópultját: futtassa a `kubectl proxy` parancsot, majd nyissa meg a böngészőt a parancs által kibocsátott címre (fűzze hozzá az URL-címhez, `/ui` és keresse meg a Kubernetes irányítópultját).
- A futó szolgáltatások listázása az alapértelmezett Azure dev Spaces Space *default (alapértelmezett*) nevű helyen: `kubectl get services --namespace=default`

