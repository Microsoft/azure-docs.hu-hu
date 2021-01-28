---
title: InvalidNetworkConfigurationErrorCode-hiba – Azure HDInsight
description: Az Azure HDInsight InvalidNetworkConfigurationErrorCode-beli sikertelen fürtök létrehozásának különböző okai
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926998"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>A fürt létrehozása nem sikerül a InvalidNetworkConfigurationErrorCode az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

Ha `InvalidNetworkConfigurationErrorCode` a "Virtual Network konfiguráció nem kompatibilis a HDInsight-követelménysel" leírásban látható hibakód jelenik meg, általában a fürt [virtuális hálózati konfigurációjával](../hdinsight-plan-virtual-network-deployment.md) kapcsolatos problémát jelez. A hiba további leírása alapján a probléma megoldásához kövesse az alábbi szakaszokat.

## <a name="hostname-resolution-failed"></a>"Az állomásnév feloldása sikertelen"

### <a name="issue"></a>Probléma

A hiba leírása tartalmazza az "állomásnév feloldása sikertelen" kifejezést.

### <a name="cause"></a>Ok

Ez a hiba az egyéni DNS-konfigurációval kapcsolatos problémára mutat. A virtuális hálózaton belüli DNS-kiszolgálók továbbítják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az adott virtuális hálózaton belüli állomásnevek feloldásához (a részletekért lásd: [névfeloldás a virtuális](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) hálózatokban). Az Azure rekurzív feloldóit a virtuális IP-168.63.129.16 keresztül érheti el. Ez az IP-cím csak az Azure-beli virtuális gépekről érhető el. Így nem fog működni, ha helyszíni DNS-kiszolgálót használ, vagy a DNS-kiszolgáló egy olyan Azure-beli virtuális gép, amely nem része a fürt virtuális hálózatának.

### <a name="resolution"></a>Feloldás

1. SSH-t a fürt részét képező virtuális gépre, és futtassa a parancsot `hostname -f` . Ezzel visszaadja a gazdagép teljes tartománynevét (az `<host_fqdn>` alábbi útmutatásnak megfelelően).

1. Ezután futtassa a parancsot `nslookup <host_fqdn>` (például: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Ha a parancs feloldja a nevet egy IP-címhez, az azt jelenti, hogy a DNS-kiszolgáló megfelelően működik. Ebben az esetben az HDInsight-mel támogatási esetet kell megvizsgálnia, és meg fogjuk vizsgálni a problémát. A támogatási esetben adja meg a végrehajtott hibaelhárítási lépéseket. Ez segít a probléma gyorsabb megoldásában.

1. Ha a fenti parancs nem ad vissza IP-címet, futtassa a parancsot `nslookup <host_fqdn> 168.63.129.16` (például: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Ha ez a parancs képes az IP-cím feloldására, az azt jelenti, hogy a DNS-kiszolgáló nem továbbítja a lekérdezést az Azure DNS-be, vagy nem olyan virtuális gép, amely a fürthöz tartozó virtuális hálózat részét képezi.

1. Ha nem rendelkezik olyan Azure-beli virtuális géppel, amely egyéni DNS-kiszolgálóként működhet a fürt virtuális hálózatában, előbb hozzá kell adnia ezt. Hozzon létre egy virtuális GÉPET a virtuális hálózaton, amely DNS-továbbítóként lesz konfigurálva.

1. Miután telepített egy virtuális GÉPET a virtuális hálózatban, konfigurálja a DNS-továbbítási szabályokat ezen a virtuális GÉPEN. Továbbítsa az összes iDNS névfeloldási kérelmet a 168.63.129.16, a többi pedig a DNS-kiszolgálóra. [Íme](../hdinsight-plan-virtual-network-deployment.md) egy példa erre a beállításra egy egyéni DNS-kiszolgálón.

1. Adja hozzá a virtuális gép IP-címét első DNS-bejegyzésként a Virtual Network DNS-konfigurációhoz.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nem sikerült csatlakozni az Azure Storage-fiókhoz"

### <a name="issue"></a>Probléma

A hiba leírása a következőt tartalmazza: "nem sikerült csatlakozni az Azure Storage-fiókhoz" vagy "nem sikerült csatlakozni az Azure SQL-hez".

### <a name="cause"></a>Ok

Az Azure Storage és az SQL nem rendelkezik rögzített IP-címmel, ezért engedélyeznie kell a kimenő kapcsolatokat az összes IP-címhez a szolgáltatások elérésének engedélyezéséhez. A megoldás pontos lépései attól függnek, hogy van-e beállítva hálózati biztonsági csoport (NSG) vagy User-Defined-szabály (UDR). A konfigurációk részletes ismertetését lásd: a [hálózati forgalom szabályozása hálózati biztonsági csoportokkal és felhasználó által megadott útvonalakkal HDInsight](../control-network-traffic.md) .

### <a name="resolution"></a>Feloldás

* Ha a fürt [hálózati biztonsági csoportot (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)használ.

    Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **kimenő biztonsági szabályok** szakaszban korlátozás nélkül engedélyezheti az internetre irányuló kimenő hozzáférést (vegye figyelembe, hogy a kisebb **prioritási** szám nagyobb prioritást jelent). Emellett az **alhálózatok** szakaszban ellenőrizze, hogy a NSG alkalmazva van-e a fürt alhálózatára.

* Ha a fürt [felhasználó által definiált útvonalakat (UDR)](../../virtual-network/virtual-networks-udr-overview.md)használ.

    Nyissa meg a Azure Portal, és azonosítsa a fürt üzembe helyezéséhez használt alhálózathoz társított útválasztási táblázatot. Miután megtalálta az alhálózat útválasztási táblázatát, ellenőrizze az **útvonalak** szakaszt.

    Ha vannak meghatározott útvonalak, ellenőrizze, hogy vannak-e útvonalak az IP-címekhez ahhoz a régióhoz, ahol a fürtöt telepítették, és az egyes útvonalak **NextHopType** az **Internet**. A fenti cikkben leírt minden szükséges IP-címhez meg kell adni egy útvonalat.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Nem sikerült kimenő kapcsolatot létesíteni a fürtből a HDInsight erőforrás-szolgáltatóval folytatott kommunikációhoz. Győződjön meg arról, hogy a kimenő kapcsolat engedélyezve van. "

### <a name="issue"></a>Probléma

A hiba leírása a következőt tartalmazza: "nem sikerült létrehozni a kimenő kapcsolatot a fürtből a HDInsight erőforrás-szolgáltatóval folytatott kommunikációhoz. Győződjön meg arról, hogy a kimenő kapcsolat engedélyezve van. "

### <a name="cause"></a>Ok

Privát csatolt HDInsight-fürtök használatakor a fürt kimenő hozzáférését úgy kell konfigurálni, hogy lehetővé tegye a kapcsolódást a HDInsight erőforrás-szolgáltatóhoz.

### <a name="resolution"></a>Feloldás

* A probléma megoldásához tekintse meg a HDInsight Private link telepítési lépéseit a [privát hivatkozás](../hdinsight-private-link.md) beállítása című témakörben.
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A virtuális hálózat konfigurációja nem kompatibilis a HDInsight-követelményekkel"

### <a name="issue"></a>Probléma

A hibák leírása a következőhöz hasonló üzeneteket tartalmaz:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Ok

Valószínűleg probléma van az egyéni DNS-beállítással.

### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a 168.63.129.16 az egyéni DNS-láncban van-e. A virtuális hálózaton belüli DNS-kiszolgálók továbbítják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az adott virtuális hálózaton belüli állomásnevek feloldásához. További információ: [névfeloldás a virtuális hálózatokban](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Az Azure rekurzív feloldóit a virtuális IP-168.63.129.16 keresztül érheti el.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Futtassa a következő parancsot:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Ennek nagyjából a következőképpen kell kinéznie:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Az eredmény alapján – a következő lépések egyikének kiválasztásával követheti el:

#### <a name="1686312916-is-not-in-this-list"></a>a 168.63.129.16 nem szerepel a listában

**1\. lehetőség**  
Adja hozzá a 168.63.129.16-t a virtuális hálózat első egyéni DNS-ként az [Azure HDInsight virtuális hálózatának megtervezése](../hdinsight-plan-virtual-network-deployment.md)című témakörben ismertetett lépések alapján. Ezek a lépések csak akkor érvényesek, ha az egyéni DNS-kiszolgáló Linux rendszeren fut.

**2\. lehetőség**  
Helyezzen üzembe egy DNS-kiszolgálói virtuális GÉPET a virtuális hálózathoz. Ez a következő lépéseket foglalja magában:

* Hozzon létre egy virtuális GÉPET a virtuális hálózaton, amely DNS-továbbítóként lesz konfigurálva (ez lehet Linux vagy Windows rendszerű virtuális gép).
* Konfigurálja a DNS-továbbítási szabályokat ezen a virtuális gépen (az összes iDNS névfeloldási kérelem továbbítása a 168.63.129.16, a többi pedig a DNS-kiszolgálóra).
* Adja hozzá a virtuális gép IP-címét az első DNS-bejegyzésként Virtual Network DNS-konfigurációhoz.

#### <a name="1686312916-is-in-the-list"></a>a 168.63.129.16 szerepel a listán

Ebben az esetben hozzon létre egy támogatási esetet a HDInsight, és vizsgáljuk meg a problémát. Adja meg az alábbi parancsok eredményét a támogatási esetben. Ez segít a probléma gyorsabb kivizsgálásában és megoldásában.

A fő csomóponton egy SSH-munkamenetből szerkessze és futtassa a következőt:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Ok

A hibakód másik oka `InvalidNetworkConfigurationErrorCode` lehet az elavult paraméter használata a `EnableVmProtection` PowerShellben vagy egy Azure-Runbook.

### <a name="resolution"></a>Feloldás

Használja az érvényes paramétereket az `Get-AzVirtualNetwork` az [PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork) -ban leírt módon

---

### <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
