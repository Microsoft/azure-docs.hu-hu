---
title: Parancsfájl mintája – a fájlmegosztás helyreállítható törlésének letiltása
description: Megtudhatja, hogyan lehet letiltani egy parancsfájlt a fájlmegosztás helyreállítható törlésével egy Storage-fiókban.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84122795"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>A fájlmegosztás törlésének letiltása egy Storage-fiókban

Ebből a dokumentumból megtudhatja, hogyan tilthatja le a fájlmegosztás helyreállítható törlését egy Storage-fiókban.

Kövesse az alábbi lépéseket:

1. Telepítse a armclient. Ha szeretné megtudni, hogyan telepítheti, látogasson el [erre a hivatkozásra](https://github.com/projectkudu/ARMClient).

2. Mentse a következő két kérelem törzsét a számítógép egyik mappájába.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Tartsa kéznél a Storage-fiók Azure Resource Manager (ARM) AZONOSÍTÓját. Például: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Jelentkezzen be a hitelesítő adataival a **armclient-bejelentkezés** futtatásával.

5. Szerezze be a fájlmegosztás aktuális törlési tulajdonságait a Storage-fiókban.

    A következő beolvasási művelet lekéri a *inquirytest* -fiókban lévő fájlmegosztás törlési tulajdonságait:

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. A Storage-fiókban lévő fájlmegosztás helyreállítható törlésének letiltása.

    A következő PUT művelet letiltja a *inquirytest* -fiókban lévő fájlmegosztás törlési tulajdonságait:

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Ha újra engedélyezni szeretné a Soft delete használatát, használja a következő mintát.

    A következő PUT művelettel engedélyezheti a fájlmegosztás Soft delete tulajdonságát a "inquirytest" fiókban.

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
