---
title: Azure Storage (tároló) hozzáadása
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást egy tárolóban lévő alkalmazásban Azure App Serviceban. Fájlok megosztása az alkalmazások között, a statikus tartalmak távoli és helyileg elérhetővé való kezelése stb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c3e5ca30b7f0f00b6d647f5fdaeb772900996adc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720239"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Hozzáférés az Azure Storage-hoz (előzetes verzió) hálózati megosztásként App Service-tárolóból

::: zone pivot="container-windows"

Ez az útmutató bemutatja, hogyan csatlakoztathatja az Azure Storage-fájlokat hálózati megosztásként egy App Service lévő Windows-tárolóhoz. Csak [Azure Files megosztások](../storage/files/storage-how-to-use-files-cli.md) és [prémium fájlok megosztása](../storage/files/storage-how-to-create-file-share.md) támogatott. Az előnyök közé tartoznak a biztonságos tartalom, a tartalom hordozhatósága, a több alkalmazáshoz való hozzáférés és a több átadási módszer is.

> [!NOTE]
>Az Azure Storage App Service **előzetes** verzióban érhető el, és **éles környezetben** **nem támogatott** .

::: zone-end

::: zone pivot="container-linux"

Ez az útmutató bemutatja, hogyan csatlakoztathatja az Azure Storage-t egy Linux-tárolóhoz App Service. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a több átadási módszer.

> [!NOTE]
>Az Azure Storage-ban App Service **előzetes** verzióban érhető el a Linux és a Web App for containers app Service. **Éles környezetben** **nem támogatott** .

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="container-windows"

- [Egy meglévő Windows-tároló alkalmazás a Azure App Service](quickstart-custom-container.md)
- [Azure-fájlmegosztás létrehozása](../storage/files/storage-how-to-use-files-cli.md)
- [Fájlok feltöltése az Azure-fájlmegosztásba](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Egy meglévő [app Service Linux-alkalmazásban](index.yml).
- [Azure Storage-fiók](../storage/common/storage-account-create.md?tabs=azure-cli)
- Egy [Azure-fájlmegosztás és-könyvtár](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> A Azure Files nem alapértelmezett tároló, és a webalkalmazásban nem szereplő külön számlázható. Az infrastruktúra-korlátozások miatt nem támogatja a tűzfal konfigurációját.
>

## <a name="limitations"></a>Korlátozások

::: zone pivot="container-windows"

- Az Azure Storage App Service jelenleg **nem támogatott** a saját programkód-forgatókönyvek (nem tároló Windows-alkalmazások) használata esetén.
- Az App Service Azure Storage szolgáltatás **nem támogatja** a **tárolási tűzfal** konfigurálását az infrastruktúra korlátai miatt.
- Az Azure Storage App Service lehetővé teszi, **hogy egy alkalmazás legfeljebb öt** csatlakoztatási pontot határozzon meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage App Service FTP-/FTPs-végpontokon keresztül nem érhető el. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használata.

::: zone-end

::: zone pivot="container-linux"

- Az Azure Storage App Service támogatja a **Azure Files tárolók** (írási/olvasási) és az **Azure Blob-tárolók** csatlakoztatását (csak olvasható)
- App Service Azure Storage-ban **legfeljebb öt** csatlakozási pontot adhat meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage App Service FTP-/FTPs-végpontokon keresztül nem érhető el. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használata.

::: zone-end

## <a name="link-storage-to-your-app"></a>Tároló csatolása az alkalmazáshoz

::: zone pivot="container-windows"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a könyvtárat](#prerequisites), mostantól konfigurálhatja az alkalmazást az Azure Storage használatával.

Ha Azure Files-megosztást szeretne csatlakoztatni egy címtárhoz a App Service alkalmazásban, használja az [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) parancsot. A tárolási típusnak AzureFiles kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Ezt minden olyan könyvtárhoz el kell végeznie, amelyet Azure Files-megosztáshoz szeretne csatolni.

::: zone-end

::: zone pivot="container-linux"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a könyvtárat](#prerequisites), mostantól konfigurálhatja az alkalmazást az Azure Storage használatával.

Ha Storage-fiókot szeretne csatlakoztatni a App Service alkalmazás egyik könyvtárába, használja az [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) parancsot. A tárolási típus lehet AzureBlob vagy AzureFiles. Ebben a példában a AzureFiles van használatban. A csatlakoztatási útvonal beállítása az Azure Storage-ba csatlakoztatni kívánt tárolóban található mappának felel meg. Ha a (z) "/" értékre állítja, a teljes tárolót az Azure Storage-ba csatlakoztatja.


> [!CAUTION]
> A webalkalmazásban a csatlakoztatási útvonalként megadott könyvtárnak üresnek kell lennie. A címtárban tárolt bármely tartalmat egy külső csatlakoztatás hozzáadásakor törli a rendszer. Ha egy meglévő alkalmazás fájljainak áttelepítését végzi, a Kezdés előtt készítsen biztonsági másolatot az alkalmazásról és annak tartalmáról.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Ezt minden olyan címtárhoz el kell végeznie, amelyet hozzá szeretne kapcsolni egy Storage-fiókhoz.

::: zone-end

## <a name="verify-linked-storage"></a>Csatolt tár ellenőrzése

Ha a megosztás az alkalmazáshoz van társítva, akkor a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Következő lépések

::: zone pivot="container-windows"

- [Egyéni szoftver Átmigrálása Azure app Service egy egyéni tároló használatával](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Egyéni tároló konfigurálása](configure-custom-container.md?pivots=platform-linux).

::: zone-end