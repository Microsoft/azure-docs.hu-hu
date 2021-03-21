---
title: Az Azure dev Spaces-eszközök frissítése
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Ismerje meg, hogyan frissítheti az Azure dev Spaces parancssori eszközöket, a Visual Studio Code-bővítményt és a Visual Studio-bővítményt
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.openlocfilehash: f17643e6130abbc9d5da8b484144c95b0e803f33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199237"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Az Azure dev Spaces-eszközök frissítése

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ha van új kiadás, és már használja az Azure dev Spaces-t, lehet, hogy frissítenie kell az Azure dev Spaces-ügyféleszközök eszközeit.

## <a name="update-the-azure-cli"></a>Az Azure CLI frissítése

Amikor frissíti a legújabb Azure CLI-t, a dev Spaces CLI bővítmény legújabb verzióját is letöltheti.

Nem kell eltávolítania a korábbi verziót, csak keresse meg a megfelelő letöltést az [Azure CLI](/cli/azure/install-azure-cli)-ben.


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>A dev Spaces CLI bővítmény és a parancssori eszközök frissítése

Futtassa az alábbi parancsot:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>A VS Code bővítmény frissítése

A telepítés után a bővítmény automatikusan frissül. Előfordulhat, hogy újra kell töltenie a bővítményt az új funkciók használatához. A VS Code-ban nyissa meg a **bővítmények** ablaktáblát, válassza ki az **Azure dev Spaces** bővítményeket, és válassza az **Újratöltés** lehetőséget.

## <a name="update-visual-studio"></a>A Visual Studio frissítése

Az Azure fejlesztői tárhelyek az Azure-fejlesztési számítási feladatok részét képezik, és a Visual Studio összes frissítésében szerepelnek.

## <a name="next-steps"></a>Következő lépések

Tesztelje az új eszközöket új fürt létrehozásával. Próbálja ki a gyors útmutatókat és oktatóanyagokat az [Azure dev Spaces](../index.yml)webhelyen.
