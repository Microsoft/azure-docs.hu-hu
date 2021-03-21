---
title: Kulcs-érték párok beolvasása egy adott időpontban
titleSuffix: Azure App Configuration
description: Lekérheti a régi kulcs-érték párokat az Azure-alkalmazás konfigurációjában az időponthoz kötődő Pillanatképek használatával, amely a kulcsok értékének változásait rögzíti.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929897"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja a kulcs-értékeken végrehajtott módosítások rekordját tárolja. Ez a rekord a kulcs-érték változások ütemezését jeleníti meg. A kulcs-érték előzményeit újra létrehozhatja, és a korábbi értékeket megadhatja a legfontosabb előzmények időszakában (7 nap az ingyenes csomagok esetében, vagy 30 nap a standard szintű tárolók számára). Ezzel a szolgáltatással visszafelé is lekérheti az időpontot, és lekérheti a régi kulcs értékét. Például helyreállíthatja a legutóbbi telepítés előtt használt konfigurációs beállításokat, hogy visszaállítsa az alkalmazást az előző konfigurációra.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcs-értékek lekéréséhez Azure Portal vagy CLI-t használhat. Az Azure CLI-ben `az appconfig revision list` a szükséges értékek beolvasásához használja a t, és adja hozzá a megfelelő paramétereket.  Adja meg az Azure-alkalmazás konfigurációs példányát úgy, hogy az áruház nevét ( `--name <app-config-store-name>` ) vagy egy () adatforrást használ `--connection-string <your-connection-string>` . Korlátozza a kimenetet egy adott időpontot () megadva `--datetime` , valamint a visszaadni kívánt elemek maximális számának megadásával ( `--top` ).

Ha nem helyileg telepítette az Azure CLI-t, igény szerint [Azure Cloud Shell](../cloud-shell/overview.md)is használhatja.

A Key-Values összes rögzített módosításának beolvasása.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

A kulcs és a címkék összes rögzített módosításának beolvasása `environment` `test` `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

A hierarchikus kulcs területének összes rögzített módosításának beolvasása `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

A kulcs összes rögzített módosításának beolvasása `color` egy adott időpontban.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Kérje le az utolsó 10 rögzített módosítást a kulcs-értékekre, és csak a `key` , és az időbélyegző értékét adja vissza `label` `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)