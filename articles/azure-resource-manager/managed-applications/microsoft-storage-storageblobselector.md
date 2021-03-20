---
title: StorageBlobSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Storage. StorageBlobSelector felhasználói felületi elemét ismerteti.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92754534"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft. Storage. StorageBlobSelector FELHASZNÁLÓIFELÜLET-elem

A Blobok Azure Storage-fiókból való kiválasztásának vezérlése.

## <a name="ui-sample"></a>Felhasználói felület mintája

A felhasználó a rendelkezésre álló Storage-Blobok tallózására szolgáló lehetőséggel jelenik meg.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. Storage. StorageBlobSelector – Tallózás":::

A **Tallózás** lehetőség kiválasztása után a felhasználó kiválaszthatja a Storage-fiókot.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. Storage. StorageBlobSelector – tár kiválasztása":::

A felhasználó látja a tárolókat a Storage-fiókban, és kijelölhet egyet.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. Storage. StorageBlobSelector – tároló kiválasztása":::

A tárolóból a felhasználó kijelölhet egy fájlt.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. Storage. StorageBlobSelector – fájl":::

A vezérlő frissült a kiválasztott fájl nevének megjelenítéséhez.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. Storage. StorageBlobSelector – fájl kiválasztása":::

## <a name="schema"></a>Séma

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Megjegyzések

A **megkötések. allowedFileExtensions** tulajdonság határozza meg az engedélyezett fájltípusokat.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
