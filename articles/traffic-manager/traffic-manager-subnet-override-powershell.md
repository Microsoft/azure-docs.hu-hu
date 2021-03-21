---
title: Azure Traffic Manager alhálózat felülbírálása Azure PowerShell használatával | Microsoft Docs
description: Ez a cikk segít megérteni, hogy Traffic Manager alhálózat felülbírálásával hogyan bírálhatja felül a Traffic Manager-profilok útválasztási módszerét, hogy a végfelhasználói IP-cím alapján, előre meghatározott IP-címen keresztül irányítsa át a forgalmat a végpont-hozzárendelésekre Azure PowerShell használatával.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504780"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Alhálózat Traffic Manager felülbírálása az Azure PowerShell-lel

Traffic Manager alhálózat felülbírálása lehetővé teszi egy profil útválasztási módszerének módosítását.  A felülbírálás hozzáadásával a rendszer a végfelhasználó IP-címe alapján, előre meghatározott IP-tartománnyal továbbítja a forgalmat a végpont-hozzárendeléshez. 

## <a name="how-subnet-override-works"></a>Az alhálózat felülbírálásának működése

Ha az alhálózat felülbírálásait egy Traffic Manager-profilhoz adja, Traffic Manager először ellenőrizze, hogy van-e alhálózat felülbírálva a végfelhasználó IP-címéhez. Ha az egyik található, a rendszer a felhasználó DNS-lekérdezését a megfelelő végpontra irányítja.  Ha nem talál leképezést, Traffic Manager a profil eredeti útválasztási metódusára kerül vissza. 

Az IP-címtartományok megadhatók CIDR-tartományként (például 1.2.3.0/24) vagy címtartományként (például 1.2.3.4-5.6.7.8). Az egyes végpontokhoz társított IP-tartományoknak egyedinek kell lenniük a végpont számára. Az IP-címtartományok különböző végpontok közötti átfedése miatt a Traffic Manager elutasítja a profilt.

Az alhálózatok felülbírálásait két típusú útválasztási profil támogatja:

* **Földrajzi** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, a lekérdezés a végpont állapotának megfelelő állapotba irányítja a lekérdezést.
* **Teljesítmény** – ha Traffic Manager megkeresi a DNS-lekérdezés IP-címéhez tartozó alhálózat felülbírálását, akkor a rendszer csak akkor irányítja át a forgalmat a végponthoz, ha az kifogástalan állapotú.  Ha az alhálózat felülbírálási végpontja nem kifogástalan állapotú, akkor a Traffic Manager vissza fog térni a teljesítmény-útválasztási heurisztikus művelethez.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager alhálózat felülbírálásának létrehozása

Traffic Manager alhálózat felülbírálásának létrehozásához használhatja a Azure PowerShellt a felülbíráláshoz tartozó alhálózatok Traffic Manager végponthoz való hozzáadásához.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0-ra vagy az újabb verzióra. A futtatásával `Get-Module -ListAvailable Az` megkeresheti a telepített verziót. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, az Azure-ba való bejelentkezéshez is futtatnia kell `Login-AzAccount` .


1. **Traffic Manager végpont beolvasása:**

    Az alhálózat felülbírálásának engedélyezéséhez kérje le azt a végpontot, amelyhez hozzá szeretné adni a felülbírálást, és tárolja azt egy változóban a [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)használatával.

    Cserélje le a nevet, a profilnév és a ResourceGroupName értéket a módosítani kívánt végpont értékeire.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adja hozzá az IP-címtartományt a végponthoz:**
    
    Ha az IP-címtartományt hozzá szeretné adni a végponthoz, a [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange) használatával adja hozzá a tartományt.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    A tartományok hozzáadása után a [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) használatával frissítse a végpontot.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Az IP-címtartomány eltávolítását a [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange)használatával végezheti el.

1.  **Traffic Manager végpont beolvasása:**

    Az alhálózat felülbírálásának engedélyezéséhez kérje le azt a végpontot, amelyhez hozzá szeretné adni a felülbírálást, és tárolja azt egy változóban a [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)használatával.

    Cserélje le a nevet, a profilnév és a ResourceGroupName értéket a módosítani kívánt végpont értékeire.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Távolítsa el az IP-címtartományt a végpontról:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     A tartományok eltávolítása után a [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) használatával frissítse a végpontot.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Következő lépések
További információ a Traffic Manager [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).

Tudnivalók az [alhálózat forgalmáról – útválasztási módszer](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)