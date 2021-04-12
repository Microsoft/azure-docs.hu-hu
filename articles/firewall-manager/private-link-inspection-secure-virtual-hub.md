---
title: Biztonságos forgalom a privát végpontok számára az Azure Virtual WAN-ban
description: Megtudhatja, hogyan használhatja a hálózati szabályokat és az alkalmazási szabályokat az Azure virtuális WAN-beli privát végpontokra irányuló forgalom biztonságossá tételéhez
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: a1237077b9e0a1af67d34c85d8ea8d526bd71372
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281856"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Biztonságos forgalom a privát végpontok számára az Azure Virtual WAN-ban

Az [Azure Private-végpont](../private-link/private-endpoint-overview.md) az [Azure privát kapcsolatának](../private-link/private-link-overview.md)alapvető építőeleme. A privát végpontok lehetővé teszik, hogy a virtuális hálózaton üzembe helyezett Azure-erőforrások magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

A magánhálózati végpontok lehetővé teszik, hogy az erőforrások hozzáférjenek a virtuális hálózatban üzembe helyezett privát kapcsolati szolgáltatáshoz. A magánhálózati végponthoz való hozzáférés a virtuális hálózatok és a helyszíni hálózati kapcsolatok révén kiterjeszti a kapcsolatot.

Előfordulhat, hogy a forgalmat a helyszínen vagy az Azure-ból származó ügyfelekre kell szűrnie a virtuális WAN-kapcsolattal rendelkező virtuális hálózatban található privát végpontokon keresztül elérhető szolgáltatásokra. Ez a cikk végigvezeti a feladatot a [biztonságos virtuális központ](../firewall-manager/secured-virtual-hub.md) és a [Azure Firewall](../firewall/overview.md) biztonsági szolgáltatójának használatával.

Azure Firewall a következő módszerek bármelyikével szűri a forgalmat:

* A TCP-és UDP [-protokollok hálózati szabályainak teljes tartományneve](../firewall/fqdn-filtering-network-rules.md)
* FQDN a HTTP-, HTTPS-és MSSQL [-alkalmazási szabályokban](../firewall/features.md#application-fqdn-filtering-rules) .
* Forrás és cél IP-címei, portja és protokollja [hálózati szabályok](../firewall/features.md#network-traffic-filtering-rules) használatával

A hálózati szabályokon keresztüli szabályok használatával vizsgálja meg a magánhálózati végpontokra irányuló forgalmat.
A biztonságos virtuális hubot a Microsoft felügyeli, és nem csatolható [saját DNS zónához](../dns/private-dns-privatednszone.md). Erre azért van szükség, hogy feloldja a [privát kapcsolati erőforrás](../private-link/private-endpoint-overview.md#private-link-resource) teljes tartománynevét a hozzá tartozó privát végpont IP-címére.

Az SQL FQDN-szűrés csak [proxy módban](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott (1433-es port). A *proxy* mód több késést eredményezhet az *átirányításhoz* képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal hálózati szabályok teljes tartománynevével szűrheti.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>A forgalom szűrése a hálózati és az alkalmazási szabályokban található FQDN használatával

A következő lépésekkel engedélyezheti Azure Firewall a forgalom szűrését a hálózati és az alkalmazási szabályok FQDN használatával:

1. Helyezzen üzembe egy [DNS-továbbító](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) virtuális gépet a biztonságos virtuális hubhoz csatlakoztatott virtuális hálózatban, és kapcsolódjon a privát végpontok bejegyzéstípust üzemeltető saját DNS zónákhoz.

2. Konfigurálja az [Egyéni DNS-beállításokat](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) úgy, hogy az a DNS-továbbító virtuális gép IP-címére mutasson, és engedélyezze a DNS-proxyt a biztonságos virtuális központban üzembe helyezett Azure Firewallhoz társított tűzfal-házirendben.

3. Konfigurálja az [Egyéni DNS-kiszolgálókat](../virtual-network/manage-virtual-network.md#change-dns-servers) a biztonságos virtuális hubhoz csatlakozó virtuális hálózatok számára, hogy a biztonságos virtuális központban üzembe helyezett Azure Firewallhoz tartozó magánhálózati IP-címekre mutassanak.

4. Konfigurálja a helyszíni DNS-kiszolgálókat úgy, hogy a magánhálózati DNS-lekérdezéseket továbbítsák a privát végpontok nyilvános DNS-zónáinak a biztonságos virtuális központban üzembe helyezett Azure Firewallhoz társított magánhálózati IP-címekre.

5. Szükség szerint konfigurálja az [alkalmazás szabályait](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) vagy [hálózati szabályait](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) a biztonságos virtuális hubhoz a *cél típusú* FQDN-vel telepített Azure Firewallhoz társított tűzfal-házirendben, valamint a privát kapcsolati erőforrás nyilvános teljes tartománynevét *célként*.

6. Navigáljon a biztonságos virtuális hubhoz, és válassza ki azt a biztonságos virtuális *központot, amely* a védett virtuális központban telepített Azure Firewallhoz van társítva, és jelölje ki azt a biztonságos virtuális hubot, amelynél a privát végpontokra szánt forgalom szűrése be lesz állítva.

7. Navigáljon a **biztonsági konfigurációhoz**, válassza a **Küldés Azure Firewall** a **privát forgalom** alatt lehetőséget.

8. Válassza a **privát forgalmi előtagok** lehetőséget, hogy szerkessze azokat a CIDR előtagokat, amelyeket a rendszer a biztonságos virtuális központ Azure Firewall keresztül vizsgál meg, és mindegyik privát végponthoz adjon hozzá egy/32 előtagot az alábbiak szerint:

   > [!IMPORTANT]
   > Ha ezek a/32 előtagok nincsenek konfigurálva, a privát végpontoknak szánt forgalom megkerüli Azure Firewall.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="A Firewall Manager biztonsági beállításai" border="true":::

Ezek a lépések csak akkor működnek, ha az ügyfelek és a magánhálózati végpontok az azonos biztonságos virtuális hubhoz és a helyszíni ügyfelekhez csatlakoztatott különböző virtuális hálózatokon vannak telepítve. Ha az ügyfelek és a magánhálózati végpontok ugyanabban a virtuális hálózatban vannak telepítve, akkor létre kell hozni a privát végpontokhoz tartozó UDR/32 útvonalakat. Konfigurálja ezeket az útvonalakat a **következő ugrási típussal**  a **virtuális készülékre** , és a **következő ugrási címet** állítsa a biztonságos virtuális hubhoz telepített Azure Firewall magánhálózati IP-címére. Az **átjáró-útvonalak propagálását** **Igen** értékre kell állítani.

Az alábbi ábra azt szemlélteti, hogy a különböző ügyfelek milyen DNS-és adatforgalmi folyamatokkal csatlakoznak az Azure Virtual WAN-ban üzembe helyezett privát végpontokhoz:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Forgalmi folyamatok" border="true":::

## <a name="troubleshooting"></a>Hibaelhárítás

A következő problémák merülhetnek fel, amikor a titkos végpontokra irányuló forgalmat biztonságos virtuális központon keresztül próbálja szűrni:

- Az ügyfelek nem tudnak csatlakozni a privát végpontokhoz.

- A Azure Firewall megkerülése megtörténik. Ezt a tünetet a hálózati vagy az alkalmazás-szabályok bejegyzéseinek hiányában ellenőrizheti Azure Firewallban.

A legtöbb esetben a következő problémák egyike okozza ezeket a problémákat:

- Helytelen DNS-névfeloldás

- Helytelen az útválasztási konfiguráció

### <a name="incorrect-dns-name-resolution"></a>Helytelen DNS-névfeloldás

1. Ellenőrizze, hogy a virtuális hálózati DNS-kiszolgálók *egyéniek* -e, és az IP-cím a biztonságos virtuális központban Azure Firewall magánhálózati IP-címe.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Ellenőrizze, hogy az ügyfelek ugyanabban a virtuális hálózatban vannak-e, mint a DNS-továbbító virtuális gép. a magánhálózati végpont nyilvános teljes tartománynevét a hozzá tartozó magánhálózati IP-címhez is feloldja, ha közvetlenül a DNS-továbbítóként konfigurált virtuális gépet kérdezi le.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Vizsgálja meg a *AzureFirewallDNSProxy* Azure Firewall a naplóbejegyzések, és ellenőrizze, hogy képes-e fogadni és feloldani az ügyfelektől érkező DNS-lekérdezéseket.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Ellenőrizze, hogy a *DNS-proxy* engedélyezve van-e, és a DNS-továbbító virtuális gép IP-CÍMÉnek IP-címére mutató *Egyéni* DNS-kiszolgáló konfigurálva van-e a biztonságos virtuális központ Azure Firewallához társított tűzfal-házirendben.

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Helytelen az útválasztási konfiguráció

1. Ellenőrizze a *biztonsági beállításokat* a biztonságos virtuális központban üzembe helyezett Azure Firewallhoz társított tűzfal-házirendben. Győződjön meg arról, hogy a **MAGÁNHÁLÓZATI forgalom** oszlopban az összes olyan virtuális hálózat és ág kapcsolata látható, amelyről a forgalmat szűrni kívánja **Azure Firewall** .

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Azure Firewall által védett privát forgalom" border="true":::

2. Ellenőrizze a **biztonsági beállításokat** a biztonságos virtuális központban üzembe helyezett Azure Firewallhoz társított tűzfal-házirendben. Győződjön meg arról, hogy minden privát végpont magánhálózati IP-címéhez tartozik egy/32 bejegyzés, amelynél szűrni kívánja a forgalmat a **privát forgalom előtagjai** között.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="A Firewall Manager biztonsági konfigurációja – saját forgalmi előtagok" border="true":::

3. A virtuális WAN alatt található biztonságos virtuális központban ellenőrizze, hogy a virtuális hálózatokhoz és ágakhoz társított útválasztási táblákhoz milyen útvonalak vannak társítva a forgalom szűréséhez. Győződjön meg arról, hogy minden privát végpont magánhálózati IP-címéhez/32 bejegyzés van, amelynek a forgalmát szűrni szeretné.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Biztonságos virtuális hubhoz érvényes útvonalak" border="true":::

4. Vizsgálja meg a virtuális hálózatokban üzembe helyezett virtuális gépekhez csatlakoztatott hálózati adapterek hatályos útvonalait, amelyeknek a forgalmát szűrni szeretné. Győződjön meg arról, hogy minden privát végpont magánhálózati IP-címéhez/32 bejegyzés van, amelynek a forgalmát szűrni szeretné.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Vizsgálja meg a helyszíni útválasztási eszközök útválasztási táblázatait. Győződjön meg arról, hogy megtanulta azon virtuális hálózatok címterület-tárolóhelyeit, amelyeken a magánhálózati végpontok telepítve vannak.

   Az Azure Virtual WAN nem hirdeti meg a tűzfal-házirend **biztonsági konfigurációjában** a helyi számítógépeken a **magánhálózati forgalom előtagjai** alatt konfigurált előtagokat. A rendszer azt várta, hogy a/32-bejegyzések nem jelennek meg a helyszíni útválasztási eszközök útválasztási tábláiban.

6. Vizsgálja meg a **AzureFirewallApplicationRule** és a **AzureFirewallNetworkRule** Azure Firewall naplókat. Győződjön meg arról, hogy a titkos végpontok felé irányuló forgalom naplózásra kerül.

   A **AzureFirewallNetworkRule** -naplóbejegyzések nem tartalmaznak FQDN-információkat. Szűrés IP-cím és port alapján a hálózati szabályok vizsgálata során.

   [Azure Files](../storage/files/storage-files-introduction.md) privát végpontokra irányuló forgalom szűrése esetén a **AzureFirewallNetworkRule** csak akkor jönnek létre, amikor az ügyfél először csatlakoztat vagy csatlakozik a fájlmegosztást. A Azure Firewall nem hoz majd naplókat a fájlmegosztás fájljaihoz tartozó [szifilisz](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) -műveletekhez. Ennek az az oka, hogy a rendszer az állandó TCP-csatornán keresztül hajtja végre a szifilisz-műveleteket, amikor az ügyfél először csatlakoztatja vagy csatlakoztatja a fájlmegosztást.

   Példa az alkalmazási szabály naplójának lekérdezésére:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Következő lépések

- [Egy privát végpontra irányuló forgalom vizsgálata az Azure Firewall használatával](../private-link/inspect-traffic-with-azure-firewall.md)
