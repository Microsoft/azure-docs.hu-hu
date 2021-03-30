---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85570210"
---
## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlba, először bontsa ki a fájlokat. Ha például letöltött egy ZIP-fájlt a GitHubról, akkor nem telepítheti a fájlt. A GitHub további beágyazott könyvtárakat is felvehet, amelyek nem működnek App Service. 
>

A helyi terminál ablakban navigáljon az alkalmazás-projekt gyökérkönyvtárához. 

Ennek a könyvtárnak tartalmaznia kell a webalkalmazás belépési fájlját, például: _index.html_, _index. php_ és _app.js_. Emellett olyan csomagkezelő fájlokat is tartalmazhat, mint például _aproject.js_, a _composer.js, a_ _package.jsa_, a _bower.jsés a_ _requirements.txt_.

Hacsak nem szeretné, hogy app Service az üzembe helyezés automatizálását, futtassa az összes felépítési feladatot (például:,,, `npm` `bower` `gulp` `composer` és `pip` ), és győződjön meg arról, hogy rendelkezik az alkalmazás futtatásához szükséges összes fájllal. Erre a lépésre akkor van szükség, ha [közvetlenül szeretné futtatni a csomagot](../articles/app-service/deploy-run-package.md).

Készítsen ZIP-archívumot a projekt minden eleméről. A `dotnet` projektek esetében ez a mappa a parancs kimeneti mappája `dotnet publish` . A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

