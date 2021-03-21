---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102205981"
---
A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le a *\<deploymentLocalGitUrl-from-create-step>* elemet annak a git-távoli URL-címére, amelyet a [Webalkalmazás létrehozása](#create-a-web-app)során mentett.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, akkor győződjön meg arról, hogy megadja az **üzembe helyezési felhasználó konfigurálása** során létrehozott hitelesítő adatokat, nem pedig a Azure Portalba való bejelentkezéshez használt hitelesítő adatokat.

```bash
git push azure main
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
