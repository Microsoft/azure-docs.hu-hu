---
title: Telepítse az Azure Application konzisztens pillanatkép eszközt a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz telepítéséhez, amelyet a Azure NetApp Files használhat.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869191"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Az Azure Application konzisztens pillanatkép-eszköz (előzetes verzió) telepítése

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz telepítéséhez, amelyet a Azure NetApp Files használhat.

## <a name="introduction"></a>Bevezetés

A letölthető önálló telepítő úgy lett kialakítva, hogy a pillanatkép-eszközök könnyen állíthatók be és futtathatók legyenek a nem gyökérszintű felhasználói jogosultságokkal (például azacsnap). A telepítő beállítja a felhasználót, és a pillanatkép-eszközöket a felhasználók `$HOME/bin` alkönyvtárba helyezi (alapértelmezés = `/home/azacsnap/bin` ).
Az öntelepítő megkísérli meghatározni az összes fájl helyes beállításait és elérési útját a telepítést végző felhasználó konfigurációja alapján (például root). Ha az előfeltételként szükséges lépések (a Storage és a SAP HANA közötti kommunikáció engedélyezése) root-ként lettek futtatva, a telepítés a titkos kulcsot és `hdbuserstore` a biztonsági mentési felhasználó helyét fogja másolni. Azonban lehetséges, hogy a tároló háttérrel való kommunikációt engedélyező lépések és SAP HANA a telepítés után egy hozzáértő rendszergazda manuálisan elvégezhető.

## <a name="prerequisites-for-installation"></a>A telepítés előfeltételei

Kövesse az irányelveket a pillanatképek és a vész-helyreállítási parancsok beállításához és végrehajtásához. A pillanatkép-eszközök telepítése és használata előtt ajánlott a következő lépések legfelső szintűként való végrehajtása.

1. **Az operációs rendszer javította**: az Azure-ban [SAP HANA (nagyméretű példányok) telepítésével és konfigurálásával](../virtual-machines/workloads/sap/hana-installation.md#operating-system)kapcsolatban lásd a javítás és az SMT telepítőjét.
1. **Az idő szinkronizálása be van állítva**. Az ügyfélnek meg kell adnia egy NTP-kompatibilis időkiszolgálót, és ennek megfelelően kell konfigurálnia az operációs rendszert.
1. A **Hana telepítve van** : Hana telepítési utasítások a Hana- [adatbázison az SAP NetWeaver telepítésével](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)kapcsolatban.
1. A Storage szolgáltatással való **[kommunikáció engedélyezése](#enable-communication-with-storage)** (további részletekért tekintse meg a különálló szakaszt): az ügyfélnek be kell ÁLLÍTANIA az SSH-t egy privát/nyilvános kulcspár használatával, és meg kell adnia a nyilvános kulcsot minden olyan csomópont számára, ahol a pillanatkép-eszközöket a rendszer a tároló háttérbe állításához szükséges Microsoft-műveletekhez hajtja végre.
   1. **Azure NetApp Files esetén (a részletekért tekintse meg a külön szakaszt)**: az ügyfélnek az egyszerű szolgáltatásnév hitelesítési fájlját kell előkészítenie.
   1. **Azure-beli nagyméretű példány esetén (a részletekért tekintse meg a külön szakaszt)**: az ügyfélnek be kell ÁLLÍTANIA az SSH-t egy magán-és nyilvános kulcspár használatával, és meg kell adnia a nyilvános kulcsot minden olyan csomóponthoz, ahol a pillanatkép-eszközöket a rendszer a tároló háttérbe állításához szükséges Microsoft-műveletekhez hajtja végre.

      Tesztelje ezt úgy, hogy az SSH használatával csatlakozik az egyik csomóponthoz (például: `ssh -l <Storage UserName> <Storage IP Address>` ).
      Írja be `exit` a tárolási kérdés kijelentkezését.

      A Microsoft Operations a kiépítés időpontjában megadja a Storage-felhasználó és a tároló IP-címét.
  
1. **[Kommunikáció engedélyezése SAP HANAsal](#enable-communication-with-sap-hana)** (további részletekért lásd a különálló szakaszt): az ügyfélnek be kell állítania egy megfelelő SAP HANA felhasználót a pillanatkép végrehajtásához szükséges jogosultságokkal.
   1. Ezt a beállítást a parancssorból a következő szöveg használatával lehet tesztelni: `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - A fenti példák a SAP HANA való nem SSL-kommunikációra vonatkoznak.

## <a name="enable-communication-with-storage"></a>Kommunikáció engedélyezése a Storage szolgáltatással

Ez a szakasz azt ismerteti, hogyan engedélyezhető a kommunikáció a Storage szolgáltatással.

### <a name="azure-netapp-files"></a>Azure NetApp Files

RBAC egyszerű szolgáltatás létrehozása

1. Azure Cloud Shell-munkameneten belül ellenőrizze, hogy az előfizetésben be van-e jelentkezve, és alapértelmezés szerint az egyszerű szolgáltatáshoz kívánja társítani:

    ```azurecli-interactive
    az account show
    ```

1. Ha az előfizetés nem megfelelő, használja a

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Az alábbi példa alapján hozzon létre egy egyszerű szolgáltatást az Azure CLI használatával

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Ennek a következő példához hasonló kimenetet kell előállítania:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Ez a parancs az előfizetés szintjén automatikusan hozzárendeli az RBAC közreműködői szerepkört az egyszerű szolgáltatáshoz, leszűkítheti a hatókört az adott erőforráscsoporthoz, ahol a tesztek létrehozzák az erőforrásokat.

1. Kivágja és beilleszti a kimeneti tartalmat egy nevű fájlba, amely a `azureauth.json` paranccsal azonos rendszeren található `azacsnap` , és a megfelelő rendszerengedélyekkel biztosítja a fájl védelmét.

### <a name="azure-large-instance"></a>Nagyméretű Azure-példány

A tároló háttérbeli kommunikációja egy titkosított SSH-csatornán végezhető el. A következő példa az SSH beállításához nyújt útmutatást a kommunikációhoz.

1. A `/etc/ssh/ssh_config` fájl módosítása

    Tekintse át a következő kimenetet, ahol a `MACs hmac-sha1` sor hozzá lett adva:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Privát/nyilvános kulcspár létrehozása

    A következő példában szereplő parancs használatával hozza létre a kulcspárt, ne adjon meg jelszót a kulcs létrehozásakor.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. A nyilvános kulcs elküldése a Microsoft műveleteinek

    Küldje el a parancs kimenetét `cat /root/.ssh/id_rsa.pub` (például alább) a Microsoft-műveletekhez, hogy a pillanatkép-eszközök kommunikálni tudjanak a tárolási alrendszerrel.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Kommunikáció engedélyezése SAP HANA

A pillanatkép-eszközök kommunikálnak a SAP HANAokkal, és szükségük van egy megfelelő engedélyekkel rendelkező felhasználóra az adatbázis mentési pontjának elindításához és felszabadításához. A következő példa a SAP HANA v2 felhasználó telepítését és a `hdbuserstore` SAP HANA adatbázishoz való kommunikációt mutatja be.

A következő példában szereplő parancsok egy felhasználót (AZACSNAP) állítanak be a SYSTEMDB SAP HANA 2.
adatbázis, szükség szerint módosítsa az IP-címet, a felhasználóneveket és a jelszavakat:

1. Kapcsolódás a SYSTEMDB a felhasználó létrehozásához

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. A felhasználó létrehozása

    Ez a példa a AZACSNAP-felhasználót hozza létre a SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Felhasználói engedélyek megadása

    Ez a példa azt az engedélyt állítja be, hogy a AZACSNAP-felhasználó engedélyezze az adatbázis-konzisztens tárolási pillanatkép elvégzését.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. Nem *kötelező* – megakadályozza a felhasználó jelszavának lejáratát

    > [!NOTE]
    > A módosítás előtt érdeklődjön a vállalati szabályzatban.

   Ez a példa letiltja a jelszó lejáratát a AZACSNAP felhasználó számára, anélkül, hogy ez a változás a felhasználó jelszava le fog járni, a pillanatképek megfelelővé tételének megakadályozása.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. A SAP HANA biztonságos felhasználói tároló beállítása (a jelszó módosítása) Ez a példa a `hdbuserstore` Linux-rendszerhéjból származó parancsot használja a SAP HANA biztonságos felhasználói tároló beállításához.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Ellenőrizze a biztonságos felhasználói tároló SAP HANAét, és ellenőrizze, hogy helyesen van-e beállítva a biztonságos felhasználó tárolója, a `hdbuserstore` paranccsal listázhatja az alábbi példához hasonló kimenetet. A használatával kapcsolatos további részletek `hdbuserstore` az SAP webhelyén érhetők el.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Az SSL használata a SAP HANAsal való kommunikációhoz

Az `azacsnap` eszköz SAP HANA `hdbsql` parancsát használja a SAP HANA való kommunikációhoz. Ez magában foglalja az SSL-beállítások használatát a SAP HANAsal való kommunikáció titkosításakor. `azacsnap``hdbsql`a a parancs SSL-beállításait használja az alábbiak szerint.

A következő beállítás használatakor a rendszer mindig a következőket használja `azacsnap --ssl` :

- `-e` – Engedélyezi a TLS-encryptionTLS/SSL-titkosítást. A kiszolgáló a legmagasabb rendelkezésre állást választja.
- `-ssltrustcert` -Megadja, hogy érvényesítse-e a kiszolgáló tanúsítványát.
- `-sslhostnameincert "*"` – A kiszolgáló identitásának ellenőrzéséhez használt állomásnév megadása. Ha állomásnévként adja meg a nevet `"*"` , akkor a kiszolgáló állomásneve nincs érvényesítve.

Az SSL-kommunikációhoz a Key Store és a Trust Store fájlok is szükségesek.  Habár lehetséges, hogy ezek a fájlok a Linux-telepítés alapértelmezett helyein tárolódnak, annak biztosítása érdekében, hogy a különböző SAP HANA rendszerek esetében a megfelelő lényeges anyagokat használják (azaz abban az esetben, ha az egyes SAP HANA rendszerekhez különböző kulcstároló-és megbízhatósági tároló-fájlokat használnak), a a `azacsnap` `securityPath` konfigurációs fájlban megadott helyen tárolja a kulcs-és megbízhatósági tároló fájljait `azacsnap` .

#### <a name="key-store-files"></a>Key Store-fájlok

- Ha ugyanazokkal a kulcsfontosságú anyagokkal több SID-t használ, könnyebben hozhatók létre hivatkozások a securityPath-helyre a `azacsnap` konfigurációs fájlban meghatározottak szerint.  Győződjön meg arról, hogy ezek az értékek az SSL protokollt használó minden SID-ben léteznek.
  - OpenSSL esetén:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Commoncrypto esetén:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Ha SID-ként több SID-t használ a kulcshoz, másolja (vagy helyezze át és nevezze át) a fájlokat a securityPath helyre a SID- `azacsnap` konfigurációs fájlban meghatározott módon.
  - OpenSSL esetén:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Commoncrypto esetén:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

`azacsnap`A hívások `hdbsql` `-sslkeystore=<securityPath>/<SID>_keystore` a parancssorban lesznek hozzáadva.

#### <a name="trust-store-files"></a>Megbízhatósági tároló fájljai

- Ha több SID-t használ ugyanazzal a fontos anyaggal, a konfigurációs fájlban meghatározott módon hozzon létre rögzített hivatkozásokat a securityPath helyre `azacsnap` .  Győződjön meg arról, hogy ezek az értékek az SSL protokollt használó minden SID-ben léteznek.
  - OpenSSL esetén:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Commoncrypto esetén:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Ha SID-ként több SID-t használ a kulcshoz, másolja (vagy helyezze át és nevezze át) a fájlokat a securityPath helyre a SID- `azacsnap` konfigurációs fájlban meghatározott módon.
  - OpenSSL esetén:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Commoncrypto esetén:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> A `<SID>` fájlnevek összetevőnek a SAP HANA rendszerazonosítónak kell lennie az összes nagybetűvel (például `H80` , `PR1` stb.).

`azacsnap`A hívások `hdbsql` `-ssltruststore=<securityPath>/<SID>_truststore` a parancssorban lesznek hozzáadva.

Éppen ezért, `azacsnap -c test --test hana --ssl openssl` Ha a `SID` a `H80` konfigurációs fájlban található, a következő módon futtatja a `hdbsql` kapcsolatokat:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> A `\` karakter egy parancssori sortörés a parancssorban átadott több paraméter érthetőségének javításához.

## <a name="installing-the-snapshot-tools"></a>A pillanatkép-eszközök telepítése

A letölthető önálló telepítő úgy lett kialakítva, hogy a pillanatkép-eszközök könnyen állíthatók be és futtathatók legyenek a nem gyökérszintű felhasználói jogosultságokkal (például azacsnap). A telepítő beállítja a felhasználót, és a pillanatkép-eszközöket a felhasználók `$HOME/bin` alkönyvtárba helyezi (alapértelmezés = `/home/azacsnap/bin` ).

Az öntelepítő megkísérli meghatározni az összes fájl helyes beállításait és elérési útját a telepítést végző felhasználó konfigurációja alapján (például root). Ha az előző telepítési lépések (a Storage és a SAP HANA közötti kommunikáció engedélyezése) root-ként lettek futtatva, akkor a telepítés a titkos kulcsot és a `hdbuserstore` biztonsági mentést végző felhasználó helyét fogja másolni. Azonban előfordulhat, hogy a tároló háttér-és SAP HANA segítségével végzett kommunikációt engedélyező lépéseket a telepítés után egy hozzáértő rendszergazda manuálisan el tudja végezni.

> [!NOTE]
> Az Azure-beli nagyméretű példányok telepítésének korábbi SAP HANAei esetében az előre telepített pillanatkép-eszközök könyvtára volt `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Az [előfeltételként szükséges lépések](#prerequisites-for-installation) elvégzése után mostantól a következőképpen telepítheti a pillanatkép-eszközöket az öntelepítő használatával:

1. Másolja a letöltött önálló telepítőt a célként megadott rendszeren.
1. Futtassa az öntelepítőt `root` felhasználóként, tekintse meg a következő példát. Ha szükséges, hajtsa végre a fájl végrehajtható fájlját a `chmod +x *.run` paranccsal.

Ha az öntelepítő parancsot argumentumok nélkül futtatja, a telepítő a következő módon fogja megjeleníteni a pillanatkép-eszközök telepítésére vonatkozó súgót:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Az öntelepítő lehetőséggel kinyerheti (-X) a pillanatkép-eszközöket a kötegből anélkül, hogy felhasználói létrehozását és telepítését kellene végrehajtania. Ez lehetővé teszi, hogy a tapasztalt rendszergazda manuálisan végezze el a telepítés lépéseit, vagy egy meglévő telepítés verziófrissítéséhez másolja a parancsokat.

### <a name="easy-installation-of-snapshot-tools-default"></a>Pillanatkép-eszközök egyszerű telepítése (alapértelmezett)

A telepítő úgy lett kialakítva, hogy gyorsan telepítse az Azure-beli SAP HANA pillanatkép-eszközeit. Alapértelmezés szerint, ha a telepítő csak a-I kapcsolóval fut, akkor a következő lépéseket hajtja végre:

1. Hozzon létre Pillanatkép-felhasználót a "azacsnap", a kezdőkönyvtár és a csoporttagság beállítása számára.
1. Konfigurálja a azacsnap-felhasználó bejelentkezési adatait `~/.profile` .
1. Keressen fájlrendszert a azacsnap hozzáadandó címtárakhoz `$PATH` , ezek általában a SAP HANA eszközök elérési útjai, például a és a `hdbsql` `hdbuserstore` .
1. Keressen fájlrendszert a azacsnap-hez hozzáadandó könyvtárakhoz `$LD_LIBRARY_PATH` . Számos parancshoz meg kell adni a könyvtár elérési útját ahhoz, hogy megfelelően lehessen végrehajtani, ez konfigurálja a telepített felhasználó számára.
1. Másolja a azacsnap található SSH-kulcsokat a "root" felhasználótól (a telepítést futtató felhasználótól). Ez azt feltételezi, hogy a "root" felhasználó már konfigurálta a kapcsolatot a tárolóval
    - Lásd: "[a tárolóval való kommunikáció engedélyezése](#enable-communication-with-storage)" szakasz.
1. Másolja a SAP HANA biztonságos felhasználói tárolót a azacsnap. Ez azt feltételezi, hogy a "root" felhasználó már konfigurálta a biztonságos felhasználói tárolót – lásd: "a kommunikáció engedélyezése a SAP HANAsal" című szakasz.
1. A rendszer kibontja a pillanatkép-eszközöket `/home/azacsnap/bin/` .
1. A parancsokban az `/home/azacsnap/bin/` engedélyeik be vannak állítva (tulajdonosi és végrehajtható bit stb.).

Az alábbi példa a telepítő helyes kimenetét mutatja az alapértelmezett telepítési lehetőséggel való futtatáskor.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>A pillanatkép-eszközök eltávolítása

Ha a pillanatkép-eszközök az alapértelmezett beállításokkal lettek telepítve, az Eltávolítás csak azt a felhasználót távolítja el, amelyre a parancsok telepítve lettek (alapértelmezett érték = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>A pillanatkép-eszközök manuális telepítése

Bizonyos esetekben az eszközöket manuálisan kell telepíteni, de az ajánlott, hogy a telepítő alapértelmezett beállításával könnyítse meg ezt a folyamatot.

A karakterrel kezdődő minden egyes sor azt mutatja be, hogy a `#` legfelső szintű felhasználó futtatja a karaktert követő parancsokat. A `\` sor végén a rendszerhéj-parancs normál vonal-folytatási karaktere.

A legfelső szintű rendszeradminisztrátorként a következőképpen végezheti el a manuális telepítést:

1. A "sapsys" csoport AZONOSÍTÓjának lekérése, ebben az esetben a Group ID = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Hozzon létre Pillanatkép-felhasználót a "azacsnap", a kezdőkönyvtár és a csoporttagság beállítása az 1. lépésben szereplő csoportazonosító használatával.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Győződjön meg arról, hogy a felhasználó azacsnap-bejelentkezése `.profile` létezik.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Keressen fájlrendszert a azacsnap $PATHhoz hozzáadandó címtárakban, ezek általában az SAP HANA eszközök elérési útjai, például a `hdbsql` és a `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. A felhasználó profiljához tartozó frissített útvonal hozzáadása

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Keressen fájlrendszert a azacsnap $LD _LIBRARY_PATHhoz hozzáadandó címtárakban.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. A könyvtár frissített elérési útjának hozzáadása a felhasználó profiljához

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Nagyméretű Azure-példányok esetén
    1. Másolja a azacsnap található SSH-kulcsokat a "root" felhasználótól (a telepítést futtató felhasználótól). Ez azt feltételezi, hogy a "root" felhasználó már konfigurálta a kapcsolatot a tárolóval
       > Lásd: "[a tárolóval való kommunikáció engedélyezése](#enable-communication-with-storage)" szakasz.

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. A felhasználói engedélyek megfelelő beállítása az SSH-fájlokhoz

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Azure NetApp Files
    1. Konfigurálja a felhasználó `DOTNET_BUNDLE_EXTRACT_BASE_DIR` elérési útját a .net Core single-file Extract utasításban.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Másolja a SAP HANA biztonságos felhasználói tárolót a azacsnap. Ez azt feltételezi, hogy a "root" felhasználó már konfigurálta a biztonságos felhasználói tárolót.
    > Tekintse meg[a "kommunikáció engedélyezése SAP HANAval](#enable-communication-with-sap-hana)" című szakaszt.

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. A megfelelő felhasználói engedélyek beállítása a `hdbuserstore` fájlokhoz

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. A pillanatkép-eszközök kibontása a/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. A parancsok végrehajtható fájljának elkészítése

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Győződjön meg arról, hogy a felhasználó saját könyvtárában a megfelelő tulajdonosi engedélyek vannak beállítva

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>A pillanatkép-eszközök telepítésének befejezése

A telepítő a pillanatkép-eszközök telepítésének befejezése után elvégezheti a szükséges lépéseket.
A pillanatkép-eszközök konfigurálásához és teszteléséhez kövesse az alábbi lépéseket.  A sikeres tesztelés után végezze el az első adatbázis-konzisztens tárolási pillanatképet.

A következő kimenet a telepítőnek az alapértelmezett telepítési lehetőségekkel való futtatása után elvégzendő lépéseket mutatja be:

1. Váltás a pillanatkép felhasználói fiókba
    1. `su - azacsnap`
1. A HANA biztonságos felhasználói tároló beállítása
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Váltás a parancsok helyére
   1. `cd /home/azacsnap/bin/`
1. A Customer details fájl konfigurálása
   1. `azacsnap -c configure –-configuration new`
1. A tárolóhoz való kapcsolódás tesztelése.....
   1. `azacsnap -c test –-test storage`
1. A HANA-hoz való kapcsolódás tesztelése....
    1. SSL nélkül
       1. `azacsnap -c test –-test hana`
    1. az SSL használatakor ki kell választania a megfelelő SSL-beállítást
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Az első pillanatkép biztonsági mentésének futtatása
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

A 2. lépésre akkor van szükség, ha a telepítés előtt a "[kommunikáció engedélyezése SAP HANAtal](#enable-communication-with-sap-hana)" művelet nem történt meg.

> [!NOTE]
> A tesztelési parancsokat helyesen kell végrehajtani. Ellenkező esetben a parancsok sikertelenek lehetnek.

## <a name="configuring-the-database"></a>Az adatbázis konfigurálása

Ez a szakasz az adatbázis konfigurálását ismerteti.

### <a name="sap-hana-configuration"></a>SAP HANA konfiguráció

A rendszer néhány javasolt módosítást alkalmaz a SAP HANAra, hogy biztosítsa a naplók biztonsági másolatainak és katalógusának védelmét. Alapértelmezés szerint a `basepath_logbackup` és a `basepath_catalogbackup` fájlokat a könyvtárba fogja exportálni `$(DIR_INSTANCE)/backup/log` , és nem valószínű, hogy ez az elérési út olyan köteten található, amely pillanatképre van konfigurálva, ezért a `azacsnap` fájlok nem lesznek védve a tárolási pillanatképekkel.

A következő `hdbsql` parancs példákat mutat be a napló és a katalógus elérési útjának megadására olyan tárolóhelyek esetében, amelyek a által áttekinthető tárolási köteteken találhatók `azacsnap` . Ügyeljen arra, hogy a parancssorban szereplő értékek megfeleljenek a helyi SAP HANA konfigurációnak.

### <a name="configure-log-backup-location"></a>Napló biztonsági mentési helyének konfigurálása

Ebben a példában a paraméter módosítása történik `basepath_logbackup` .

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>A katalógus biztonsági mentési helyének konfigurálása

Ebben a példában a paraméter módosítása történik `basepath_catalogbackup` . Először ellenőrizze, hogy az `basepath_catalogbackup` elérési út létezik-e a fájlrendszeren, ha nem hozza létre az elérési utat ugyanazzal a tulajdonossal, mint a címtárat.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Ha az elérési utat létre kell hozni, a következő példa létrehozza az elérési utat, és beállítja a megfelelő tulajdonjogot és engedélyeket. Ezeket a parancsokat root-ként kell futtatni.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

A következő példa megváltoztatja a SAP HANA beállítást.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>A napló és a katalógus biztonsági mentési helyeinek megtekintése

A fenti módosítások végrehajtása után ellenőrizze, hogy a beállítások helyesek-e a következő paranccsal.
Ebben a példában a fenti útmutatást követően beállított beállítások rendszerbeállításokként jelennek meg.

> A lekérdezés az összehasonlítás alapértelmezett beállításait is visszaadja.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Napló biztonsági mentési időkorlátjának konfigurálása

A naplók biztonsági mentésének elvégzéséhez SAP HANA alapértelmezett beállítása 900 másodperc (15 perc). Ajánlott az értéket 300 másodpercre csökkenteni (azaz 5 perc).  Ezután rendszeres biztonsági mentést futtathat (például 10 percenként), ha hozzáadja a log_backups kötetet a konfigurációs fájl másik kötet szakaszába.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Napló biztonsági mentésének időtúllépése

A napló biztonsági mentési időkorlátjának módosítása után ellenőrizze, hogy ez a következőként van-e beállítva.
Ebben a példában a beállított beállítások a rendszerbeállításokként jelennek meg, de a lekérdezés az összehasonlítás alapértelmezett beállításait is visszaadja.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Application konzisztens pillanatkép-eszköz konfigurálása](azacsnap-cmd-ref-configure.md)
