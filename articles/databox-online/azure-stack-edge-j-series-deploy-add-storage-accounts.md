---
title: Oktatóanyag az adatok Storage-fiókba való átviteléhez Azure Stack Edge Pro GPU-val | Microsoft Docs
description: Ismerje meg, hogyan vehet fel és csatlakozhat helyi és Edge Storage-fiókokhoz Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 970c8a9a906ec542bfc37805abbe3cb67ac10202
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442230"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Oktatóanyag: adatok átvitele a Storage-fiókok használatával Azure Stack Edge Pro GPU-val 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez az oktatóanyag azt ismerteti, hogyan lehet hozzáadni és csatlakozni a Storage-fiókokhoz az Azure Stack Edge Pro-eszközön. A Storage-fiókok hozzáadása után Azure Stack Edge Pro átviheti az adatátvitelt az Azure-ba.

Az eljárás végrehajtása körülbelül 30 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Storage-fiók hozzáadása
> * Kapcsolódás a Storage-fiókhoz

 
## <a name="prerequisites"></a>Előfeltételek

A Storage-fiókok Azure Stack Edge Pro-hoz való hozzáadása előtt győződjön meg a következőket:

- Telepítette a fizikai eszközt a [Azure stack Edge Pro telepítése](azure-stack-edge-gpu-deploy-install.md)című témakörben leírtak szerint.

- A fizikai eszközt aktiválta a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.


## <a name="add-an-edge-storage-account"></a>Edge Storage-fiók hozzáadása

Edge Storage-fiók létrehozásához hajtsa végre a következő eljárást:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Kapcsolódás az Edge Storage-fiókhoz

Mostantól *http* -vagy *https*-kapcsolaton keresztül is CSATLAKOZHAT az Edge Storage REST API-khoz.

- A *https* a biztonságos és ajánlott módszer.
- A *http* -t megbízható hálózatokhoz való csatlakozáskor használja a rendszer.

## <a name="connect-via-http"></a>Kapcsolat http-n keresztül

A Edge Storage REST API-k http-n keresztüli kapcsolatához a következő lépések szükségesek:

- Az Azure konzisztens szolgáltatás virtuális IP-címének és blob szolgáltatásbeli végpontjának hozzáadása a távoli gazdagéphez
- A kapcsolat ellenőrzése 

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Eszköz IP-címének és blob szolgáltatásbeli végpontjának hozzáadása a távoli ügyfélhez

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Kapcsolat ellenőrzése

A kapcsolódás ellenőrzéséhez általában az alábbi információkra lesz szüksége (amelyek az előző lépésben összegyűjtve változhatnak):

- A Storage-fiók neve.
- A Storage-fiók elérési kulcsa.
- Blob service végpont.

Már rendelkezik a Storage-fiók nevével és a blob Service-végponttal. A Storage-fiók elérési kulcsát úgy érheti el, ha a Azure Resource Manager Azure PowerShell ügyfél használatával csatlakozik az eszközhöz.

Kövesse a [Kapcsolódás az eszközhöz a Azure Resource Manager használatával](azure-stack-edge-j-series-connect-resource-manager.md)című témakör lépéseit. Miután bejelentkezett a helyi eszköz API-khoz a Azure Resource Manager keresztül, szerezze be a Storage-fiókok listáját az eszközön. Futtassa a következő parancsmagot:

`Get-AzureRMStorageAccount`

Az eszközön található Storage-fiókok listájából válassza ki azt a Storage-fiókot, amelyhez hozzáférési kulcsra van szüksége. Jegyezze fel a Storage-fiók nevét és az erőforráscsoportot.

Alább látható egy minta kimenet:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

A hozzáférési kulcs lekéréséhez futtassa a következő parancsmagot:

`Get-AzureRmStorageAccountAccessKey`

Alább látható egy minta kimenet:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Másolja ki és mentse a kulcsot. Ezt a kulcsot fogja használni a Azure Storage Explorer használatával történő kapcsolódás ellenőrzéséhez.

A kapcsolódás sikeres létrejöttének ellenőrzéséhez használja a Storage Explorer a külső Storage-fiókhoz való csatoláshoz. Ha nincs Storage Explorer, [töltse le Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Csatlakozási HTTPS-kapcsolaton keresztül

Az Azure Blob Storage REST API-khoz HTTPS-kapcsolaton keresztül történő kapcsolódáshoz a következő lépések szükségesek:

- A blob Endpoint-tanúsítvány beszerzése
- A tanúsítvány importálása az ügyfélen vagy a távoli gazdagépen
- Az eszköz IP-címe és a blob szolgáltatás végpontjának hozzáadása az ügyfélhez vagy a távoli gazdagéphez
- A kapcsolatok konfigurálása és ellenőrzése

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

### <a name="get-certificate"></a>Tanúsítvány beolvasása

A blob Storage HTTPS-kapcsolaton keresztüli eléréséhez SSL-tanúsítvány szükséges az eszközhöz. Ezt a tanúsítványt a Azure Stack Edge Pro-eszközre is fel kell töltenie *. pfx* -fájlként, amelyhez titkos kulccsal csatolva van. További információ a létrehozásáról (csak tesztelési és fejlesztési célokra) és a tanúsítványok Azure Stack Edge Pro-eszközre való feltöltéséhez keresse fel a következőt:

- [Hozza létre a blob-végpont tanúsítványát](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Töltse fel a blob-végpont tanúsítványát](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Tanúsítványok importálása az eszközhöz hozzáférő ügyfélen](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Tanúsítvány importálása

Ha Azure Storage Explorer használatával csatlakozik a Storage-fiókokhoz az eszközön, akkor a tanúsítványt is importálnia kell a PEM formátumban Storage Explorerba. Windows-környezetben a Base-64 kódolású *. cer* megegyezik a PEM formátumával.

A következő lépésekkel importálhatja a tanúsítványokat a Azure Storage Explorer:

1. Győződjön meg arról, hogy Azure Storage Explorer a Azure Stack API-kat célozza meg. Lépjen a **> cél Azure stack API**-k szerkesztése menüpontra. Ha a rendszer kéri, indítsa újra Storage Explorer a módosítás érvénybe léptetéséhez.

2. Az SSL-tanúsítványok importálásához lépjen az **> SSL-tanúsítványok szerkesztése > tanúsítványok importálása** elemre.

  
   ![Tanúsítvány importálása Storage Explorerba](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Navigáljon és adja meg az aláíró láncot és a blob-tanúsítványokat. Az aláíró láncnak és a blob tanúsítványnak PEM formátumúnak kell lennie, amely a Windows rendszer Base64 kódolású formátuma. A rendszer értesítést küld arról, hogy a tanúsítványok importálása sikeres volt.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Eszköz IP-címének és blob szolgáltatásbeli végpontjának hozzáadása

[Ha *http*-kapcsolaton keresztül csatlakozik, kövesse ugyanezen lépéseket az eszköz IP-címének és a blob-szolgáltatás végpontjának hozzáadásához](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>A kapcsolatok konfigurálása és ellenőrzése

A HTTP protokollon [keresztüli kapcsolódás során használt kapcsolat konfigurálásához és ellenőrzéséhez ](#verify-connection)kövesse az alábbi lépéseket. Az egyetlen különbség, hogy a *http használata beállítás* nincs bejelölve.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következő Azure Stack Edge Pro-témaköröket:

> [!div class="checklist"]
> * Storage-fiók hozzáadása
> * Csatlakozás egy tárfiókhoz

Ha szeretné megtudni, hogyan alakíthatja át adatait Azure Stack Edge Pro használatával, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az adatátalakítás Azure Stack Edge Pro-val](./azure-stack-edge-j-series-deploy-configure-compute.md)


