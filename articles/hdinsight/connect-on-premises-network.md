---
title: Az Azure HDInsight összekapcsolása a helyszíni hálózattal
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöt egy Azure-Virtual Networkban, majd hogyan csatlakoztatható a helyszíni hálózathoz. Megtudhatja, hogyan konfigurálhatja a névfeloldást a HDInsight és a helyszíni hálózat között egy egyéni DNS-kiszolgáló használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: cd787e1c846bfe4728577cbbce069385ce064a10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943407"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>A HDInsight csatlakoztatása a helyszíni hálózathoz

Ismerje meg, hogyan csatlakoztathatók a HDInsight a helyszíni hálózathoz az Azure Virtual Network és egy VPN Gateway használatával. Ez a dokumentum a következő tervezési információkat tartalmazza:

* A HDInsight használata egy Azure-Virtual Network, amely a helyszíni hálózathoz csatlakozik.
* DNS-névfeloldás konfigurálása a virtuális hálózat és a helyszíni hálózat között.
* Hálózati biztonsági csoportok konfigurálása a HDInsight való internetes hozzáférés korlátozására.
* A virtuális hálózat HDInsight által biztosított portok.

## <a name="overview"></a>Áttekintés

A következő műveletek végrehajtásával engedélyezheti, hogy a csatlakoztatott hálózat HDInsight és erőforrásai kommunikáljanak a név alapján:

1. Azure-Virtual Network létrehozása.
1. Hozzon létre egy egyéni DNS-kiszolgálót az Azure Virtual Networkban.
1. Konfigurálja úgy a virtuális hálózatot, hogy az alapértelmezett Azure rekurzív feloldó helyett az egyéni DNS-kiszolgálót használja.
1. Konfigurálja a továbbítást az egyéni DNS-kiszolgáló és a helyszíni DNS-kiszolgáló között.

Ezek a konfigurációk a következő viselkedést teszik lehetővé:

* A rendszer a __virtuális hálózat__ DNS-utótagját tartalmazó teljes tartománynevek kérelmeit továbbítja az egyéni DNS-kiszolgálónak. Az egyéni DNS-kiszolgáló ezután továbbítja ezeket a kéréseket az Azure rekurzív feloldónak, amely visszaadja az IP-címet.
* Minden más kérelem továbbítva lesz a helyszíni DNS-kiszolgálónak. Még a nyilvános internetes erőforrásokra vonatkozó kéréseket, például a microsoft.com a rendszer a helyszíni DNS-kiszolgálónak továbbítja a névfeloldáshoz.

A következő ábrán a zöld vonalak a virtuális hálózat DNS-utótagjának végét képező erőforrásokra vonatkozó kérések. A kék vonalak a helyszíni hálózaton vagy a nyilvános interneten található erőforrásokra vonatkozó kérések.

![A DNS-kérelmek konfigurációban való feloldásának ábrája](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Ha a PowerShellt használja, szüksége lesz az az [modulra](/powershell/azure/).
* Ha az Azure CLI-t szeretné használni, és még nem telepítette, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="create-virtual-network-configuration"></a>Virtuális hálózat konfigurációjának létrehozása

A következő dokumentumok segítségével megtudhatja, hogyan hozhat létre a helyszíni hálózathoz csatlakozó Azure-Virtual Network:

* [Az Azure Portal használata](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Az Azure PowerShell használata](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Az Azure parancssori felület használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Egyéni DNS-kiszolgáló létrehozása

> [!IMPORTANT]  
> A HDInsight a virtuális hálózatra való telepítése előtt létre kell hoznia és konfigurálnia kell a DNS-kiszolgálót.

Ezek a lépések a [Azure Portal](https://portal.azure.com) használatával létrehoznak egy Azure-beli virtuális gépet. A virtuális gépek létrehozásának egyéb módjaiért lásd: virtuális gép [létrehozása – Azure CLI](../virtual-machines/linux/quick-create-cli.md) és [VM-Azure PowerShell létrehozása](../virtual-machines/linux/quick-create-powershell.md).  A [kötési](https://www.isc.org/downloads/bind/) DNS szoftvert használó linuxos virtuális gép létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  
1. A felső menüben válassza az **+ erőforrás létrehozása** lehetőséget.

    ![Ubuntu rendszerű virtuális gép létrehozása](./media/connect-on-premises-network/azure-portal-create-resource.png)

1.   >  A **virtuális gép létrehozása** lapon válassza a számítási **virtuális gép** lehetőséget.

1. Az __alapok__ lapon adja meg a következő adatokat:  
  
    | Mező | Érték |
    | --- | --- |
    |Előfizetés |Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport |Válassza ki azt az erőforráscsoportot, amely a korábban létrehozott virtuális hálózatot tartalmazza.|
    |Virtuális gép neve | Adjon meg egy rövid nevet, amely azonosítja ezt a virtuális gépet. Ez a példa az **DNSProxy**-t használja.|
    |Régió | Válassza ki ugyanazt a régiót, mint a korábban létrehozott virtuális hálózat.  Nem minden virtuálisgép-méret érhető el minden régióban.  |
    |Rendelkezésre állási beállítások |  Válassza ki a kívánt rendelkezésre állási szintet.  Az Azure számos lehetőséget kínál az alkalmazások rendelkezésre állásának és rugalmasságának kezelésére.  A Availability Zones-vagy rendelkezésre állási csoportokban lévő replikált virtuális gépek használatát az adatközpont-kimaradások és a karbantartási események védelme érdekében Ebben a példában **nem szükséges infrastruktúra-redundancia**. |
    |Kép | Hagyja az **Ubuntu Server 18,04 LTS**-et. |
    |Hitelesítéstípus | __Jelszó__ vagy __nyilvános SSH-kulcs__: az SSH-fiók hitelesítési módszere. Javasoljuk, hogy használjon nyilvános kulcsokat, mivel azok biztonságosabbak. Ez a példa a **jelszót** használja.  További információ: [ssh-kulcsok létrehozása és használata Linux rendszerű virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentum.|
    |Felhasználónév |Adja meg a virtuális gép rendszergazdai felhasználónevét.  Ez a példa az **sshuser**-t használja.|
    |Jelszó vagy nyilvános SSH-kulcs | A rendelkezésre álló mező meghatározása a **hitelesítési típus** alapján történik.  Adja meg a megfelelő értéket.|
    |Nyilvános bejövő portok|Válassza a **kiválasztott portok engedélyezése** lehetőséget. Ezután válassza az **SSH (22)** lehetőséget a **bejövő portok kiválasztása** legördülő listából.|

    ![Virtuális gép alapszintű konfigurációja](./media/connect-on-premises-network/virtual-machine-basics.png)

    Hagyja meg az egyéb bejegyzéseket az alapértelmezett értékeken, majd válassza a **hálózatkezelés** lapot.

4. A **hálózatkezelés** lapon adja meg a következő adatokat:

    | Mező | Érték |
    | --- | --- |
    |Virtuális hálózat | Válassza ki a korábban létrehozott virtuális hálózatot.|
    |Alhálózat | Válassza ki a korábban létrehozott virtuális hálózat alapértelmezett alhálózatát. Ne __válassza ki__ a VPN-átjáró által használt alhálózatot.|
    |Nyilvános IP-cím | Használja az automatikusan feltöltött értéket.  |

    ![HDInsight virtuális hálózati beállításai](./media/connect-on-premises-network/virtual-network-settings.png)

    Hagyja meg az egyéb bejegyzéseket az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** elemet.

5. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget a virtuális gép létrehozásához.

### <a name="review-ip-addresses"></a>IP-címek ellenőrzése

Miután létrehozta a virtuális gépet, az **üzembe helyezés sikeres** értesítése jelenik meg az **erőforrás keresése** gombbal.  Az új virtuális géphez való ugráshoz válassza az **erőforrás keresése** lehetőséget.  Az új virtuális gép alapértelmezett nézetében hajtsa végre az alábbi lépéseket a társított IP-címek azonosításához:

1. A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.

2. Jegyezze fel a **nyilvános IP-cím/DNS-név címke** és a **MAGÁNHÁLÓZATI IP-cím** értékét későbbi használatra.

   ![Nyilvános és magánhálózati IP-címek](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Kötés telepítése és konfigurálása (DNS-szoftver)

1. Használja az SSH-t a virtuális gép __nyilvános IP-címéhez__ való kapcsolódáshoz. A helyére írja `sshuser` be a virtuális gép létrehozásakor megadott SSH-felhasználói fiókot. A következő példa egy virtuális géphez csatlakozik a 40.68.254.142-ben:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. A kötés telepítéséhez használja az alábbi parancsokat az SSH-munkamenetből:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ha úgy szeretné konfigurálni a kötést, hogy a névfeloldási kérelmeket továbbítsa a helyszíni DNS-kiszolgálóra, használja a következő szöveget a fájl tartalmának használatával `/etc/bind/named.conf.options` :

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Cserélje le a szakasz értékeit a `goodclients` virtuális hálózat és a helyszíni hálózat IP-címének tartományára. Ez a szakasz azokat a címeket határozza meg, amelyekkel a DNS-kiszolgáló fogadja a kérelmeket.
    >
    > Cserélje le a `192.168.0.1` szakasz bejegyzését a helyszíni `forwarders` DNS-kiszolgáló IP-címére. Ez a bejegyzés a DNS-kéréseket a helyszíni DNS-kiszolgálóra irányítja a feloldáshoz.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    A fájl mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

4. Az SSH-munkamenetben használja a következő parancsot:

    ```bash
    hostname -f
    ```

    Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a virtuális hálózat __DNS-utótagja__ . Mentse ezt az értéket, mivel később használatban van.

5. A virtuális hálózaton belüli erőforrások DNS-neveinek feloldásához a kötés konfigurálásához használja a következő szöveget a fájl tartalmának használatával `/etc/bind/named.conf.local` :

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Le kell cserélnie a `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` -t a korábban lekért DNS-utótaggal.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    A fájl mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

6. A kötés elindításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. A következő parancsokkal ellenőrizheti, hogy a kötés feloldja-e a helyszíni hálózaton található erőforrások nevét:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Cserélje le a értéket `dns.mynetwork.net` a helyszíni hálózatban lévő erőforrás teljes tartománynevére (FQDN).
    >
    > Cserélje le a értékét a `10.0.0.4` virtuális hálózatban lévő egyéni DNS __-kiszolgáló belső IP-címére__ .

    A válasz az alábbi szöveghez hasonlóan jelenik meg:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>A virtuális hálózat konfigurálása az egyéni DNS-kiszolgáló használatára

Ha úgy szeretné konfigurálni a virtuális hálózatot, hogy az egyéni DNS-kiszolgálót használja az Azure rekurzív feloldó helyett, kövesse az alábbi lépéseket a [Azure Portal](https://portal.azure.com):

1. A bal oldali menüben navigáljon az **összes szolgáltatás**  >  **hálózati**  >  **virtuális hálózat** elemre.

2. Válassza ki a virtuális hálózatot a listából, amely megnyitja a virtuális hálózat alapértelmezett nézetét.  

3. Az alapértelmezett nézet **Beállítások** területén válassza a **DNS-kiszolgálók** elemet.  

4. Válassza az __Egyéni__ lehetőséget, majd adja meg az egyéni DNS-kiszolgáló **MAGÁNHÁLÓZATI IP-címét** .

5. Kattintson a __Mentés__ gombra.  <br />  

    ![A hálózat egyéni DNS-kiszolgálójának beállítása](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Helyszíni DNS-kiszolgáló konfigurálása

Az előző szakaszban konfigurálta az egyéni DNS-kiszolgálót, hogy továbbítsa a kéréseket a helyszíni DNS-kiszolgálónak. Ezután a helyszíni DNS-kiszolgálót úgy kell konfigurálni, hogy továbbítsa a kérelmeket az egyéni DNS-kiszolgálónak.

A DNS-kiszolgáló konfigurálásával kapcsolatos konkrét lépéseket a DNS-kiszolgáló dokumentációjában talál. Tekintse át a __feltételes továbbító__ konfigurálásának lépéseit.

A feltételes továbbítás csak egy adott DNS-utótagra vonatkozó kérelmeket továbbítja. Ebben az esetben egy továbbítót kell konfigurálnia a virtuális hálózat DNS-utótagja számára. Az utótagra vonatkozó kérelmeket továbbítani kell az egyéni DNS-kiszolgáló IP-címére. 

A következő szöveg a **kötési** DNS-szoftver feltételes továbbítójának konfigurációját szemlélteti:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

További információ a **Windows Server 2016** DNS használatával kapcsolatban: [Add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) dokumentáció...

Miután konfigurálta a helyszíni DNS-kiszolgálót, `nslookup` a helyszíni hálózaton keresztül ellenőrizheti, hogy fel tudja-e oldani a neveket a virtuális hálózaton. A következő példa 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Ez a példa a helyszíni DNS-kiszolgálót használja a 196.168.0.4-ben az egyéni DNS-kiszolgáló nevének feloldásához. Cserélje le az IP-címet a helyszíni DNS-kiszolgáló egyikére. Cserélje le a `dnsproxy` címeket az egyéni DNS-kiszolgáló teljesen minősített tartománynevére.

## <a name="optional-control-network-traffic"></a>Nem kötelező: hálózati forgalom szabályozása

Hálózati biztonsági csoportokat (NSG) vagy felhasználó által definiált útvonalakat (UDR) is használhat a hálózati forgalom szabályozásához. A NSG lehetővé teszi a bejövő és a kimenő forgalom szűrését, valamint a forgalom engedélyezését vagy megtagadását. A UDR lehetővé teszi annak szabályozását, hogy a forgalom hogyan áramlik a virtuális hálózat, az Internet és a helyszíni hálózat erőforrásai között.

> [!WARNING]  
> A HDInsight az Azure-felhő adott IP-címeinek, valamint a nem korlátozott kimenő hozzáférésnek a bejövő hozzáférését igényli. Ha NSG vagy UDR használ a forgalom vezérléséhez, a következő lépéseket kell elvégeznie:

1. Keresse meg a virtuális hálózatot tartalmazó hely IP-címeit. A szükséges IP-címek hely szerinti listájának megtekintéséhez tekintse meg a [szükséges IP-címeket](./hdinsight-management-ip-addresses.md).

2. Az 1. lépésben azonosított IP-címek esetében engedélyezze a bejövő forgalmat az adott IP-címekről.

   * Ha a __NSG__-t használja: engedélyezze a __bejövő__ forgalmat az __443__ -as porton az IP-címekhez.
   * Ha a __UDR__-t használja: állítsa be az útvonal __következő ugrási__ típusát az __internetre__ az IP-címekhez.

Az Azure PowerShell vagy az Azure CLI használatával történő NSG létrehozásához például tekintse meg a [HDInsight kiterjesztése az Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) dokumentummal című témakört.

## <a name="create-the-hdinsight-cluster"></a>A HDInsight-fürt létrehozása

> [!WARNING]  
> A virtuális hálózat HDInsight telepítése előtt konfigurálnia kell az egyéni DNS-kiszolgálót.

A HDInsight-fürt létrehozásához használja a [HDInsight-fürt létrehozása a Azure Portal dokumentum használatával](./hdinsight-hadoop-create-linux-clusters-portal.md) című témakör lépéseit.

> [!WARNING]  
> * A fürt létrehozása során ki kell választania a virtuális hálózatot tartalmazó helyet.
> * A konfiguráció __Speciális beállítások__ részében ki kell választania a korábban létrehozott virtuális hálózatot és alhálózatot.

## <a name="connecting-to-hdinsight"></a>Csatlakozás a HDInsight

A HDInsight legtöbb dokumentációja feltételezi, hogy a fürthöz az interneten keresztül férhet hozzá. Például hogy a `https://CLUSTERNAME.azurehdinsight.net` címen tud csatlakozni a fürthöz. Ez a címe a nyilvános átjárót használja, amely nem érhető el, ha a NSG vagy a UDR használatával korlátozza a hozzáférést az internetről.

Egyes dokumentációk arra is hivatkoznak `headnodehost` , amikor egy SSH-munkamenetből csatlakozik a fürthöz. Ez a címe csak a fürtben lévő csomópontokból érhető el, és nem használható a virtuális hálózaton keresztül csatlakozó ügyfeleken.

Ha közvetlenül szeretne csatlakozni a HDInsight a virtuális hálózaton keresztül, kövesse az alábbi lépéseket:

1. A HDInsight-fürtcsomópontok belső teljes tartománynevének felderítéséhez használja az alábbi módszerek egyikét:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. A szolgáltatás által elérhető port meghatározásához tekintse meg a [Apache Hadoop Services által használt](./hdinsight-hadoop-port-settings-for-services.md) portokat a HDInsight dokumentumban.

    > [!IMPORTANT]  
    > A fő csomópontokon futó egyes szolgáltatások egyszerre csak egy csomóponton aktívak. Ha egy fő csomóponton próbál hozzáférni egy szolgáltatáshoz, és az nem sikerül, váltson át a másik fő csomópontra.
    >
    > Például az Apache Ambari egyszerre csak egy fő csomóponton aktív. Ha megpróbál hozzáférni a Ambari egy fő csomóponton, és 404-es hibát ad vissza, akkor a másik fő csomóponton fut.

## <a name="next-steps"></a>Következő lépések

* A virtuális hálózatok HDInsight használatával kapcsolatos további információkért lásd: [virtuális hálózat központi telepítésének megtervezése az Azure HDInsight-fürtökhöz](./hdinsight-plan-virtual-network-deployment.md).

* Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../virtual-network/virtual-networks-overview.md).

* A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md).

* A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).