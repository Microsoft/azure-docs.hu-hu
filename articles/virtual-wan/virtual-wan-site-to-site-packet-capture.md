---
title: 'Oktatóanyag: Csomagrögzítés Azure Virtual WAN kapcsolatokon'
description: Ebből az oktatóanyagból megtudhatja, hogyan végezhet csomagrögzítést Virtual WAN a VPN Gateway.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879155"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Csomagrögzítés végrehajtása a Azure Virtual WAN-hely VPN Gateway 

A kapcsolattal és a teljesítménnyel kapcsolatos problémák gyakran összetettek. A probléma okának leszűkítése jelentős időt és erőfeszítést igényel. A csomagrögzítés segítségével leszűkítheti a probléma hatókörét a hálózat bizonyos részeire. Segítségével megállapíthatja, hogy a probléma a hálózat ügyféloldalán, a hálózat Azure-oldalán vagy valahol a kettő között van-e. Miután leszűkítette a problémát, hatékonyabb a hibakeresés és a javítási művelet.

A következő cikk bemutatja, hogyan indíthatja el és állíthatja le a csomagrögzítést a Azure Virtual WAN a VPN Gateway. Ez a funkció jelenleg csak a PowerShellen keresztül érhető el.

## <a name="prerequisites"></a>Előfeltételek

A cikkben található lépések használhatja, ha a következő konfiguráció már be van állítva a környezetben:

* Egy Virtual WAN, egy virtuális központ és egy VPN Gateway virtuális központban üzembe helyezett virtuális központ. Előfordulhat, hogy VPN-helyeket is csatlakoztat a helyek közötti kapcsolathoz, VPN Gateway.


### <a name="working-with-azure-powershell"></a>A Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>A környezet beállítása

Az alábbi szkript PowerShellben való futtatásával győződjön meg arról, hogy a megfelelő előfizetési környezetben van. Ez biztosítja, hogy olyan felhasználóhoz van bejelentkezve, aki rendelkezik a csomagrögzítés végrehajtásához szükséges engedélyekkel a VPN Gateway.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Tárfiók létrehozása

A csomagrögzítés eredményeinek tárolásához létre kell hoznia egy tárfiókot az Azure-előfizetésében. A [tárfiókok létrehozásához](.././storage/common/storage-account-create.md) tekintse meg ezt a dokumentumot.

A fiók létrehozása után futtassa az alábbi parancsokat egy közös hozzáférésű jogosultság jogosultsága (SAS) URL-címének létrehozásához. A csomagrögzítés eredményeit a rendszer ezen az URL-címen tárolja.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>A csomagrögzítés kezdete

A csomagrögzítést kétféle lehetőséggel kezdheti meg: csomagokat rögzíthet egyetlen VPN-kapcsolaton vagy a teljes, VPN Gateway. Vegye figyelembe, hogy ha a VPN-kapcsolatok rögzítését választotta, akkor egyszerre csak 6 kapcsolaton hajthat végre rögzítéseket.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Csomagrögzítés egy hely–hely kapcsolaton VPN Gateway (minden kapcsolat)

Futtassa az alábbi parancsokat. A Hely–hely kapcsolat neve VPN Gateway a virtuális központra való kattintással kattintson a VPN (hely–hely) elemre a Kapcsolatok alatt.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Az átjáró Virtual WAN képe." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Csomagrögzítés adott hely–hely VPN-kapcsolatokon

>[!NOTE]
> Vegye figyelembe, hogy egyidejűleg csak 5 VPN-kapcsolaton futtathat csomagrögzítést.


Futtassa az alábbi parancsokat. A hely–hely VPN-kapcsolatok neve úgy található, hogy megnyitja a virtuális központot, és a Kapcsolatok alatt a VPN (hely–hely) elemre kattint. Ezután lépjen arra a VPN-webhelyre, amelyről csomagrögzítést szeretne végezni, majd kattintson a jobb oldalon található három pontra. A **felugró menüben** kattintson a VPN-kapcsolat szerkesztése elemre.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="A VPN-kapcsolatnevek keresését képe." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Az adott VPN-helyhez csatlakoztatott hivatkozások neve az előző szakaszban található VPN-webhelyre kattintva és a Hivatkozások **táblázatban található.** 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="A VPN-hivatkozás nevének megkeresét mutató kép." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Nem kötelező: Szűrők megadása

Íme néhány gyakran elérhető csomagrögzítő eszköz. A megfelelő csomagrögzítések ilyen eszközökkel való rögzítése nehézkes lehet, különösen nagy forgalmú forgatókönyvekben. A csomagrögzítés egyszerűsítése érdekében szűrőket is megadhat a csomagrögzítéshez, hogy az adott viselkedésre összpontosítson. Az elérhető paraméterek a következők:

>[!NOTE]
> A TracingFlags és a TCPFlags esetében több protokollt is megadhat a rögzíteni kívánt protokollok numerikus értékeinek összeadásával (ugyanaz, mint a logikai VAGY). Ha például csak az ESP- és OPVN-csomagokat szeretné rögzíteni, a TracingFlag értéke 8+1 = 9.  

| Paraméter | Leírás | Alapértelmezett értékek | Elérhető értékek|
|--- |--- | --- | ---|
| NyomkövetésiFlags | Egész szám, amely meghatározza a rögzített csomagtípusokat | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Egész szám, amely meghatározza a rögzített TCP-csomagtípusokat | 0 (nincs) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|A rögzített csomagok maximális mérete bájtban. A csomagok csonkolódnak, ha nagyobbak a megadott értéknél. |120|Bármelyik|
| MaxFileSize (Maximális fájl mérete) |Maximális rögzítési fájlméret MB-ban. A rögzítések körkörös pufferben vannak tárolva, így a túlcsordulás FIFO-ként lesz kezelve (először a régebbi csomagokat távolítják el)|100|Bármelyik|
|SourceSubnets (Forrás-alhálózatok)|A rendszer rögzíti a megadott CIDR-tartományokból származó csomagokat. Tömbként van megadva.|[] (minden IPv4-cím)|Vesszővel elválasztott IPV4-alhálózatok tömbje|
| DestinationSubnets (DestinationSubnets) |A rendszer a megadott CIDR-tartományokba küldött csomagokat rögzíti. Tömbként van megadva. |[] (minden IPv4-cím)|Vesszővel elválasztott IPV4-alhálózatok tömbje|
|SourcePort (Forrásport) |A rendszer a megadott tartományokban található forráscsomagokat rögzíti. Tömbként van megadva.|[] (minden port)|Vesszővel elválasztott portok tömbje|
|DestinationPort |A megadott tartományokban célcsomagok vannak rögzítik. Tömbként van megadva.|[] (minden port)|Vesszővel elválasztott portok tömbje|
| CaptureSingleDirectionTrafficOnly |Ha igaz, a csomagrögzítésben csak a kétirányú folyamat egyetlen iránya fog ni. Ez rögzíti az IP-címek és portok összes lehetséges kombinációját.|Igaz|Igaz, Hamis|
|Protokoll|Az IANA-protokolloknak megfelelő egész számok tömbje. |[] (minden protokoll)| Az itt található [protokollok](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Az alábbiakban egy példa látható a csomagrögzítésre szűrősring használatával. Az előző táblázat alapján igény szerint módosíthatja a paramétereket.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>A csomagrögzítés leállítása
Javasoljuk, hogy legalább 600 másodpercig hagyja futni a csomagrögzítést. Az útvonal több összetevője közötti szinkronizálási problémák miatt előfordulhat, hogy a rövidebb csomagrögzítések nem biztosítanak teljes adatokat. Ha készen áll a csomagrögzítés leállítására, futtassa a következő parancsot.

A paraméterek hasonlóak a Csomagrögzítés indítása szakaszban találhatóakhoz. Az SAS URL-cím a Tárfiók létrehozása [szakaszban lett létrehozva.](#createstorage)

### <a name="gateway-level-packet-capture"></a>Átjárószintű csomagrögzítés

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Kapcsolatszintű csomagrögzítések

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>A csomagrögzítés megtekintése

A Azure Portal keresse meg a "Tárfiók létrehozása" menüben létrehozott tárfiókot. Kattintson a tárolóra, és töltse le a fájlt. A csomagrögzítési adatfájlok PCAP formátumban jönnek létre. PCAP-fájlokat a Wireshark vagy más gyakran elérhető alkalmazás használatával nyithat meg.

## <a name="next-steps"></a>Következő lépések

A további tudnivalókért lásd: Virtual WAN:

> [!div class="nextstepaction"]
> * [Virtuális WAN – GYIK](virtual-wan-faq.md)