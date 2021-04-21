---
title: Hibaelhárítási útmutató Azure Service Bus | Microsoft Docs
description: Megismerheti a hibaelhárítási tippeket és javaslatokat néhány, az alkalmazás használata során Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786566"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Hibaelhárítási útmutató Azure Service Bus
Ez a cikk hibaelhárítási tippeket és javaslatokat tartalmaz néhány, az alkalmazás használata során esetleg Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Csatlakozási, tanúsítvány- vagy időtúllépési problémák
Az alábbi lépések segíthetnek a *.servicebus.windows.net. 

- Keresse meg vagy [nyissa meg a webhelyet.](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` Segít ellenőrizni, hogy ip-szűréssel, virtuális hálózattal vagy tanúsítványlánccal kapcsolatos problémák vannak-e, amelyek gyakoriak a Java SDK használatakor.

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Példa a hibaüzenetre:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- A következő parancs futtatásával ellenőrizze, hogy blokkolva van-e port a tűzfalon. A használt portok a következőek: 443 (HTTPS), 5671 (AMQP) és 9354 (Net Messaging/SBMP). A használt kódtártól függően a rendszer más portokat is használ. Itt található a mintaparancs, amely ellenőrzi, hogy az 5671-es port blokkolva van-e. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux rendszeren:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Ha időszakos csatlakozási problémák lépnek fel, futtassa a következő parancsot az eldobott csomagok ellenőrzéséhez. Ez a parancs 1 másodpercenként 25 különböző TCP-kapcsolatot próbál létrehozni a szolgáltatással. Ezután ellenőrizheti, hogy ezek közül hány volt sikeres/sikertelen, és láthatja a TCP-kapcsolat késését is. Az eszközt innen `psping` töltheti [le:](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Az egyenértékű parancsokat akkor használhatja, ha más eszközöket használ, például , `tnc` `ping` stb. 
- Szerezzen be egy hálózati nyomkövetést, ha az előző lépések nem segítenek, és elemezze olyan eszközökkel, mint a [Wireshark.](https://www.wireshark.org/) Ha [szükséges Microsoft ügyfélszolgálata](https://support.microsoft.com/) lépjen kapcsolatba a kapcsolatfelvételi e-Microsoft ügyfélszolgálata. 
- A kapcsolatok engedélyezési listához hozzáadni szükséges IP-címek megkeresése: Milyen IP-címeket kell hozzáadnom az [engedélyezési listához?](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-) 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>A szolgáltatásfrissítésekkel/-újraindításokkal kapcsolatos problémák

### <a name="symptoms"></a>Hibajelenségek
- A kérések pillanatnyi szabályozása is előfordulhat.
- Előfordulhat, hogy a bejövő üzenetek/kérések száma csökken.
- A naplófájl hibaüzeneteket tartalmazhat.
- Előfordulhat, hogy az alkalmazások néhány másodpercig nem csatlakoznak a szolgáltatáshoz.

### <a name="cause"></a>Ok
A háttérszolgáltatás frissítései és újraindításai ezeket a problémákat okozhatják az alkalmazásokban.

### <a name="resolution"></a>Feloldás
Ha az alkalmazáskód SDK-t használ, az újrapróbálkozási szabályzat már be van építve és aktív. Az alkalmazás újracsatlakozik anélkül, hogy jelentős hatással lenne az alkalmazásra/munkafolyamatra.

## <a name="unauthorized-access-send-claims-are-required"></a>Jogosulatlan hozzáférés: Jogcímek küldése szükséges

### <a name="symptoms"></a>Hibajelenségek 
Ez a hiba akkor jelenhet meg, amikor egy Service Bus-témakört próbál meg elérni Visual Studio-ről egy helyszíni számítógépen egy felhasználó által hozzárendelt felügyelt identitás küldési engedélyekkel.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Ok
Az identitás nem rendelkezik engedélyekkel a Service Bus eléréséhez. 

### <a name="resolution"></a>Feloldás
A hiba elhárításához telepítse a [Microsoft.Azure.Services.AppAuthentication kódtárat.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  További információ: [Helyi fejlesztés hitelesítése.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Az engedélyek szerepkörökhöz való hozzárendelésével kapcsolatban lásd: Felügyelt identitás hitelesítése az Azure Active Directory hozzáféréséhez Azure Service Bus [erőforrásokhoz.](service-bus-managed-service-identity.md)

## <a name="service-bus-exception-put-token-failed"></a>Service Bus kivétel: A jogkivonat nem sikerült

### <a name="symptoms"></a>Hibajelenségek
Ha több mint 1000 üzenetet próbál küldeni ugyanaz Service Bus kapcsolattal, a következő hibaüzenet jelenik meg: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Ok
A rendszer korlátozza azon jogkivonatok számát, amelyek egy adott kapcsolattal küldenek és fogadnak üzeneteket egy Service Bus névtérhez. Ez 1000. 

### <a name="resolution"></a>Feloldás
Nyisson meg egy új kapcsolatot a Service Bus névtérhez, hogy több üzenetet küldjön.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>A virtuális hálózati szabály PowerShell használatával való hozzáadása sikertelen

### <a name="symptoms"></a>Hibajelenségek
Egyetlen virtuális hálózat két alhálózatát konfigurálta egy virtuális hálózati szabályban. Ha a [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) parancsmaggal próbál eltávolítani egy alhálózatot, az nem távolítja el az alhálózatot a virtuális hálózati szabályból. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Ok
Az Azure Resource Manager megadott azonosító érvénytelen lehet. Ez akkor fordulhat elő, ha a virtuális hálózat egy másik erőforráscsoportban van, mint a Service Bus. Ha nem adja meg explicit módon a virtuális hálózat erőforráscsoportját, a CLI-parancs a virtuális Azure Resource Manager erőforráscsoportjának használatával Service Bus létre. Ezért nem tudja eltávolítani az alhálózatot a hálózati szabályból. 

### <a name="resolution"></a>Feloldás
Adja meg Azure Resource Manager alhálózat teljes azonosítóját, amely tartalmazza a virtuális hálózatot tartalmazó erőforráscsoport nevét. Például:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Azure Resource Manager kivételeket.](service-bus-resource-manager-exceptions.md) A sablonokkal (sablonokkal vagy közvetlen hívásokkal Azure Service Bus Azure Resource Manager létrehozott kivételeket sorolja fel.
- [Üzenetkezelési kivételek.](service-bus-messaging-exceptions.md) A szolgáltatás által a .NET-keretrendszer létrehozott Azure Service Bus.