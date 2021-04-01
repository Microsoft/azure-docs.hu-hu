---
title: Egyéni mintavétel létrehozása a klasszikus üzembe helyezési modell használatával – Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt a Application Gatewayhoz a PowerShell használatával a klasszikus üzemi modellben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397858"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Egyéni mintavétel létrehozása az Azure Application Gateway (klasszikus) szolgáltatáshoz a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni mintavételt ad hozzá egy meglévő Application gatewayhez a PowerShell használatával. Az egyéni mintavételek olyan alkalmazások esetében hasznosak, amelyek adott állapot-ellenőrzési oldallal rendelkeznek, illetve olyan alkalmazásokhoz, amelyek nem adnak választ az alapértelmezett webalkalmazásra.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, [hogyan hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Application Gateway-erőforrás létrehozása egyéni mintavételsel

Az átjáró létrehozásához használja a `New-AzureApplicationGateway` parancsmagot, és cserélje le az értékeket a saját értékeire. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

Az alábbi példa egy új Application Gateway-t hoz létre egy „testvnet1” nevű virtuális hálózat és egy „subnet-1” nevű alhálózat használatával.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Az átjáró létrehozásának ellenőrzéséhez használhatja a `Get-AzureApplicationGateway` parancsmagot.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A kis, közepes és nagy méretűek közül választhat.
> 
> 

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezek az értékek akkor jönnek létre, amikor az átjáró fut állapotban van.

### <a name="configure-an-application-gateway-by-using-xml"></a>Application Gateway konfigurálása XML használatával

Az alábbi példában egy XML-fájllal konfigurálja az Application Gateway beállításait, és véglegesíti őket az Application Gateway-erőforráshoz.  

Másolja az alábbi szöveget a Jegyzettömbbe.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Szerkessze a zárójelek közötti értékeket a konfigurációs elemeknek megfelelően. Mentse a fájlt .xml kiterjesztéssel.

Az alábbi példa azt szemlélteti, hogyan használható konfigurációs fájl az Application Gateway beállításához a HTTP-forgalom terheléselosztásához a 80-es nyilvános porton, valamint a hálózati forgalom továbbítása a 80-es háttér-porthoz két IP-cím között egy egyéni mintavétel használatával.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.

A rendszer új konfigurációs elemeket ad \<Probe\> hozzá az egyéni mintavételek konfigurálásához.

A konfigurációs paraméterek a következők:

|Paraméter|Leírás|
|---|---|
|**Név** |Az egyéni mintavétel hivatkozási neve. |
| **Protokoll** | Használt protokoll (a lehetséges értékek HTTP vagy HTTPS).|
| **Gazdagép** és **elérési út** | Teljes URL-cím elérési útja, amelyet az Application Gateway hív meg a példány állapotának meghatározásához. Ha például van egy webhely http: \/ /contoso.com/, akkor az egyéni mintavétel konfigurálható a "http: \/ /contoso.com/Path/custompath.htm" értékre a mintavételi ellenőrzésekhez, hogy sikeres legyen a http-válasz.|
| **Intervallum** | A mintavételi időköz ellenőrzésének beállítása másodpercben.|
| **Időtúllépés** | Meghatározza a mintavétel időtúllépését a HTTP-válaszok esetében.|
| **UnhealthyThreshold** | A háttérbeli példány *sérültként* való megjelöléséhez szükséges sikertelen http-válaszok száma.|

A mintavétel neve a konfigurációban hivatkozik \<BackendHttpSettings\> arra, hogy melyik háttér-készlet használja az egyéni mintavételi beállításokat.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Egyéni mintavétel hozzáadása meglévő Application Gateway-átjáróhoz

Az Application Gateway aktuális konfigurációjának módosításához három lépés szükséges: az aktuális XML-konfigurációs fájl beolvasása, a módosítás, hogy egyéni mintavételt végezzen, és konfigurálja az Application Gatewayt az új XML-beállításokkal.

1. Az XML-fájl beolvasása a használatával `Get-AzureApplicationGatewayConfig` . Ez a parancsmag exportálja a konfigurációs XML-t, hogy a rendszer módosítsa a mintavételi beállításokat.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Nyissa meg az XML-fájlt egy szövegszerkesztőben. Adjon hozzá egy szakaszt a következő `<probe>` után: `<frontendport>` .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   Az XML backendHttpSettings szakaszában adja hozzá a mintavétel nevét az alábbi példában látható módon:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Mentse az XML-fájlt.

1. Frissítse az Application Gateway konfigurációját az új XML-fájllal a használatával `Set-AzureApplicationGatewayConfig` . Ez a parancsmag frissíti az Application Gatewayt az új konfigurációval.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Következő lépések

Ha Transport Layer Security (TLS), korábbi nevén SSL (SSL) kiszervezést szeretne konfigurálni, tekintse meg [az Application Gateway konfigurálása TLS-alapú kiszervezéshez](./tutorial-ssl-powershell.md)című témakört.

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](./application-gateway-ilb-arm.md).