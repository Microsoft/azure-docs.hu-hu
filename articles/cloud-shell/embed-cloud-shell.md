---
title: Beágyazási Azure Cloud Shell | Microsoft Docs
description: Ismerkedjen meg Azure Cloud Shell beágyazásával.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "60614310"
---
# <a name="embed-azure-cloud-shell"></a>Beágyazás Azure Cloud Shell

A beágyazási Cloud Shell lehetővé teszi a fejlesztők és a tartalmi írók számára, hogy közvetlenül nyissanak meg Cloud Shell egy dedikált URL-címről, a [shell.Azure.com](https://shell.azure.com). Ez azonnal a Cloud Shell hitelesítésének, szerszámozásának és naprakész Azure CLI/Azure PowerShell eszközeinek teljes erejét biztosítja a felhasználók számára.

Normál méretű gomb

[![Rendszeres indítás](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)

Nagy méretű gomb

[![Nagyméretű indítás](https://shell.azure.com/images/launchcloudshell@2x.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)

## <a name="how-to"></a>Használati útmutató

A következő lépésekkel integrálhatja Cloud Shell indítás gombját a Markdown-fájlokba:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Az előugró Cloud Shell beágyazására szolgáló HTML a következő:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Felhasználói élmény testreszabása

Az URL-cím kibővítésével beállíthat egy adott rendszerhéj-élményt.

|Élmény   |URL-cím   |
|---|---|
|Legutóbb használt rendszerhéj   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Következő lépések
[Bash Cloud Shell rövid útmutatóban](quickstart.md)<br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md)
