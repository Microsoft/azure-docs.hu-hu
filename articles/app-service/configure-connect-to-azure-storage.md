---
title: Azure Storage hozzáadása (tároló)
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást egy tárolóba Azure App Service. Fájlok megosztása alkalmazások között, statikus tartalom távoli kezelése és helyi hozzáférés stb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777616"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Az Azure Storage (előzetes verzió) elérése hálózati megosztásként az App Service-ben található tárolóból

::: zone pivot="container-windows"

Ez az útmutató bemutatja, hogyan csatolhatja az Azure Storage Filest hálózati megosztásként egy Windows-tárolóhoz a App Service. Csak [Azure Files- és](../storage/files/storage-how-to-use-files-cli.md) [Prémium-fájlmegosztások](../storage/files/storage-how-to-create-file-share.md) támogatottak. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, a több alkalmazáshoz való hozzáférés és a többféle átviteli módszer.

> [!NOTE]
>Az Azure Storage App Service előzetes **verzióban érhető** **el, éles** **környezetben azonban nem támogatott.**

::: zone-end

::: zone pivot="container-linux"

Ez az útmutató bemutatja, hogyan csatolhatja az Azure Storage-et egy Linux-App Service. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a többféle átviteli módszer.

> [!NOTE]
>Az Azure Storage App Service **előzetes verzióban érhető** el a App Service on Linux és Web App for Containers. Éles **forgatókönyvekben** nem **támogatott.**

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="container-windows"

- [Egy meglévő Windows-tárolóalkalmazás a Azure App Service](quickstart-custom-container.md)
- [Azure-fájlmegosztás létrehozása](../storage/files/storage-how-to-use-files-cli.md)
- [Fájlok feltöltése az Azure-fájlmegosztásba](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Egy meglévő [App Service on Linux alkalmazás.](index.yml)
- [Egy Azure Storage-fiók](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Egy Azure-fájlmegosztás és könyvtár.](../storage/files/storage-how-to-use-files-cli.md)

::: zone-end

> [!NOTE]
> Azure Files nem alapértelmezett tároló, és külön kell fizetni, a webalkalmazás nem tartalmazza. Az infrastruktúra korlátozásai miatt nem támogatja a tűzfal-konfigurációk használata.
>

## <a name="limitations"></a>Korlátozások

::: zone pivot="container-windows"

- Az Azure Storage App Service a  saját kód használata esetén (nem tárolóba telepített Windows-alkalmazások esetében) jelenleg nem támogatott.
- Az Azure Storage App Service nem **támogatja** a **Storage-tűzfal** konfigurációjának az infrastruktúra korlátozásai miatt való használatát.
- Az Azure Storage App Service alkalmazásonként **legfeljebb öt** csatlakoztatási pont megadását teszi lehetővé.
- Az alkalmazáshoz csatlakoztatott Azure Storage nem érhető el az FTP App Service/FTPs végponton keresztül. Használja a [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

::: zone-end

::: zone pivot="container-linux"

- Az Azure Storage App Service támogatja a **Azure Files (olvasás/írás)** és az **Azure Blob-tárolók** (csak olvasható) csatlakoztatását
- Az Azure Storage App Service alkalmazásonként **legfeljebb öt** csatlakoztatási pont megadását teszi lehetővé.
- Az alkalmazáshoz csatlakoztatott Azure Storage nem érhető el az FTP App Service/FTPs végponton keresztül. Használja a [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

::: zone-end

## <a name="link-storage-to-your-app"></a>Tároló csatolása az alkalmazáshoz

::: zone pivot="container-windows"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást](#prerequisites)és a könyvtárat, konfigurálhatja az alkalmazást az Azure Storage-ban.

Ha csatlakoztatni Azure Files megosztást egy könyvtárhoz a App Service alkalmazásban, használja az [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) parancsot. A tárolótípusnak AzureFiles típusúnak kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Ezt minden más olyan könyvtárhoz érdemes megtennie, amely egy új megosztáshoz Azure Files kapcsolódik.

::: zone-end

::: zone pivot="container-linux"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást](#prerequisites)és a könyvtárat, konfigurálhatja az alkalmazást az Azure Storage-ban.

Ha tárfiókot csatlakoztat egy címtárhoz a App Service alkalmazásában, használja az [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) parancsot. A Tároló típusa lehet AzureBlob vagy AzureFiles. Ebben a példában az AzureFiles-t használjuk. A csatlakoztatási útvonal beállítása megegyezik azzal a mappával, amely az Azure Storage-hoz csatlakoztatni kívánt tárolón belül található. Ha a beállításnál a "/" érték van meg, a teljes tároló csatlakoztatva lesz az Azure Storage-hoz.


> [!CAUTION]
> A webalkalmazás csatlakoztatási útvonalaként megadott könyvtárnak üresnek kell lennie. Az ebben a könyvtárban tárolt tartalmak egy külső csatlakoztatás hozzáadásakor törlődnek. Ha egy meglévő alkalmazás fájljait milikál, a kezdés előtt készítse el az alkalmazás és a tartalom biztonsági másolatát.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Ezt minden olyan könyvtárhoz érdemes megtennie, amely egy tárfiókhoz van csatolva.

::: zone-end

## <a name="verify-linked-storage"></a>Csatolt tároló ellenőrzése

Miután a megosztást az alkalmazáshoz kapcsolta, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Következő lépések

::: zone pivot="container-windows"

- [Egyéni szoftver áttelepítése Azure App Service egyéni tároló használatával.](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Konfigurál egy egyéni tárolót.](configure-custom-container.md?pivots=platform-linux)

::: zone-end