---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90606584"
---
Az üzenetsor a [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) vagy a  [Microsoft Azure Storage Explorerban](https://storageexplorer.com/)tekinthető meg. Az üzenetsor az Azure CLI-ben is megtekinthető az alábbi lépések szerint:

1. Nyissa meg a Function Project *local.setting.js* fájlját, és másolja a kapcsolatok karakterláncának értékét. Egy terminál-vagy parancssori ablakban futtassa a következő parancsot egy nevű környezeti változó létrehozásához, és illessze be az `AZURE_STORAGE_CONNECTION_STRING` adott kapcsolódási karakterláncot a helyére  `<MY_CONNECTION_STRING>` . (Ez a környezeti változó azt jelenti, hogy nem kell megadnia a kapcsolódási karakterláncot minden további parancshoz az `--connection-string` argumentum használatával.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Választható A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs használatával megtekintheti a fiókban található tárolási várólistákat. A parancs kimenetének tartalmaznia kell egy nevű várólistát `outqueue` , amely akkor jött létre, amikor a függvény első üzenetét írta a várólistára.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. A (z [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) ) parancs használatával olvassa el a várólista üzenetét, amely a függvény korábbi teszteléséhez használt keresztnév. A parancs beolvassa és eltávolítja az első üzenetet a várólistából. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ez a szkript a certutil használatával dekódolja a Base64 kódolású üzenetek gyűjteményét egy helyi ideiglenes fájlból. Ha nincs kimenet, próbálja meg eltávolítani `> NUL` a szkriptből a certutil-kimenet letiltásának leállítására, ha hiba történt. 
    
    ---
    
    Mivel az üzenettörzs tárolása [Base64 kódolású](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding), az üzenetet a Megjelenítés előtt dekódolni kell. A végrehajtás után `az storage message get` a rendszer eltávolítja az üzenetet a várólistából. Ha csak egy üzenet szerepel a-ben `outqueue` , akkor nem fog beolvasni egy üzenetet, ha a parancsot Másodszor futtatja, hanem hibaüzenetet kap.
