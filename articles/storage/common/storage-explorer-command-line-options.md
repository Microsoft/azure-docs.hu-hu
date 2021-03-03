---
title: Azure Storage Explorer parancssori kapcsolók | Microsoft Docs
description: Azure Storage Explorer indítási parancssori kapcsolók dokumentációja
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745412"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Storage Explorer parancssori kapcsolók

A Microsoft Azure Storage Explorer parancssori kapcsolókat tartalmaz, amelyek az alkalmazás indításakor adhatók hozzá. A parancssori kapcsolók többsége hibakeresési és hibaelhárítási célokat szolgál.

## <a name="command-line-options"></a>Parancssori kapcsolók
Beállítás  | Leírás
:------- | :-----------
`--debug`/`--prod`  | Indítsa el az alkalmazást hibakeresési vagy éles módban. Hibakeresési módban a helyi mellékleteket az alkalmazás helyi tárolójában fogja tárolni, és nem titkosítja a rendszer. A rejtett tulajdonságok a kiválasztott erőforrás-csomópontok tulajdonságok paneljén jelennek meg. A naplózási részletességi szint a Storage Explorer belső telepítési logikáját felderítő hibakeresési üzenetek nyomtatására lesz beállítva. Az alapértelmezett érték `--prod`.
`--lang`  | Indítsa el az alkalmazást egy adott nyelvvel. Például: `--lang="zh-Hans"`.
`--disable-gpu` | Indítsa el az alkalmazást GPU-gyorsítás nélkül.
`--auto-open-dev-tools` | Hagyja, hogy az alkalmazás megnyissa a fejlesztői eszközök ablakot, amint megjelenik a böngészőablak. Ez a beállítás akkor hasznos, ha egy töréspontot szeretne elérni egy sorban a böngészőablak indítási kódjában.
`--verbosity` | Storage Explorer naplózás részletességi szintjének beállítása. A támogatott részletességi szintek a következők:,,,, `debug` `verbose` `info` `warn` `error` és `silent` . Például: `--verbosity=verbose`. Éles módban történő futtatáskor az alapértelmezett részletességi szint a `info` . Hibakeresési módban való futtatáskor a napló részletességi szintje mindig lesz `debug` .
`--log-dir` | Állítsa be a könyvtárat a naplófájlok mentéséhez. Például: `--log-dir=path_to_a_directory`.

Példa a Storage Explorer indítására egyéni parancssori kapcsolókkal

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Ezek a parancssori kapcsolók új Storage Explorer-verziókban változhatnak.

## <a name="when-to-use-command-line-options"></a>Mikor kell használni a parancssori kapcsolókat

Egyes parancssori kapcsolók használatával testre szabhatók a Storage Explorer. Azokhoz a beállításokhoz, amelyek megfelelő felhasználói beállításokkal rendelkeznek, például: `--lang` . Javasoljuk, hogy a parancssori kapcsoló használata helyett a felhasználói beállításokat használja. 

A többi parancssori kapcsoló a hibakereséshez és a hibaelhárításhoz is hasznos lehet. Ha Storage Explorer probléma lép fel, a hibakeresési módban a probléma reprodukálása segíthet a vizsgálat részletesebb információinak megszerzésében.