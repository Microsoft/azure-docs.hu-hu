---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564958"
---
## <a name="prepare-your-repository"></a>A tárház előkészítése

Azure App Service Build-kiszolgálóról származó automatizált buildek beszerzéséhez győződjön meg arról, hogy a tárház gyökerében a megfelelő fájlok vannak a projektben.

| Futtatókörnyezet | Gyökérkönyvtár fájljai |
|-|-|
| ASP.NET (csak Windows) | _*. SLN_, _*. csproj_ vagy _default. aspx_ |
| ASP.NET Core | _*. SLN_ vagy _*. csproj_ |
| PHP | _index. php_ |
| Ruby (csak Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ vagy _package.js_ egy indítási parancsfájllal |
| Python | _\* ._, _requirements.txt_ vagy _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. asp_, _index.htm_, _index.html_ vagy _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ az _alkalmazás \_ adatai/feladatok/folyamatos_ a folyamatos webjobs-feladatok, vagy az _alkalmazás \_ adatai/feladatok/aktivált_ webjobs esetén. További információ: [kudu webjobs-dokumentáció](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Lásd: [Azure functions folyamatos üzembe helyezése](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

A központi telepítés testreszabásához vegyen fel egy *. Deployment* fájlt az adattár gyökerébe. További információ: a [központi telepítések](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) és az [egyéni telepítési parancsfájlok](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)testreszabása.

> [!NOTE]
> Ha a Visual Studióban fejleszti a fejlesztést, [a Visual Studio létrehoz egy tárházat](/azure/devops/repos/git/creatingrepo?tabs=visual-studio). A projekt azonnal készen áll a git használatával történő üzembe helyezésre.
>

