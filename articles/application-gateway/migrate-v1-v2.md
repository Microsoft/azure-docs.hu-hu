---
title: Migrálás v1-ről v2-re – Azure Application Gateway
description: Ez a cikk bemutatja, hogyan telepítheti át az Azure Application Gateway és a webalkalmazási tűzfalat v1-ről v2-re
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 4757a8237aa6226b78e7c1e79ba50710e31d28e3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594265"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Az Azure Application Gateway és a webalkalmazási tűzfal migrálása v1-ről v2-re

Az [Azure Application Gateway és a webalkalmazási tűzfal (WAF) v2](application-gateway-autoscaling-zone-redundant.md) mostantól elérhető, és további funkciókat kínál, mint például az automatikus skálázás és a rendelkezésre állási zóna redundancia. A meglévő 1-es verziójú átjárók frissítése a 2-es verzióra azonban nem történik meg automatikusan. Ha v1-ről v2-re szeretne áttérni, kövesse a jelen cikkben ismertetett lépéseket.

Az áttelepítés két szakaszból áll:

1. A konfiguráció áttelepíteni
2. Az ügyfél forgalmának migrálása

Ez a cikk a konfiguráció áttelepítését ismerteti. Az ügyfél-forgalom áttelepítése az adott környezettől függően változhat. Bizonyos magas szintű általános javaslatok azonban [megtalálhatók](#migrate-client-traffic).

## <a name="migration-overview"></a>Migrálás áttekintése

Olyan Azure PowerShell-parancsfájl érhető el, amely a következő műveleteket végzi el:

* Létrehoz egy új Standard_v2 vagy WAF_v2 átjárót egy megadott virtuális hálózati alhálózatban.
* Zökkenőmentesen másolja a v1 standard vagy WAF átjáróhoz társított konfigurációt az újonnan létrehozott Standard_V2 vagy WAF_V2 átjáróhoz.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Az új v2-átjáró új nyilvános és magánhálózati IP-címmel rendelkezik. A meglévő v1-átjáróhoz társított IP-címek nem helyezhetők át zökkenőmentesen v2-re. Meglévő (nem lefoglalt) nyilvános vagy magánhálózati IP-címet azonban lefoglalhat az új v2-átjáróra.
* Meg kell adnia egy IP-címtartományt egy másik alhálózat számára a virtuális hálózaton belül, ahol a v1-átjáró található. A parancsfájl nem tudja létrehozni a v2-átjárót minden olyan meglévő alhálózatban, amely már rendelkezik v1-átjáróval. Ha azonban a meglévő alhálózatnak már van v2-átjárója, az továbbra is működik, ha elegendő IP-címtartomány van megadva.
* Ha hálózati biztonsági csoporttal vagy a v2-átjáró alhálózatához társított felhasználói útvonalakkal rendelkezik, győződjön meg arról, hogy betartják a sikeres áttelepítés [NSG-követelményeit](../application-gateway/configuration-infrastructure.md#network-security-groups) és [UDR-követelményeit](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes)
* A [virtuális hálózati szolgáltatás végponti házirendjei](../virtual-network/virtual-network-service-endpoint-policies-overview.md) jelenleg nem támogatottak Application Gateway alhálózatban.
* A TLS/SSL-konfiguráció átadásához meg kell adnia a v1-átjáróban használt összes TLS/SSL-tanúsítványt.
* Ha a v1-átjáró esetében engedélyezve van a FIPS mód, a rendszer nem telepíti át az új v2-átjáróra. Az FIPS mód nem támogatott a v2-ben.
* a v2 nem támogatja az IPv6 protokollt, így az IPv6-kompatibilis v1-átjárók nem települnek át. Ha futtatja a szkriptet, előfordulhat, hogy nem fejeződött be.
* Ha a v1-átjáró csak magánhálózati IP-címmel rendelkezik, a parancsfájl létrehoz egy nyilvános IP-címet és egy magánhálózati IP-címet az új v2-átjáró számára. a v2-átjárók jelenleg csak privát IP-címeket támogatnak.
* A betűket, számokat, kötőjeleket és aláhúzásokat nem tartalmazó fejlécek nem jutnak el az alkalmazáshoz. Ez csak a fejlécek nevére vonatkozik, nem pedig a fejléc értékeire. Ez a v1-ről feltört változás.

## <a name="download-the-script"></a>A parancsfájl letöltése

Töltse le az áttelepítési parancsfájlt a  [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításaitól és beállításaitól függően két lehetőség közül választhat:

* Ha nem rendelkezik az Azure az modulok telepítésével, vagy ne feledje eltávolítani az Azure-t, akkor a legjobb lehetőség a `Install-Script` szkript futtatására szolgáló lehetőség használata.
* Ha meg kell őriznie az Azure az modulokat, a legjobb megoldás, ha letölti a szkriptet, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve van-e az Azure az modulok, futtassa a parancsot `Get-InstalledModule -Name az` . Ha a telepített modulok nem láthatók, akkor használhatja a `Install-Script` metódust.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódus használatával

Ha ezt a beállítást szeretné használni, az Azure az modulokat nem kell telepítenie a számítógépre. Ha telepítve vannak, a következő parancs hibát jelez. Távolítsa el az Azure az modulokat, vagy használja a másik lehetőséget a szkript manuális letöltéséhez és futtatásához.
  
Futtassa a szkriptet a következő paranccsal a legújabb verzió beszerzéséhez:

`Install-Script -Name AzureAppGWMigration -Force`

Ez a parancs telepíti a szükséges az modulokat is.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a szkript használatával

Ha van néhány Azure az modulok telepítve, és nem távolítható el (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a szkriptet a parancsfájl letöltése hivatkozás **manuális Letöltés** lapján. A parancsfájl nyers nupkg-fájlként van letöltve. Ha a parancsfájlt erről a nupkg-fájlról szeretné telepíteni, olvassa el a [manuális csomagok letöltése](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)című témakört.

A szkript futtatása:

1. Az Azure-hoz való `Connect-AzAccount` kapcsolódáshoz használható.

1. `Import-Module Az`A használatával importálhatja az az modulokat.

1. Futtassa `Get-Help AzureAppGWMigration.ps1` a parancsot a szükséges paraméterek vizsgálatához:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   A parancsfájl paraméterei:
   * **resourceId: [string]: kötelező** – ez a meglévő standard v1-vagy WAF v1-átjáró Azure-erőforrás-azonosítója. A karakterlánc értékének megkereséséhez navigáljon a Azure Portal, válassza ki az Application Gateway-vagy WAF-erőforrást, majd kattintson az átjáró **Tulajdonságok** hivatkozására. Az erőforrás-azonosító az adott oldalon található.

     Az erőforrás-azonosító lekéréséhez a következő Azure PowerShell parancsokat is futtathatja:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [string]: kötelező** – ez az az IP-címtartomány, amelyet az új v2-átjárót tartalmazó új alhálózat számára lefoglalt (vagy le szeretne foglalni). Ezt a CIDR-jelölésben kell megadni. Például: 10.0.0.0/24. Nem kell előre létrehoznia ezt az alhálózatot. A szkript létrehozza, ha nem létezik.
   * **appgwName: [string]: nem kötelező**. Ezt a karakterláncot adja meg, amelyet az új Standard_v2 vagy WAF_v2 átjáró neveként használhat. Ha ez a paraméter nincs megadva, a rendszer a meglévő v1-átjáró nevét fogja használni a hozzáfűzni *_v2* utótaggal együtt.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: nem kötelező**.  A v1-átjáró TLS/SSL-tanúsítványait képviselő PSApplicationGatewaySslCertificate-objektumok vesszővel tagolt listáját az új v2-átjáróra kell feltölteni. A standard v1-vagy WAF v1-átjáróhoz konfigurált TLS-/SSL-tanúsítványok mindegyikéhez létrehozhat egy új PSApplicationGatewaySslCertificate objektumot az `New-AzApplicationGatewaySslCertificate` itt látható parancs használatával. Szüksége lesz a TLS/SSL-tanúsítvány fájljának elérési útjára és a jelszóra.

     Ez a paraméter csak akkor választható, ha nem rendelkezik a v1-átjáróhoz vagy-WAF konfigurált HTTPS-figyelőkkel. Ha legalább egy HTTPS-figyelőt telepít, ezt a paramétert kell megadnia.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Az `$mySslCert1, $mySslCert2` előző példában szereplő (vesszővel tagolt) értékeket a parancsfájlban szereplő paraméterek értékeiként adhatja át.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: nem kötelező**. Az Ön által létrehozott PSApplicationGatewayTrustedRootCertificate-objektumok vesszővel tagolt listája, hogy azok a [megbízható legfelső szintű tanúsítványok](ssl-overview.md) a v2-átjáróról származó háttér-példányok hitelesítésére legyenek kialakítva.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      PSApplicationGatewayTrustedRootCertificate-objektumok listájának létrehozásához lásd: [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **privateipaddress tulajdonságot: [string]: nem kötelező**. Egy adott magánhálózati IP-címet, amelyet hozzá szeretne rendelni az új v2-átjáróhoz.  Ennek az új v2-átjáróhoz hozzárendelt VNet kell származnia. Ha ez nincs megadva, a parancsfájl egy magánhálózati IP-címet foglal le a v2-átjáróhoz.
   * **publicIpResourceId: [string]: nem kötelező**. A meglévő nyilvános IP-cím (standard SKU) erőforrás resourceId az előfizetésben, amelyet le szeretne foglalni az új v2-átjáróra. Ha ez nincs megadva, a parancsfájl egy új nyilvános IP-címet foglal le ugyanabban az erőforráscsoporthoz. A név a v2-átjáró neve, amely *-IP-* címmel van hozzáfűzve.
   * **validateMigration: [kapcsoló]: nem kötelező**. Akkor használja ezt a paramétert, ha azt szeretné, hogy a parancsfájl a v2-átjáró létrehozása és a konfigurációs másolat után elvégezze az alapszintű konfigurációs összehasonlítások érvényességét. Alapértelmezés szerint a rendszer nem végez érvényesítést.
   * **enableAutoScale: [kapcsoló]: nem kötelező**. Akkor használja ezt a paramétert, ha azt szeretné, hogy a parancsfájl a létrehozása után engedélyezze az automatikus skálázást az új v2-átjárón. Alapértelmezés szerint az automatikus skálázás le van tiltva. A későbbiekben manuálisan is engedélyezheti később az újonnan létrehozott v2-átjárón.

1. Futtassa a szkriptet a megfelelő paraméterek használatával. A befejezéshez öt – hét percet is igénybe vehet.

    **Példa**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Ügyfél-forgalom migrálása

Először ellenőrizze, hogy a parancsfájl sikeresen létrehozott-e egy új v2-átjárót a v1-átjáróról áttelepített pontos konfigurációval. Ezt a Azure Portal ellenőrizheti.

Emellett a v2-átjárón keresztül is küldhet kis mennyiségű forgalmat kézi tesztként.
  
Íme néhány forgatókönyv, ahol az aktuális Application Gateway (standard) megkapja az ügyfelek forgalmát, valamint a javaslataikat a következőkhöz:

* **Egy egyéni DNS-zóna (például contoso.com), amely a standard v1 vagy WAF v1-átjáróhoz társított előtéri IP-címére mutat (egy rekord használatával)**.

    A DNS-rekordot frissítheti úgy, hogy az Standard_v2 Application gatewayhez társított előtér-IP-vagy DNS-címkére mutasson. A DNS-rekordon beállított TTL-től függően előfordulhat, hogy az összes ügyfél-forgalom az új v2-átjáróra kerül át.
* **Egy egyéni DNS-zóna (például contoso.com), amely a DNS-címkére mutat (például: *MYAPPGW.EASTUS.CLOUDAPP.Azure.com* egy CNAME-rekord használatával), amely a v1-átjáróhoz van társítva**.

   Két lehetőség közül választhat:

  * Ha a nyilvános IP-címeket használja az Application gatewayen, a Traffic Manager profil használatával felügyelt, részletes áttelepítést végezhet a forgalom fokozatos átirányításához (súlyozott forgalom-útválasztási módszer) az új v2-átjáróra.

    Ezt úgy teheti meg, hogy a v1 és v2 Application Gateway DNS-címkéit is hozzáadja a [Traffic Manager profiljához](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), és az egyéni DNS-rekordot (például `www.contoso.com` ) a Traffic Manager tartományhoz (például contoso.trafficmanager.net) adja.
  * Másik lehetőségként frissítheti az egyéni tartomány DNS-rekordját, hogy az az új v2 Application Gateway DNS-címkéjére mutasson. A DNS-rekordon beállított TTL-től függően előfordulhat, hogy az összes ügyfél-forgalom az új v2-átjáróra kerül át.
* Az **ügyfelek az Application Gateway előtérbeli IP-címéhez csatlakoznak**.

   Frissítse az ügyfeleket az újonnan létrehozott v2 Application gatewayhez társított IP-cím (ek) használatára. Javasoljuk, hogy ne használjon közvetlenül IP-címeket. Érdemes lehet használni az Application gatewayhez társított DNS-név címkét (például yourgateway.eastus.cloudapp.azure.com), amelyet a saját egyéni DNS-zónájához (például contoso.com) is használhat.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak korlátozások a Azure PowerShell szkripttel a konfiguráció a v1-ről a v2-re való áttelepíteni?

Igen. Lásd a [figyelmeztetéseket és korlátozásokat](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>A cikk és a Azure PowerShell Application Gateway WAF termékre vonatkozó parancsfájl is? 

Igen.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>A Azure PowerShell szkript a saját v1-átjáróról az újonnan létrehozott v2-átjáróra is átváltott forgalmat?

Nem. A Azure PowerShell szkript csak a konfigurációt telepíti át. A tényleges forgalom áttelepítése az Ön felelőssége és a vezérlése.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>A Azure PowerShell szkript által létrehozott új v2-átjáró megfelelően van-e elkészítve a saját v1-átjáró által jelenleg kiszolgált forgalom kezeléséhez?

A Azure PowerShell szkript létrehoz egy új v2-átjárót, amely a meglévő v1-átjáró forgalmának kezeléséhez megfelelő méretű. Az automatikus skálázás alapértelmezés szerint le van tiltva, de a parancsfájl futtatásakor engedélyezheti az automatikus skálázást.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Úgy konfiguráltam a v1-átjárót, hogy naplókat küldjön az Azure Storage-nak. A szkript replikálja ezt a konfigurációt a v2-ben is?

Nem. A parancsfájl nem replikálja ezt a konfigurációt v2-re. A naplózási konfigurációt külön kell hozzáadnia az áttelepített v2-átjáróhoz.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Támogatja ez a szkript az Azure kulcstartóba feltöltött tanúsítványokat?

Nem. Jelenleg a parancsfájl nem támogatja a kulcstartóban lévő tanúsítványokat. Ezt azonban egy későbbi verzióra is figyelembe kell venni.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Néhány probléma merült fel a szkript használatával. Hogyan Kérhetek segítséget?
  
Az Azure-támogatással kapcsolatban a "konfiguráció és beállítás/Migrálás v2 SKU-ra" című témakörben olvashat. További információ az [Azure-támogatásról](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a Application Gateway v2-ről](application-gateway-autoscaling-zone-redundant.md)
