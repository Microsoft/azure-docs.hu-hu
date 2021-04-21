---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 921127ffdd35007cc3fa2eaaa95cdb3fac8bbe15
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782273"
---
Az üzenetsort megtekintheti a [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) a következő [Microsoft Azure Storage Explorer.](https://storageexplorer.com/) Az üzenetsort az Azure CLI-ban is megtekintheti a következő lépésekben leírtak szerint:

1. Nyissa meg a függvényprojekt *local.setting.jsfájlban,* és másolja a kapcsolati sztring értékét. Egy terminálban vagy parancsablakban futtassa a következő parancsot egy nevű környezeti változó létrehozásához, amely az adott kapcsolati sztringet a helyére `AZURE_STORAGE_CONNECTION_STRING` írja  `<MY_CONNECTION_STRING>` be. (Ez a környezeti változó azt jelenti, hogy a argumentum használatával nem kell minden további parancshoz megszabadni a kapcsolati `--connection-string` sztringet.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
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
    
1. (Nem kötelező) Az [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) paranccsal megtekintheti a fiókjában lévő Storage-üzenetsorokat. A parancs kimenetének tartalmaznia kell egy nevű üzenetsort, amely akkor jött létre, amikor a függvény az első üzenetét ebbe az `outqueue` üzenetsorba írta.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. A paranccsal olvassa be az üzenetet ebből az üzenetsorból, amelynek a függvény korábbi tesztelése során [`az storage message get`](/cli/azure/storage/message#az_storage_message_get) használt első nevének kell lennie. A parancs beolvassa és eltávolítja az első üzenetet az üzenetsorból. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
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

    Ez a szkript a certutil használatával dekódolja a base64 kódolású üzenetgyűjteményt egy helyi ideiglenes fájlból. Ha nincs kimenet, próbálja meg eltávolítani a szkriptet a certutil kimenet letiltásának mellőzése érdekében, ha `> NUL` hiba történik. 
    
    ---
    
    Mivel az üzenet törzse [base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)kódolású, az üzenetet a megjelenítés előtt dekódolni kell. A végrehajtása után `az storage message get` az üzenet el lesz távolítva az üzenetsorból. Ha csak egy üzenet volt a-ban, akkor a parancs második futtatásakor nem fog üzenetet lekérni, hanem `outqueue` hibaüzenetet kap.
