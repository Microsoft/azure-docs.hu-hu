---
title: Az SSH-bújtatással való eléréséről az Azure HDInsight használata
description: Ismerje meg, hogyan SSH-alagút használatával biztonságosan keresse meg a webes erőforrásokat a Linux-alapú HDInsight-csomóponton.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 943bf0f4bba014c31a11bb30bf8d3b6a7c11a343
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299349"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Az Apache Ambari webes felület, JobHistory, NameNode, az Apache Oozie és egyéb webes eléréséhez használja a SSH-bújtatással

HDInsight-fürtök az Apache Ambari webes Felülettel hozzáférést biztosítanak az interneten keresztül, de egyes szolgáltatásokhoz szükséges egy SSH-alagutat. Például a webes felhasználói felületen az Apache Oozie-szolgáltatás nem érhető el anélkül, hogy az SSh-alagút az interneten keresztül.

## <a name="why-use-an-ssh-tunnel"></a>Miért érdemes használni az SSH-alagút

Az Ambari menük számos csak SSH-alagúton keresztül működik. Ezek a menük weblapok és szolgáltatások más csomóponttípusok, például a feldolgozó csomópontokon futó támaszkodnak.

A következő Web UI SSH-alagút van szükség:

* JobHistory
* nameNode
* Hozzászóláslánc implementációk
* Az Oozie webes felhasználói felületen
* A HBase főkiszolgáló és a naplók felhasználói felület

Ha Parancsfájlműveletek segítségével testre szabhatja a fürt, szolgáltatások vagy segédprogramok telepít, amely egy webes szolgáltatás elérhetővé van szükség az SSH-alagút. Például ha szkriptművelettel Hue telepítése egy SSH-alagutat a eléréséhez a Hue webes felhasználói felületen kell használnia.

> [!IMPORTANT]  
> Ha HDInsight virtuális hálózaton keresztül közvetlen hozzáféréssel rendelkezik, nem kell SSH-alagutak használatára. Közvetlenül a virtuális hálózaton keresztül férnek hozzá a HDInsight egy példa: a [HDInsight csatlakoztatása a helyszíni hálózathoz](connect-on-premises-network.md) dokumentumot.

## <a name="what-is-an-ssh-tunnel"></a>Mi az az SSH-alagút

[Secure Shell (SSH) bújtatás](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) kapcsolódik egy HDInsight fő csomópont egy portot a helyi gépen. A helyi portra irányuló adatforgalmat továbbít egy SSH-kapcsolatot a fő csomópontot. A kérelem oldódik meg, mintha a fő csomópontot származna. A válasz majd vissza a munkaállomáson az alagúton keresztül van átirányítva.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy webes böngésző SOCKS5 proxy használatára konfigurálható.

    > [!WARNING]  
    > A beépített Windows Internetbeállítások SOCKS proxy-támogatás nem támogatja a SOCKS5, és nem működik a jelen dokumentumban leírt lépések. Az alábbi böngészők Windows proxybeállítások támaszkodnak, és jelenleg nem működik a jelen dokumentumban leírt lépések:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome is támaszkodik a Windows proxybeállításokat. Bővítmények, amelyek támogatják a SOCKS5 is telepítheti. Javasoljuk, hogy [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Hozzon létre egy alagutat az SSH-parancs használatával

Hozzon létre ssh-t a következő parancsot bújtatására, segítségével használja a `ssh` parancsot. Cserélje le `sshuser` egy SSH-felhasználót a HDInsight-fürtöt, és cserélje le a `clustername` a HDInsight-fürt nevére:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Ez a parancs létrehoz egy kapcsolatot, amely átirányítja a forgalmat a helyi port 9876 a fürthöz ssh-n keresztül. A következő lehetőségek közül választhat:

* **D 9876** – a helyi port, amely átirányítja a forgalmat az alagúton keresztül.
* **C** -tömörítése minden olyan adat, mert a webes forgalom leginkább szöveget.
* **2** -force SSH protokoll 2. verzió kizárólag kipróbálásához.
* **q** -csendes mód.
* **T** -pszeudo-tty-kiosztás, tiltsa le, mivel csak továbbít egy portot.
* **n** -STDIN, olvasásának megelőzése, mivel csak továbbít egy portot.
* **N** -nem távoli parancs végrehajtására, mivel csak továbbít egy portot.
* **f** -fut a háttérben.

Ha befejezte a parancsot, a helyi számítógépen 9876 portra küldött forgalmat irányítja a rendszer a fürt fő csomópontjának.

## <a name="useputty"></a>Hozzon létre egy alagút a PuTTY használatával

[A puTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) grafikus SSH-ügyfelet a Windows rendszer. Ha nem ismeri a putty-t, tekintse meg a [dokumentáció PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Az alábbi lépések segítségével hozzon létre SSH-alagút a PuTTY használatával:

### <a name="create-or-load-a-session"></a>Nem tölthető be a munkamenet

1. Nyissa meg a putty-kapcsolaton keresztül, és győződjön meg arról **munkamenet** van kiválasztva a bal oldali menüben. Ha egy munkamenet már mentette, válassza ki a munkamenet nevét a **mentett munkamenetek** listájára és válassza ki **terhelés**.

1. Ha még nem rendelkezik egy mentett munkamenetet, adja meg a kapcsolati adatokat:
    * **Host Name (vagy IP-cím)** – az SSH-címe a HDInsight-fürt. Ha például **mycluster-ssh.azurehdinsight.net**
    * **Port** – 22
    * **Kapcsolat típusa** - SSH

1. Válassza ki **mentése**

    ![SSH-munkamenet létrehozása](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Az a **kategória** a párbeszédpanel bal szakaszt, kibontja **kapcsolat**, bontsa ki a **SSH**, majd válassza ki **alagutak**.

1. Adja meg a következő információkat a a **SSH vezérlő beállításokban port átirányítása** űrlapot:
   
   * **Source port** (Forrásport) - Az ügyfélen az a port, amelyet továbbítani szeretne. Ha például **9876**.

   * **Cél** – az SSH-címe a HDInsight-fürt. Például: **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** (Dinamikus) - Lehetővé teszi a dinamikus SOCKS proxy útválasztást.
     
     ![bújtatás beállítások képe](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

1. Válassza ki **Hozzáadás** adja hozzá a beállításokat, majd **nyissa meg a** megnyitásához az SSH-kapcsolatot.

1. Amikor a rendszer kéri, jelentkezzen be a kiszolgálóra.

## <a name="use-the-tunnel-from-your-browser"></a>Használja az alagút a böngészőben

> [!IMPORTANT]  
> A jelen szakaszban ismertetett lépések a Mozilla FireFox böngésző ugyanazokat a proxykiszolgáló-beállításokat biztosít minden platformon használja. Egyéb modern böngészők, például a Google Chrome, például az alagút dolgozhat FoxyProxy bővítmény lehet szükség.

1. Konfigurálja a használatát a böngésző számára **localhost** és használhatók, ha a port, az alagút létrehozása egy **SOCKS v5** proxy. Íme, mi a Firefox beállítások a következőhöz hasonlóak. Ha 9876, mint egy másik portot használta, módosítsa a portot, amelyet használ:
   
    ![a Firefox-beállításai](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > Kiválasztásával **távoli DNS** oldja fel a tartománynévrendszer (DNS) kéréseket a HDInsight-fürt használatával. Ez a beállítás megszünteti a DNS, a fürt fő csomópontjának használatával.

2. Győződjön meg arról, hogy működik-e az alagutat a webhely felkeresésével [ https://www.whatismyip.com/ ](https://www.whatismyip.com/). A visszaadott IP-cím egy használják a Microsoft Azure-adatközpontban kell lennie.

## <a name="verify-with-ambari-web-ui"></a>Az Ambari webes felhasználói felületének ellenőrzéséhez

Miután a fürt létrejött, a következő lépések segítségével győződjön meg arról, hogy az Ambari webes szolgáltatás webes elérhető:

1. A böngészőben nyissa meg `http://headnodehost:8080`. A `headnodehost` címet a fürthöz, és hárítsa el az átjárócsomóponthoz, amelyen fut az Ambari az alagúton keresztül zajlik. Amikor a rendszer kéri, adja meg a rendszergazdai felhasználónevet (rendszergazdai) és a jelszót a fürt számára. Felkérheti másodszor az Ambari webes felhasználói felület által. Ha igen, írja be újra az adatokat.

   > [!NOTE]  
   > Használatakor a `http://headnodehost:8080` oldja meg a fürthöz való csatlakozáshoz, az alagúton keresztül kapcsolódik. Kommunikáció HTTPS helyett az SSH-alagút használatával lett biztonságossá téve. HTTPS-en keresztül az interneten keresztül csatlakozni, használja a `https://clustername.azurehdinsight.net`, ahol `clustername` a fürt neve.

2. Jelölje ki az Ambari webes Kezelőfelületen HDFS az oldal bal oldalán lévő listából.

    ![A HDFS kiválasztva kép](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Amikor az megjelenik a HDFS adatait, válassza ki a **Gyorshivatkozások**. A fürt átjárócsomópontjaiból listája jelenik meg. Válassza ki az egyik fő csomópontot, majd a **NameNode felhasználói felület**.

    ![A témakör Gyorshivatkozását menü kibontva lemezkép](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

    > [!NOTE]  
    > Ha bejelöli __Gyorshivatkozások__, várjon mutató kaphat. Ez az állapot akkor fordulhat elő, ha lassú internetkapcsolattal rendelkezik. Várjon néhány percig is az adatok a kiszolgálótól kapott kell, majd próbálkozzon újra a listában.
    >
    > Az egyes bejegyzéseket a **Gyorshivatkozások** menü előfordulhat, hogy elszigeteli a képernyő jobb oldalán. Ha igen, bontsa ki az egér használatával, és használja a jobbra mutató nyílra, görgessen a képernyő jobb látható a menüben a többi.

4. Az alábbi képhez hasonló lap jelenik meg:

    ![A NameNode felhasználói felület képe](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

    > [!NOTE]  
    > Figyelje meg, hogy ez az oldal URL-címe legyen hasonló `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Ez az URI a csomópont a belső teljesen minősített tartománynevét (FQDN) használja, és csak érhető el, amikor SSH-alagút használatával.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre és használhat SSH-alagút, tekintse meg az Ambari használandó egyéb módjaira vonatkozóan a következő dokumentumot:

* [Az Apache Ambari használatával HDInsight-fürtök kezelése](hdinsight-hadoop-manage-ambari.md)