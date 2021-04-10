---
title: Az SSH-alagút használata az Azure HDInsight eléréséhez
description: Ismerje meg, hogyan használható az SSH-alagút a Linux-alapú HDInsight-csomópontokon üzemeltetett webes erőforrások biztonságos tallózásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ef7e0450725b456a7fb2b1ab61c50d7edece52ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867559"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Az Apache Ambari webes felhasználói felületének, a JobHistory, a NameNode, az Apache Oozie és más felületének eléréséhez használja az SSH-bújtatást

A HDInsight-fürtök az interneten keresztül biztosítanak hozzáférést az Apache Ambari webes FELÜLETéhez. Egyes funkciókhoz SSH-alagút szükséges. Az Apache Oozie webes FELÜLETe például nem érhető el az interneten keresztül SSH-alagút nélkül.

## <a name="why-use-an-ssh-tunnel"></a>Miért érdemes SSH-alagutat használni?

A Ambari számos menüje csak SSH-alagúton keresztül működik. Ezek a menük a más csomópont-típusokon, például a feldolgozó csomópontokon futó webhelyeken és szolgáltatásokon alapulnak.

Az alábbi webes felülethez SSH-alagút szükséges:

* JobHistory
* NameNode
* Szálak készletei
* Oozie webes felhasználói felület
* Felhasználói felület HBase Master és naplózása

A webszolgáltatások számára elérhető parancsfájl-műveletekkel telepített szolgáltatások esetében SSH-alagútra lesz szükség. A parancsfájl-művelettel telepített színárnyalat megköveteli, hogy az SSH-alagút hozzáférjen a webes felhasználói felülethez.

> [!IMPORTANT]  
> Ha a virtuális hálózaton keresztül közvetlen hozzáférést biztosít a HDInsight, nem kell SSH-alagutakat használnia. Az HDInsight virtuális hálózaton keresztüli közvetlen elérésére példaként tekintse [meg a HDInsight összekapcsolása](connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakört.

## <a name="what-is-an-ssh-tunnel"></a>Mi az SSH-alagút?

A [Secure Shell (SSH) bújtatás](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) a helyi gépen található portot csatlakoztatja a HDInsight egyik fő csomópontjához. A helyi portra továbbított forgalom a fő csomóponttal létesített SSH-kapcsolatban van átirányítva. A rendszer úgy oldja meg a kérést, mintha a fő csomópontról származik. Ekkor a rendszer visszairányítja a választ az alagúton keresztül a munkaállomásra.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy webböngésző, amely SOCKS5 proxy használatára konfigurálható.

    > [!WARNING]  
    > A Windows Internet-beállításokba beépített SOCKS proxy-támogatás nem támogatja a SOCKS5, és nem működik a jelen dokumentumban ismertetett lépésekkel. A következő böngészők a Windows-proxybeállításokat használják, és jelenleg nem működnek a jelen dokumentumban ismertetett lépésekkel:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > A Google Chrome a Windows-proxy beállításain is alapul. Azonban a SOCKS5 támogató bővítményeket is telepíthet. A [FoxyProxy standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)használatát javasoljuk.

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Alagút létrehozása az SSH parancs használatával

Az alábbi parancs használatával hozzon létre egy SSH-alagutat a `ssh` paranccsal. Cserélje le `sshuser` egy SSH-felhasználót a HDInsight-fürtre, és cserélje le a `CLUSTERNAME` nevet a HDInsight-fürt nevére:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Ezzel a paranccsal olyan kapcsolat jön létre, amely a 9876-as helyi portra irányítja át a forgalmat az SSH-val a fürtre. A következő lehetőségek közül választhat:

|Beállítás |Leírás |
|---|---|
|D 9876|Az a helyi port, amely az alagúton keresztül irányítja a forgalmat.|
|C|Tömörítse az összes adatokat, mivel a webes forgalom többnyire szöveg.|
|2|Az SSH-t csak a 2-es verziójú protokoll kipróbálására kényszeríti.|
|q|Csendes mód.|
|T|Tiltsa le a pszeudo-TTY-foglalást, mivel csak egy portot továbbít.|
|p|Az STDIN olvasásának megakadályozása, mert csak egy port továbbítására van lehetőség.|
|N|Ne hajtson végre távoli parancsot, mert éppen csak egy portot továbbít.|
|f|Futtatás a háttérben.|

A parancs befejezése után a rendszer a helyi számítógépen a 9876-es portra eljuttatott forgalmat a fürt fő csomópontjára irányítja.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Alagút létrehozása a PuTTY használatával

A [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty) a Windows rendszerhez készült grafikus SSH-ügyfél. Ha nem ismeri a PuTTY-t, tekintse meg a [Putty dokumentációját](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). A következő lépésekkel hozhat létre SSH-alagutat a PuTTY használatával:

### <a name="create-or-load-a-session"></a>Munkamenet létrehozása vagy betöltése

1. Nyissa meg a PuTTY eszközt, és győződjön meg arról, hogy a bal oldali menüben a **munkamenet** be van jelölve. Ha már mentett egy munkamenetet, válassza ki a munkamenet nevét a **mentett munkamenetek** listából, és válassza a **Betöltés** lehetőséget.

1. Ha még nem rendelkezik mentett munkamenettel, adja meg a kapcsolati adatait:

    |Tulajdonság |Érték |
    |---|---|
    |Állomásnév (vagy IP-cím)|A HDInsight-fürt SSH-címe. Például: **mycluster-ssh.azurehdinsight.net**.|
    |Port|22|
    |Kapcsolattípus|SSH|

1. Válassza a **Mentés** lehetőséget

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png" alt-text="HDInsight-munkamenet létrehozása":::

1. A párbeszédpanel bal oldalán található **Kategória** szakaszban bontsa ki a **kapcsolatok** csomópontot, bontsa ki az **SSH** elemet, majd válassza az **alagutak** lehetőséget.

1. Adja meg az alábbi információkat az **SSH-port továbbítási formáját vezérlő beállításokhoz** :

    |Tulajdonság |Érték |
    |---|---|
    |Forrásport|Az ügyfélen továbbítani kívánt port. Például **9876**.|
    |Cél|A HDInsight-fürt SSH-címe. Például: **mycluster-ssh.azurehdinsight.net**.|
    |Dinamikus|Engedélyezi a dinamikus SOCKS proxy útválasztását.|

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png" alt-text="Putty konfigurációs bújtatási beállítások":::

1. A beállítások hozzáadásához válassza a **Hozzáadás** lehetőséget, majd válassza a **Megnyitás** lehetőséget egy SSH-kapcsolatok megnyitásához.

1. Ha a rendszer kéri, jelentkezzen be a kiszolgálóra.

## <a name="use-the-tunnel-from-your-browser"></a>Az alagút használata a böngészőben

> [!IMPORTANT]  
> Az ebben a szakaszban szereplő lépések a Mozilla FireFox böngészőt használják, mivel ugyanazokat a proxybeállításokat biztosítja az összes platformon. Más modern böngészők, például a Google Chrome, olyan bővítményt igényelhetnek, mint például a FoxyProxy, hogy működjenek az alagúton.

1. Konfigurálja a böngészőt a **localhost** és az alagút a **SOCKS V5** proxyként való létrehozásakor használt port használatára. A Firefox beállításai így néznek ki. Ha a 9876-nál eltérő portot használt, módosítsa a portot a használthoz:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png" alt-text="a Firefox böngésző proxybeállításait":::

   > [!NOTE]  
   > A **távoli DNS** lehetőség kiválasztásával a rendszer a DNS-kérelmeket a HDInsight-fürt használatával oldja fel. Ez a beállítás a fürt fő csomópontjának használatával oldja fel a DNS-t.

2. Ellenőrizze, hogy az alagút működik-e úgy, hogy meglátogat egy webhelyet, például: [https://www.whatismyip.com/](https://www.whatismyip.com/) . A visszaadott IP-címet a Microsoft Azure adatközpontnak kell használnia.

## <a name="verify-with-ambari-web-ui"></a>Ellenőrzés Ambari webes felhasználói felülettel

A fürt létrejötte után a következő lépésekkel ellenőrizheti, hogy elérhető-e a szolgáltatás webes felülete a Ambari web használatával:

1. Nyissa meg a `http://headnodehost:8080` URL-címet a böngészőben. A rendszer az `headnodehost` alagúton keresztül továbbítja a-címeket a fürthöz, és az Ambari-on futó fő csomópontra oldja fel. Ha a rendszer kéri, adja meg a fürt rendszergazdai felhasználónevét és jelszavát. A Ambari webes felhasználói felülete másodszor is kérheti a kérést. Ha igen, adja meg újra az adatokat.

   > [!NOTE]  
   > Ha a `http://headnodehost:8080` címeket a fürthöz való csatlakozáshoz használja, az alagúton keresztül csatlakozik. A kommunikáció a HTTPS-kapcsolat helyett az SSH-alagút használatával védett. Ha HTTPS-kapcsolaton keresztül szeretne csatlakozni az interneten keresztül, akkor a a (z `https://clustername.azurehdinsight.net` ), ahol a a `clustername` fürt neve.

2. A Ambari webes felhasználói felületén válassza a HDFS lehetőséget a lap bal oldalán található listából.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png" alt-text="Apache Ambari hdfs szolgáltatás kiválasztva":::

3. Ha megjelenik a HDFS szolgáltatás adatai, válassza a **gyors hivatkozások** lehetőséget. Megjelenik a fürt fő csomópontjainak listája. Válassza ki az egyik fő csomópontot, majd válassza a **NameNode felhasználói felület** lehetőséget.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png" alt-text="A gyors menü kibontott képe":::

    > [!NOTE]  
    > Ha a __gyors hivatkozások__ lehetőséget választja, akkor a várakozási mutatót is elérheti. Ez az állapot akkor fordulhat elő, ha lassú internetkapcsolattal rendelkezik. Várjon egy percet vagy kettőt, amíg az adatok beérkeznek a kiszolgálóról, majd próbálja megismételni a listát.
    >
    > Előfordulhat, hogy a **gyors hivatkozások** menü egyes bejegyzéseinek kivágása a képernyő jobb oldalán lehetséges. Ha igen, bontsa ki a menüt az egérrel, és a jobbra mutató nyílra kattintva görgessen a képernyő jobb oldalára, és tekintse meg a menü hátralévő részét.

4. A következő képhez hasonló oldal jelenik meg:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png" alt-text="A Hadoop NameNode felhasználói felületének képe":::

    > [!NOTE]  
    > Figyelje meg az oldal URL-címét; a következőhöz hasonlónak kell lennie: `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster` . Ez az URI a csomópont belső teljesen minősített tartománynevét (FQDN) használja, és csak SSH-alagút használata esetén érhető el.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre és használhat egy SSH-alagutat, tekintse meg a következő dokumentumot a Ambari használatának egyéb módjaihoz:

* [HDInsight-fürtök kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
