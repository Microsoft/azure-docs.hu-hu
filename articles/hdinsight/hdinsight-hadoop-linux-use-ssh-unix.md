---
title: Az SSH használata a Hadoop–Azure HDInsight rendszerben
description: A HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum a Windows-, Linux-, UNIX-vagy macOS-ügyfelekről származó SSH-parancsokkal történő HDInsight-csatlakozással kapcsolatos információkat tartalmaz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: a8ad0c787235b94a600d14a3b447bbfee052321b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864856"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val

Megtudhatja, hogyan használhatja a [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) szolgáltatást az Azure HDInsight Apache Hadoop való biztonságos kapcsolódáshoz. További információ a virtuális hálózatokon keresztüli csatlakozásról: [Azure HDInsight Virtual Network Architecture](./hdinsight-virtual-network-architecture.md). Lásd még: [virtuális hálózat központi telepítésének megtervezése az Azure HDInsight-fürtökhöz](./hdinsight-plan-virtual-network-deployment.md).

A következő táblázat a HDInsight SSH-ügyféllel való csatlakozásakor szükséges címeket és portokat tartalmazza:

| Cím | Port | A következőhöz csatlakozik: |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Elsődleges átjárócsomópont |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Másodlagos átjárócsomópont |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Edge-csomópont (ML szolgáltatások a HDInsight-on) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | peremhálózati csomópont (bármely más fürt típusa, ha van egy peremhálózati csomópont) |

Cserélje le a `<clustername>` elemet a fürt nevére. Cserélje le az `<edgenodename>` elemet az élcsomópont nevére.

Ha a fürt élcsomópontot tartalmaz, ajánlott __az élcsomóponthoz mindig__ az SSH segítségével kapcsolódni. Az átjárócsomópontok olyan szolgáltatásokat futtatnak, amelyek kritikus fontosságúak a Hadoop állapota szempontjából. Az élcsomópont csak azt futtatja, amit Ön telepít rá. Az élcsomópontok használatával kapcsolatban további információért lásd: [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Amikor első alkalommal csatlakozik a HDInsighthoz, az SSH-ügyfél olyan figyelmeztetést jeleníthet meg, amely szerint a gazdaszámítógép nem hitelesíthető. Amikor a rendszer erre felkéri, válassza a „yes” (igen) lehetőséget ahhoz, hogy a gazdaszámítógép felvegye az SSH-ügyfél megbízható kiszolgálókat tartalmazó listába.
>
> Ha korábban már csatlakozott egy ilyen nevű kiszolgálóhoz, előfordulhat, hogy a rendszer figyelmezteti rá, hogy a tárolt gazdaszámítógép-kulcs nem egyezik meg a kiszolgáló gazdaszámítógép-kulcsával. A kiszolgálónévhez tartozó, meglévő bejegyzés eltávolításához tekintse meg az SSH-ügyfél dokumentációját.

## <a name="ssh-clients"></a>SSH-ügyfelek

Az `ssh` és `scp` parancs elérhető a Linux, Unix és macOS rendszerekben. Az `ssh`-ügyfelet általában arra használják, hogy távoli parancssori munkamenetet hozzon létre Linux vagy Unix rendszerben. Az `scp`-ügyfél segítségével biztonságosan másolhat fájlokat a saját ügyfél és a távoli rendszer között.

A Microsoft Windows alapértelmezés szerint nem telepít SSH-ügyfeleket. Az `ssh`- és az `scp`-ügyfél az alábbi csomagokban érhető el a Windows rendszerhez:

* [OpenSSH-ügyfél](/windows-server/administration/openssh/openssh_install_firstuse). Ez az ügyfél egy opcionális funkció, amely a Windows 10 Fall Creators Update-ben lett bevezetve.

* [Bash on Ubuntu on Windows 10](/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). A Cloud Shell egy bash-környezetet biztosít a böngészőben.

* [Git](https://git-scm.com/).

Több grafikus SSH-ügyfél is van, például a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) és a [MobaXterm](https://mobaxterm.mobatek.net/). Bár ezek az ügyfelek használhatók a HDInsighthoz történő kapcsolódáshoz, a kapcsolódás folyamata más, mint az `ssh` segédprogram használatakor. További információkért tekintse meg a használt grafikus ügyfél dokumentációját.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Hitelesítés: SSH-kulcsok

Az SSH-kulcsok [nyilvános kulcsú kriptográfia](https://en.wikipedia.org/wiki/Public-key_cryptography) használatával hitelesítik az SSH-munkameneteket. Az SSH-kulcsok biztonságosabbak a jelszavaknál, és egyszerű módszert kínálnak a Hadoop-fürt biztonságos elérésére.

Ha az SSH-fiókja kulccsal van védve, az ügyfélnek meg kell adnia az egyező titkos kulcsot, amikor csatlakozik:

* A legtöbb ügyfél konfigurálható __alapértelmezett kulcs__ használatára. Az `ssh`-ügyfél például a titkos kulcsot a `~/.ssh/id_rsa` helyen keresi Linux- és Unix-környezetekben.

* Megadhatja __egy titkos kulcs elérési útját__. Az `ssh`-ügyfél esetében az `-i` paraméterrel adható meg a titkos kulcs elérési útja. Például: `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Ha __több titkos kulccsal__ rendelkezik, amelyeket különböző kiszolgálókhoz használ, fontolja meg az olyan segédprogramok használatát, mint például az [ssh-ügynök (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Az `ssh-agent` segédprogram segítségével automatikusan választhatja ki a kulcsot az SSH-munkamenet megkezdéséhez.

> [!IMPORTANT]  
> Ha jelszóval védi a titkos kulcsot, a kulcs használatakor be kell írnia a jelszót. Az `ssh-agent` és hasonló segédprogramokkal a kényelmes használat érdekében gyorsítótárazhatja a jelszót.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az `ssh-keygen` paranccsal hozhat létre nyilvános- és titkoskulcs-fájlokat. A következő parancs egy 2048 bites RSA-kulcspárt hoz létre, amely a HDInsighttal használható:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

A rendszer a kulcs létrehozási folyamata során kér információt. Például a kulcsok tárolási helyét kell megadnia, vagy azt, hogy használ-e jelszót. A folyamat befejezése után két fájl jön létre; egy nyilvános kulcs és egy titkos kulcs.

* A __nyilvános kulccsal__ hozható létre a HDInsight-fürt. A nyilvános kulcs kiterjesztése `.pub`.

* A __titkos kulccsal__ hitelesíthető az ügyfél a HDInsight-fürtön.

> [!IMPORTANT]  
> A kulcsokat jelszóval védheti. Ez a jelszó lényegében egy kód a titkos kulcson. Ilyen esetekben még ha valaki meg is szerzi a titkos kulcsát, a jelszóra is szüksége van a kulcs használatához.

### <a name="create-hdinsight-using-the-public-key"></a>HDInsight létrehozása a nyilvános kulccsal

| Létrehozási metódus | A nyilvános kulcs használata |
| ------- | ------- |
| Azure Portal | Törölje __a fürt bejelentkezési jelszavának használata SSH__-hoz beállítást, majd válassza a __nyilvános kulcs__ lehetőséget az SSH-hitelesítési típusként. Végül válassza ki a nyilvános kulcs fájlját, vagy illessze be a fájl szöveges tartalmát a __Nyilvános SSH-kulcs__ mezőbe.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="Nyilvános SSH-kulcs párbeszédpanel a HDInsight-fürt létrehozásakor"::: |
| Azure PowerShell | Használja a `-SshPublicKey` [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag paraméterét, és adja át a nyilvános kulcs tartalmát karakterláncként.|
| Azure CLI | Használja a `--sshPublicKey` parancs paraméterét [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) , és adja át a nyilvános kulcs tartalmát karakterláncként. |
| Resource Manager-sablon | Az SSH-kulcsok sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) fájlban lévő `publicKeys` elemmel illeszthetők be a kulcsok az Azure-ba a fürt létrehozásakor. |

## <a name="authentication-password"></a>Hitelesítés: Jelszó

Az SSH-fiókok jelszóval védhetők. Ha SSH-val csatlakozik a HDInsight-hez, a rendszer kéri, hogy adja meg a jelszót.

> [!WARNING]  
> A Microsoft nem javasolja jelszavas hitelesítés használatát az SSH-hoz. A jelszavakat ki lehet találni, és védtelenek a találgatásos támadásokkal szemben. Ehelyett azt javasoljuk, hogy használjon [SSH-kulcsokat a hitelesítéshez](#sshkey).

> [!IMPORTANT]  
> Az SSH-fiók jelszava 70 nappal a HDInsight-fürt létrehozása után jár le. Ha a jelszó lejár, a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentumban foglaltak szerint változtathatja meg.

### <a name="create-hdinsight-using-a-password"></a>HDInsight létrehozása jelszóval

| Létrehozási metódus | Jelszó megadása |
| --------------- | ---------------- |
| Azure Portal | Alapértelmezés szerint az SSH-felhasználói fióknak ugyanaz a jelszava, mint a fürt bejelentkezési fiókjának. Ha más jelszót szeretne használni, törölje a __fürt bejelentkezési jelszavának használata SSH__-hoz lehetőséget, majd írja be a jelszót az __SSH-jelszó__ mezőbe.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="SSH-jelszó párbeszédpanel a HDInsight-fürt létrehozásakor":::|
| Azure PowerShell | Használja a `--SshCredential` [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag paraméterét, és adjon át egy olyan `PSCredential` objektumot, amely tartalmazza az SSH-felhasználói fiók nevét és jelszavát. |
| Azure CLI | Használja a `--ssh-password` parancs paraméterét [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) , és adja meg a jelszó értékét. |
| Resource Manager-sablon | A jelszavak sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) fájlban lévő `linuxOperatingSystemProfile` elemmel illeszthető be az SSH-fióknév és -jelszó az Azure-ba a fürt létrehozásakor.|

### <a name="change-the-ssh-password"></a>Az SSH-jelszó módosítása

Az SSH-felhasználói fiók jelszavának módosításával kapcsolatos információért tekintse meg a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentum __Jelszavak módosítása__ szakaszát.

## <a name="authentication-domain-joined-hdinsight"></a>Hitelesítés tartományhoz csatlakoztatott HDInsight

Ha __tartományhoz csatlakoztatott HDInsight-fürtöt__ használ, a `kinit` parancsot az SSH helyi felhasználóval való csatlakozás után kell használnia. Ez a parancs tartományi felhasználónevet és jelszót kér, és hitelesíti a munkamenetet a fürttel társított Azure Active Directory-tartománnyal.

A Kerberos-hitelesítést minden tartományhoz csatlakoztatott csomóponton (például a főcsomóponton, a peremhálózati csomóponton) is engedélyezheti az SSH-hoz a tartományi fiók használatával. Ehhez szerkessze az sshd config fájlt:

```bash
sudo vi /etc/ssh/sshd_config
```

távolítsa el a megjegyzéseket, a `KerberosAuthentication` értékét pedig állítsa a következőre: `yes`

```bash
sudo service sshd restart
```

`klist`A parancs használatával ellenőrizze, hogy a Kerberos-hitelesítés sikeres volt-e.

További információkat itt talál: [Tartományhoz csatlakoztatott HDInsight konfigurálása](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-nodes"></a>Csatlakozás csomópontokhoz

A fő csomópontok és a peremhálózati csomópont (ha van ilyen) az interneten keresztül érhető el a 22-es és a 23-as porton.

* Az __átjárócsomópontokhoz__ való csatlakozás során az elsődleges átjárócsomóponthoz való csatlakozáshoz használja a __22-es__ portot, a másodlagos átjárócsomóponthoz való csatlakozáshoz pedig a __23-as__ portot. A használandó teljes tartománynév a `clustername-ssh.azurehdinsight.net`, ahol a `clustername` a fürt neve.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Az __élcsomóponthoz__ való csatlakozáskor használja a 22-es portot. A teljes tartománynév az `edgenodename.clustername-ssh.azurehdinsight.net`, ahol az `edgenodename` az élcsomópont létrehozásakor megadott név. A `clustername` a fürt neve.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Az előző példák azt feltételezik, hogy jelszavas hitelesítést használ, vagy automatikus tanúsítványalapú hitelesítés történik. Ha SSH-kulcspárt használ a hitelesítéshez, és a tanúsítvány használata nem automatikus, az `-i` paraméterrel adja meg a titkos kulcsot. Például: `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

A csatlakozást követően a Rákérdezés azt jelzi, hogy az SSH-Felhasználónév és a csomópont, amelyhez csatlakozik. Ha például az `sshuser` felhasználóként csatlakozik az elsődleges átjárócsomóponthoz, a parancssor az `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Kapcsolódás a Worker és az Apache Zookeeper-csomópontokhoz

A munkavégző csomópontok és a Zookeeper-csomópontok nem érhetők el közvetlenül az internetről. hanem a fürt átjáró- vagy élcsomópontjain keresztül érhetők el. A következő általános lépésekkel csatlakozhat más csomópontokhoz:

1. Az SSH-val csatlakozzon egy átjáró- vagy élcsomóponthoz:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Az átjáró- vagy élcsomópont felé fennálló SSH-kapcsolatból használja az `ssh` parancsot a fürt egyik munkavégző csomópontjához való csatlakozáshoz:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    A csomópontok neveinek listájának lekéréséhez tekintse meg a [HDInsight kezelése az Apache Ambari REST API dokumentum használatával](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) című témakört.

Ha az SSH-fiókot __jelszó__ védi, a kapcsolódáshoz adja meg a jelszót.

Ha az SSH-fiókot __SSH-kulcsok__ védik, győződjön meg róla, hogy az SSH-továbbítás engedélyezve van az ügyfélen.

> [!NOTE]  
> A fürtben lévő összes csomópont közvetlen elérésének másik módja, ha a HDInsightot Azure virtuális hálózatra telepíti. Ezután csatlakoztathatja a távoli gépet ugyanehhez a virtuális hálózathoz, és közvetlenül érheti el a fürtben lévő összes csomópontot.
>
> További információ: [virtuális hálózat megtervezése a HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>SSH-ügynöktovábbítás konfigurálása

> [!IMPORTANT]  
> A következő lépések Linux vagy UNIX-alapú rendszert feltételeznek, és a Windows 10-en futó Bash-környezet esetén működnek. Ha a lépések nem működnek a rendszerén, lehet, hogy át kell tekintenie az SSH-ügyfél dokumentációját.

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a parancssoron az `touch ~/.ssh/config` karakterlánc beírásával.

2. Adja hozzá a következő szöveget az `config` fájlhoz.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Cserélje le a __Gazda__ információit azon csomópont címére, amelyet SSH-val csatlakoztat. Az előző példa az élcsomópontot használja. Ez a bejegyzés konfigurálja az SSH-ügynöktovábbítást az adott csomópont számára.

3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Ha a rendszer nem ad vissza semmit, akkor `ssh-agent` nem fut. További tudnivalókért lásd az ügynök indítási szkriptjeire vonatkozó részt a [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Az ssh-agent és az ssh együttes használata) című cikkben vagy az SSH-ügyfél dokumentációját.

4. Miután meggyőződött róla, hogy az **ssh-agent** fut, az alábbi lépésekkel adja hozzá az SSH titkos kulcsot az ügynökhöz:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Ha a titkos kulcsot egy másik fájl tárolja, a `~/.ssh/id_rsa` részt cserélje ki a fájl elérési útjára.

5. Csatlakozzon a fürt élcsomópontjához vagy átjárócsomópontjaihoz SSH-val. Ezután az SSH-paranccsal csatlakozzon egy feldolgozó vagy Zookeeper-csomóponthoz. A kapcsolat létrejön a továbbított kulccsal.

## <a name="next-steps"></a>Következő lépések

* [SSH-alagútkezelés használata a HDInsighttal](hdinsight-linux-ambari-ssh-tunnel.md)
* [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [SCP használata a HDInsighttal](./use-scp.md)