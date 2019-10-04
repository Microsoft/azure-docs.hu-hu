---
title: Azure fejlesztői tárolóhelyek megosztása
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: 62d4affa5ef49de7600f9ccc800ea6bf83e4bd49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686625"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces megosztása

Azure fejlesztői szóközöket megoszthatja a csapat a fejlesztési tárhely másokkal. Minden fejlesztő saját tárolóhelyet, a használhatatlanná tévő mások lehetőségének nélkül is használhatók. Is együttműködése egy tárolóhely engedélyezheti, hogy tesztelje a kódot teljes körű mocks létrehozása és a függőségek szimulálása nélkül. Tekintse meg a [megismerheti a fejlesztési csapat](../team-development-nodejs.md) útmutató tartalmaz további tudnivalókat.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Állítsa be egy fejlesztési terület több fejlesztő

1. Hozzon létre egy fejlesztési tárolóhelyet az Azure-ban. Válasszon [.NET Core- és a VS Code](../get-started-netcore.md), [.NET Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy [Node.js és a VS Code](../get-started-nodejs.md). A kiválasztott Azure-előfizetés tulajdonosa vagy Közreműködője hozzá kell.
1. Konfigurálja az Azure fejlesztési területet **erőforráscsoport** való [közreműködői hozzáférés biztosítása](/azure/active-directory/role-based-access-control-configure) minden egyes csapattag számára. Ez a parancs futtatásával ellenőrizheti egy fejlesztési terület erőforráscsoportot: `azds list-up`
1. Kérje meg a csoport tagjai **jelölje ki a fejlesztői területet** , fejlesztése érdekében.
   * **Parancssor vagy a VS Code**: Megtekintheti a meglévő Azure fejlesztői tárolóhelyek fér hozzá: `azds space list`. Jelölje be a fejlesztési szóközzel: `azds space select`.
   * **Visual Studio IDE**: Nyissa meg a projekt a Visual Studióban válassza **Azure fejlesztési tárolóhelyek** az indítási beállítások legördülő listából. A megnyíló párbeszédpanelen válassza ki egy meglévő fürthöz.

     ![A Visual Studio indítási beállítások legördülő menü](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>További lépések

Lásd: [megismerheti a fejlesztési csapat](../team-development-nodejs.md) további információt.
