---
title: HBase-fürt replikálása virtuális hálózatokban – Azure HDInsight
description: Megtudhatja, hogyan állíthatja be a HBase-replikációt az egyik HDInsight-verzióról a másikra a terheléselosztás, a magas rendelkezésre állás, a nulla leállás utáni áttelepítés és a frissítések, valamint a vész-helyreállítási folyamat
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: cfcb3a5a601afadb9f3fcd71c24e18a9d7f27b9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946407"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Apache HBase-fürt replikálásának beállítása az Azure Virtual Networks szolgáltatásban

Megtudhatja, hogyan állíthatja be az [Apache HBase](https://hbase.apache.org/) -replikációt egy virtuális hálózaton belül, illetve két virtuális hálózat között az Azure-ban.

A fürt replikálása forrás-leküldéses módszertant használ. Egy HBase-fürt lehet forrás vagy cél, vagy egyszerre is teljesítheti mindkét szerepkört. A replikálás aszinkron módon történik. A replikáció célja végleges konzisztencia. Ha a forrás a replikáció engedélyezésekor egy oszlop családjának szerkeszti az oszlopot, a rendszer az összes célhelyre propagálja a szerkesztést. Ha az adatok replikálása az egyik fürtről a másikra történik, a rendszer a forrás-fürtöt és az összes olyan fürtöt nyomon követi, amely már felhasználta az adatok követését a replikálási hurok

Ebben a cikkben egy forrás-cél replikálást állít be. Más fürtök topológiái esetén tekintse meg az [Apache HBase útmutatóját](https://hbase.apache.org/book.html#_cluster_replication).

A következő HBase-replikációs használati esetek egyetlen virtuális hálózat esetén:

* Terheléselosztás. Futtathat például vizsgálatokat vagy MapReduce feladatokat a célszámítógépen, és betöltheti az adatokkal a forrás-fürtöt.
* Magas rendelkezésre állás hozzáadása.
* Adatok áttelepítése egyik HBase-fürtről a másikra.
* Azure HDInsight-fürt frissítése egyik verzióról a másikra.

A következő két virtuális hálózat HBase-replikációs használati esetei:

* Vész-helyreállítás beállítása.
* Terheléselosztás és az alkalmazás particionálása.
* Magas rendelkezésre állás hozzáadása.

A fürtöket a [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication)parancsfájl- [művelet](../hdinsight-hadoop-customize-cluster-linux.md) parancsfájljai használatával replikálhatja.

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdése előtt Azure-előfizetéssel kell rendelkeznie. Lásd: [ingyenes Azure-próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>A környezetek beállítása

Három konfigurációs lehetőség közül választhat:

- Két Apache HBase-fürt egyetlen Azure-beli virtuális hálózatban.
- Két Apache HBase-fürt két különböző virtuális hálózatban ugyanabban a régióban.
- Két Apache HBase-fürt két különböző régióban (Geo-replikáció) két különböző virtuális hálózatban.

Ez a cikk a földrajzi replikálási forgatókönyvet ismerteti.

A környezetek beállításának elősegítése érdekében létrehoztunk néhány [Azure Resource Manager sablont](../../azure-resource-manager/management/overview.md). Ha más módszerekkel szeretné beállítani a környezeteket, tekintse meg a következőt:

- [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)
- [Apache HBase-fürtök létrehozása az Azure-ban Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Két virtuális hálózat beállítása két különböző régióban

Ha olyan sablont szeretne használni, amely két virtuális hálózatot hoz létre két különböző régióban és a VPN-kapcsolatot a virtuális hálózatok között, válassza a következő **üzembe helyezés az Azure** -ban gombot. A sablon definícióját egy [nyilvános blob Storage](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)tárolja.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

A sablonban a rögzített értékek némelyike:

**1. VNet**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA nyugati régiója |
| VNet neve | &lt;ClusterNamePrevix> – vnet1 |
| Címterület előtagja | 10.1.0.0/16 |
| Alhálózat neve | 1. alhálózat |
| Alhálózat előtagja | 10.1.0.0/24 |
| Alhálózat (átjáró) neve | GatewaySubnet (nem módosítható) |
| Alhálózat (átjáró) előtagja | 10.1.255.0/27 |
| Átjáró neve | vnet1gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típusa | Útvonalalapú |
| Átjáró SKU | Alapszintű |
| Átjáró IP-címe | vnet1gwip |

**2. VNet**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA keleti régiója |
| VNet neve | &lt;ClusterNamePrevix> – vnet2 |
| Címterület előtagja | 10.2.0.0/16 |
| Alhálózat neve | 1. alhálózat |
| Alhálózat előtagja | 10.2.0.0/24 |
| Alhálózat (átjáró) neve | GatewaySubnet (nem módosítható) |
| Alhálózat (átjáró) előtagja | 10.2.255.0/27 |
| Átjáró neve | vnet2gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típusa | Útvonalalapú |
| Átjáró SKU | Alapszintű |
| Átjáró IP-címe | vnet1gwip |

## <a name="setup-dns"></a>DNS beállítása

Az utolsó szakaszban a sablon létrehoz egy Ubuntu-alapú virtuális gépet a két virtuális hálózat mindegyikében.  Ebben a szakaszban a kötést telepíti a két DNS virtuális gépre, majd konfigurálja a DNS-továbbítást a két virtuális gépen.

A kötés telepítéséhez a Yon-nek meg kell keresnie a két DNS virtuális gép nyilvános IP-címét.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. A DNS virtuális gép megnyitásához válassza az **erőforráscsoportok > [erőforráscsoport neve] > [vnet1DNS]** elemet.  Az erőforráscsoport neve az utolsó eljárásban létrehozott csoport. Az alapértelmezett DNS-beli virtuális gépek nevei a következők: *vnet1DNS* és *vnet2NDS*.
3. Válassza a **Tulajdonságok** lehetőséget a virtuális hálózat Tulajdonságok lapjának megnyitásához.
4. Jegyezze fel a **nyilvános IP-címet**, és ellenőrizze a **magánhálózati IP-címet** is.  A magánhálózati IP-címet a vnet2DNS vnet1DNS és **10.2.0.4** **10.1.0.4** kell megadni.  
5. Módosítsa a DNS-kiszolgálókat mindkét virtuális hálózatra úgy, hogy az alapértelmezett (Azure által biztosított) DNS-kiszolgálókat használják a bejövő és kimenő hozzáféréshez a csomagok letöltéséhez a következő lépésekben a kötés telepítéséhez.

A kötés telepítéséhez kövesse az alábbi eljárást:

1. Használja az SSH-t a DNS virtuális gép __nyilvános IP-címéhez__ való kapcsolódáshoz. A következő példa egy virtuális géphez csatlakozik a 40.68.254.142-ben:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Cserélje le a `sshuser` értéket a DNS virtuális gép létrehozásakor megadott SSH-felhasználói fiókra.

    > [!NOTE]  
    > A segédprogram beszerzésének számos módja van `ssh` . Linux, UNIX és macOS rendszeren az operációs rendszer részeként van megadva. Ha Windows rendszert használ, vegye figyelembe a következő lehetőségek egyikét:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10](/windows/wsl/about)
    > * [Githttps://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSHhttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. A kötés telepítéséhez használja az alábbi parancsokat az SSH-munkamenetből:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurálja a kötést a névfeloldási kérelmek továbbításához a helyszíni DNS-kiszolgálóra. Ehhez használja a következő szöveget a fájl tartalmaként `/etc/bind/named.conf.options` :

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Cserélje le a szakasz értékeit a `goodclients` két virtuális hálózat IP-címének tartományára. Ez a szakasz azokat a címeket határozza meg, amelyekkel a DNS-kiszolgáló fogadja a kérelmeket.

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
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a virtuális hálózat __DNS-utótagja__ . Mentse ezt az értéket, mivel később még használni fogjuk.

    A DNS-utótagot is meg kell találnia a másik DNS-kiszolgálóról. A következő lépésben szüksége lesz rá.

5. A virtuális hálózaton belüli erőforrások DNS-neveinek feloldásához a kötés konfigurálásához használja a következő szöveget a fájl tartalmának használatával `/etc/bind/named.conf.local` :

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > A `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` másik virtuális hálózat DNS-utótagját le kell cserélnie. A továbbító IP pedig a másik virtuális hálózat DNS-kiszolgálójának magánhálózati IP-címe.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    A fájl mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

6. A kötés elindításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. A következő parancsokkal ellenőrizheti, hogy a kötés fel tudja-e oldani a másik virtuális hálózatban lévő erőforrások nevét:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Cserélje le a értéket a `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` másik hálózatban található DNS virtuális gép teljes tartománynevére (FQDN).
    >
    > Cserélje le az értékét a `10.2.0.4` másik virtuális hálózatban lévő egyéni DNS __-kiszolgáló belső IP-címére__ .

    A válasz az alábbi szöveghez hasonlóan jelenik meg:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Eddig nem tekintheti meg az IP-címet a másik hálózatról a megadott DNS-kiszolgáló IP-címe nélkül.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>A virtuális hálózat konfigurálása az egyéni DNS-kiszolgáló használatára

Ha úgy szeretné konfigurálni a virtuális hálózatot, hogy az egyéni DNS-kiszolgálót használja az Azure rekurzív feloldó helyett, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a virtuális hálózatot, majd válassza a __DNS-kiszolgálók__ lehetőséget.

2. Válassza az __Egyéni__ lehetőséget, majd adja meg az egyéni DNS __-kiszolgáló belső IP-címét__ . Végül válassza a __Mentés__ lehetőséget.

6. Nyissa meg a DNS-kiszolgáló virtuális gépet a vnet1-ben, és kattintson az **Újraindítás** gombra.  A DNS-konfiguráció érvénybe léptetéséhez újra kell indítania a virtuális hálózatban lévő összes virtuális gépet.
7. Ismételje meg a lépéseket az egyéni DNS-kiszolgáló vnet2 való konfigurálásához.

A DNS-konfiguráció teszteléséhez az SSH használatával csatlakozhat a két DNS virtuális géphez, és a másik virtuális hálózat DNS-kiszolgálóját az állomásnév használatával pingelheti. Ha nem működik, a következő parancs használatával ellenőrizze a DNS-állapotot:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase-fürtök létrehozása

Hozzon létre egy [Apache HBase](https://hbase.apache.org/) -fürtöt mind a két virtuális hálózatban a következő konfigurációval:

- **Erőforráscsoport neve**: használja ugyanazt az erőforráscsoport-nevet a virtuális hálózatok létrehozásakor.
- **Fürt típusa**: HBase
- **Verzió**: HBase 1.1.2 (HDI 3,6)
- **Hely**: a virtuális hálózattal megegyező helyet használja.  Alapértelmezés szerint a vnet1 az *USA nyugati* régiója, a vnet2 pedig az *USA keleti* régiója.
- **Storage**: hozzon létre egy új Storage-fiókot a fürthöz.
- **Virtuális hálózat** (a portál speciális beállításaiból): válassza ki a legutóbbi eljárás során létrehozott vnet1.
- **Alhálózat**: a sablonban használt alapértelmezett név a **subnet1**.

Annak biztosítása érdekében, hogy a környezet megfelelően legyen konfigurálva, meg kell tudnia pingelni a átjárócsomóponthoz teljes tartománynevét a két fürt között.

## <a name="load-test-data"></a>Tesztadatok betöltése

Fürt replikálásakor meg kell adnia a replikálni kívánt táblákat. Ebben a szakaszban egy bizonyos adatforrást tölt be a fürtbe. A következő szakaszban engedélyeznie kell a két fürt közötti replikálást.

Ha egy **Contacts** táblát szeretne létrehozni, és beszúr néhány információt a táblázatba, kövesse az [Apache HBase oktatóanyag: az Apache HBase használatának első lépései a HDInsight-ben](apache-hbase-tutorial-get-started-linux.md)című témakört.

> [!NOTE]
> Ha egyéni névtérből kívánja replikálni a táblákat, győződjön meg arról, hogy a megfelelő egyéni névterek definiálva vannak a célkiszolgálón is.
>

## <a name="enable-replication"></a>A replikáció engedélyezése

A következő lépések azt ismertetik, hogyan hívható meg a parancsfájl műveleti szkriptje a Azure Portalból. A parancsfájl-műveletek Azure PowerShell és a klasszikus Azure CLI használatával történő futtatásával kapcsolatos információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-művelet használatával](../hdinsight-hadoop-customize-cluster-linux.md).

**A HBase replikálásának engedélyezése a Azure Portal**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a forrás HBase-fürtöt.
3. A fürt menüben válassza a **parancsfájlok műveletek** elemet.
4. A lap tetején válassza az **új küldése** lehetőséget.
5. Válassza ki vagy adja meg a következő adatokat:

   1. **Név**: adja meg a **replikáció engedélyezése** értéket.
   2. **Bash-parancsfájl URL-címe**: ENTER **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Head**: Győződjön meg róla, hogy ez be van jelölve. Törölje a többi csomópont típusát.
   4. **Paraméterek**: a következő minta paraméterek lehetővé teszik az összes létező tábla replikálását, majd az összes adatforrás másolását a fürtbe:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > A forrás-és a célként megadott fürt DNS-neveként a teljes tartománynevet használja az állomásnév helyett.
      >
      > Ez az útmutató feltételezi, hogy az hn1 aktív átjárócsomóponthoz. Ellenőrizze, hogy a fürt azonosítsa-e az aktív fő csomópontot.

6. Válassza a **Létrehozás** lehetőséget. A szkript futása hosszabb ideig is eltarthat, különösen a **-COPYDATA** argumentum használatakor.

Szükséges argumentumok:

|Név|Leírás|
|----|-----------|
|-s,--src-cluster | Megadja a forrás HBase-fürt DNS-nevét. Például:-s hbsrccluster,--src-cluster = hbsrccluster |
|-d,--DST-cluster | Megadja a cél (replika) HBase-fürt DNS-nevét. Például:-s dsthbcluster,--src-cluster = dsthbcluster |
|-SP,--src-ambari-Password | Megadja a Ambari rendszergazdai jelszavát a forrás HBase-fürtön. |
|-DP,--DST-ambari-Password | Megadja a Ambari rendszergazdai jelszavát a cél HBase-fürtön.|

Nem kötelező argumentumok:

|Név|Leírás|
|----|-----------|
|-Su,--src-ambari-User | A Ambari rendszergazdai felhasználónevét adja meg a forrás HBase-fürtön. Az alapértelmezett érték a **rendszergazda**. |
|-du,--DST-ambari-User | A Ambari rendszergazdai felhasználónevét adja meg a cél HBase-fürtön. Az alapértelmezett érték a **rendszergazda**. |
|-t,--Table-List | A replikálni kívánt táblákat határozza meg. Például:--table-List = "tábla1; table2; Tábl3". Ha nem ad meg táblákat, az összes meglévő HBase-tábla replikálódik.|
|-m,--Machine | Meghatározza azt a fő csomópontot, ahol a parancsfájl-művelet fut. Az értéket az aktív fő csomópont alapján kell kiválasztani. Akkor használja ezt a beállítást, ha az $0 parancsfájlt parancsfájl-műveletként futtatja a HDInsight-portálról vagy Azure PowerShellról.|
|-CP,-COPYDATA | Engedélyezi a meglévő adatáttelepítést azon táblákon, amelyeken engedélyezve van a replikáció. |
|-RPM,-replikálás-Phoenix-meta | Engedélyezi a replikációt a Phoenix rendszertábláin. <br><br>*Ezt a beállítást körültekintően használhatja.* Javasoljuk, hogy a parancsfájl használata előtt hozza létre újra a Phoenix-táblákat a replika fürtökön. |
|-h,-– Súgó | Megjeleníti a használati adatokat. |

A `print_usage()` [szkript](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) szakasza részletesen ismerteti a paramétereket.

A parancsfájl sikeres üzembe helyezését követően az SSH használatával kapcsolódhat a cél HBase-fürthöz, majd ellenőrizheti, hogy az információk replikálva lettek-e.

### <a name="replication-scenarios"></a>Replikációs forgatókönyvek

A következő lista az általános használati eseteket és azok paraméter-beállításait mutatja be:

- **Engedélyezze a replikációt a két fürt közötti összes táblán**. Ehhez a forgatókönyvhöz nem szükséges a táblákban meglévő adatok másolása vagy áttelepítése, és nem használja a Phoenix-táblákat. Használja a következő paramétereket:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Replikáció engedélyezése adott táblákon**. A Tábla1, a table2 és a Tábl3 replikációjának engedélyezéséhez használja a következő paramétereket:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Engedélyezze a replikációt bizonyos táblákon, és másolja a meglévőket**. A Tábla1, a table2 és a Tábl3 replikációjának engedélyezéséhez használja a következő paramétereket:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Engedélyezze a replikációt az összes táblán, és a Phoenix-metaadatokat replikálja a forrásról a célhelyre**. A Phoenix-metaadatok replikációja nem tökéletes. Körültekintően használja. Használja a következő paramétereket:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Az Adatmásolás és-Migrálás

Az adatok másolásához és áttelepítéséhez két külön parancsfájl-műveletre van lehetőség a replikáció engedélyezése után:

- [Parancsfájl kis táblákhoz](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (a táblák, amelyek mérete néhány GB, és a teljes másolás várhatóan egy órán belül befejeződik)

- [Parancsfájl nagyméretű táblákhoz](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (a várhatóan egy óránál hosszabb időt vesz igénybe a másolás)

Ugyanezt az eljárást követve megtekintheti a [replikáció engedélyezése](#enable-replication) a parancsfájl-művelet meghívásához című szakaszt. Használja a következő paramétereket:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

A `print_usage()` [szkript](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) szakasza részletes leírást tartalmaz a paraméterekről.

### <a name="scenarios"></a>Forgatókönyvek

- **Adott táblák (test1, teszt2 és test3) másolása a mai napig szerkesztett összes sorhoz (aktuális időbélyeg)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Vagy

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Megadott időtartományba tartozó táblák másolása**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>A replikálás letiltása

A replikáció letiltásához használjon egy másik parancsfájl-műveleti parancsfájlt a [githubról](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Ugyanezt az eljárást követve megtekintheti a [replikáció engedélyezése](#enable-replication) a parancsfájl-művelet meghívásához című szakaszt. Használja a következő paramétereket:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

A `print_usage()` [szkript](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) szakasza részletesen ismerteti a paramétereket.

### <a name="scenarios"></a>Forgatókönyvek

- **Replikáció letiltása az összes táblában**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  vagy

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Replikáció letiltása a megadott táblákon (Tábla1, table2 és Tábl3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Ha törölni kívánja a fürtöt, győződjön meg róla, hogy eltávolítja a forrás-fürt társ-listájából. Ezt úgy teheti meg, hogy a (z) remove_peer "1" parancsot futtatja a hbase-rendszerhéjban. Ha ez nem sikerül, akkor előfordulhat, hogy a fürterőforrás nem működik megfelelően.
>

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan állíthatja be az Apache HBase-replikációt egy virtuális hálózaton belül vagy két virtuális hálózat között. A HDInsight és az Apache HBase kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Ismerkedés az Apache HBase a HDInsight-ben](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase – áttekintés](./apache-hbase-overview.md)
* [Apache HBase-fürtök létrehozása az Azure-ban Virtual Network](./apache-hbase-provision-vnet.md)