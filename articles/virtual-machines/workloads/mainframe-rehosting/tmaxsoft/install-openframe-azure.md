---
title: TmaxSoft-OpenFrame Kiindulópontul telepítése az Azure Virtual Machines
description: Ismerje meg, hogyan állíthat be olyan OpenFrame Kiindulópontul-környezetet az Azure-ban, amely fejlesztési, bemutatói, tesztelési és éles számítási feladatokhoz alkalmas.
services: virtual-machines
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 2ca5f0311102c27c1233098a18ff16701b99ea2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950790"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>TmaxSoft-OpenFrame Kiindulópontul telepítése az Azure-ban

Ismerje meg, hogyan állíthat be olyan OpenFrame Kiindulópontul-környezetet az Azure-ban, amely fejlesztési, bemutatói, tesztelési és éles számítási feladatokhoz alkalmas. Ez az oktatóanyag végigvezeti az egyes lépéseken.

A OpenFrame Kiindulópontul több olyan összetevőt tartalmaz, amelyek az Azure-ban létrehozzák a nagyszámítógépi emulációs környezetet. Például a OpenFrame Kiindulópontul online szolgáltatások lecseréli a nagyvállalati middleware-t, például az IBM Customer Information Control System (CICS) és a OpenFrame Kiindulópontul batch szolgáltatást a TJES-összetevőjével, lecseréli az IBM mainframe-EK feladatának bejegyzési alrendszerét (csatlakoztatva).

A OpenFrame Kiindulópontul bármilyen kapcsolódó adatbázissal működik, beleértve a Oracle Database, a Microsoft SQL Server, az IBM DB2 és a MySQL-t. A OpenFrame Kiindulópontul telepítése a TmaxSoft Tibero-kapcsolatot használó adatbázist használja. A OpenFrame Kiindulópontul és a Tibero is Linux operációs rendszeren futnak. Ez az oktatóanyag a CentOS 7,3-et telepíti, de más támogatott Linux-disztribúciókat is használhat. A OpenFrame Kiindulópontul-alkalmazáskiszolgáló és a Tibero-adatbázis egyetlen virtuális gépre (VM) van telepítve.

Az oktatóanyag végigvezeti a OpenFrame Kiindulópontul Suite-összetevők telepítésének lépésein. Néhányat külön kell telepíteni.

Fő OpenFrame Kiindulópontul összetevők:

- Szükséges telepítési csomagok.
- Tibero-adatbázis.
- A OpenFrame Kiindulópontul alkalmazások a nyílt adatbázis-kapcsolatot (ODBC) használják a Tibero adatbázissal való kommunikációhoz.
- OpenFrame Kiindulópontul Base, a teljes rendszerét kezelő middleware.
- OpenFrame Kiindulópontul batch, a nagyszámítógépek batch rendszereit lecserélő megoldás.
- TACF, amely a rendszerekhez és erőforrásokhoz való felhasználói hozzáférés szabályozására szolgál.
- A Batch-tranzakciók rendezési eszköze.
- OFCOBOL, egy fordító, amely a mainframe COBOL programját értelmezi.
- OFASM, egy fordító, amely a nagyvállalati szerelvények programjait értelmezi.
- A OpenFrame Kiindulópontul Server Type C (OSC), a nagyvállalati middleware-t és az IBM-CICS lecserélő megoldás.
- Java Enterprise felhasználói megoldás (JEUS), a Java Enterprise Edition 6-os verziójára hitelesített webalkalmazás-kiszolgáló.
- OFGW, a OpenFrame Kiindulópontul-átjáró összetevő, amely egy 3270-figyelőt biztosít.
- A OFManager olyan megoldás, amely a OpenFrame Kiindulópontul működését és felügyeleti funkcióit biztosítja a webes környezetben.

Egyéb szükséges OpenFrame Kiindulópontul-összetevők:

- OSI, a nagyszámítógépi middleware-t és az IMS DC-t lecserélő megoldás.
- TJES, a nagyszámítógépek csatlakoztatva lévő környezetét biztosító megoldás.
- A OFTSAM az a megoldás, amely lehetővé teszi (V) SAM-fájlok használatát a nyitott rendszeren.
- OFHiDB, a nagyszámítógépek IMS-ADATBÁZISát lecserélő megoldás.
- OFPLI, egy fordító, amely a nagyszámítógépek PL/I programjait értelmezi.
- A PROTRIEVE olyan megoldás, amely végrehajtja a nagyvállalati nyelvi HITELESÍTÉSSZOLGÁLTATÓI Easytrieve.
- A OFMiner olyan megoldás, amely elemzi a nagyszámítógépeket, majd áttelepíti azokat az Azure-ba.

## <a name="architecture"></a>Architektúra

A következő ábra áttekintést nyújt az oktatóanyagban telepített OpenFrame Kiindulópontul 7,0 architektúra-összetevőkről:

![OpenFrame Kiindulópontul-összetevők](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure rendszerkövetelmények

A következő táblázat az Azure-beli telepítés követelményeit sorolja fel.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Követelmény</th><th>Leírás</th></tr>
</thead>
<tbody>
<tr><td>Támogatott Linux-disztribúciók az Azure-ban
</td>
<td>
Linux x86 2,6 (32 bites, 64-bit)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Hardver
</td>
<td>Magok: 2 (minimum)<br/>
Memória: 4 GB (minimum)<br/>
Felcserélhető terület: 1 GB (minimum)<br/>
Merevlemez: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Választható szoftverek Windows-felhasználók számára
</td>
<td>Putty: ebben az útmutatóban a virtuális gépek funkcióinak konfigurálásához használható<br/>
Megnyerő: egy népszerű SFTP-ügyfél és FTP-ügyfél, amelyet használhat<br/>
Eclipse for Windows: A TmaxSoft által támogatott fejlesztői platform<br/>
(A Microsoft Visual Studio jelenleg nem támogatott)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Előfeltételek

Az összes szükséges szoftver összeállításához és az összes manuális folyamat befejezéséhez tervezze meg a kiadások elköltését.

Az első lépések előtt tegye a következőket:

- Szerezze be a OpenFrame Kiindulópontul telepítési adathordozóját a TmaxSoft-ből. Ha Ön már meglévő TmaxSoft-ügyfél, az TmaxSoft-képviselőjétől kaphat egy licenccel rendelkező példányt. Ellenkező esetben kérjen próbaverziót a [TmaxSoft](https://www.tmaxsoft.com/contact/)webhelyről.

- Kérje meg a OpenFrame Kiindulópontul dokumentációját e-mailben a címre <support@tmaxsoft.com> .

- Azure-előfizetés beszerzése, ha még nem rendelkezik ilyennel. Mielőtt elkezdené, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) is.

- Választható. Hozzon létre egy helyek közötti VPN-alagutat vagy egy olyan Jumpbox, amely korlátozza az Azure-beli virtuális géphez való hozzáférést a szervezeten belüli engedélyezett felhasználók számára. Ez a lépés nem kötelező, de ez az ajánlott eljárás.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Virtuális gép beállítása az Azure-ban OpenFrame Kiindulópontul és Tibero

A OpenFrame Kiindulópontul környezetet különböző üzembe helyezési mintákkal állíthatja be, de az alábbi eljárás bemutatja, hogyan helyezheti üzembe a OpenFrame Kiindulópontul-alkalmazáskiszolgáló és a Tibero-adatbázist egy virtuális gépen. Nagyobb környezetekben és nagy teljesítményű számítási feladatokban az ajánlott eljárás az, hogy az adatbázist külön virtuális gépen helyezik üzembe a jobb teljesítmény érdekében.

**Virtuális gép létrehozása**

1. Lépjen a Azure Portalra <https://portal.azure.com> , és jelentkezzen be a fiókjába.

2. Kattintson a **Virtuális gépek** lehetőségre.

    ![Erőforrások listája a Azure Portal](media/vm-01.png)

3. Kattintson a **Hozzáadás** parancsra.

    ![Lehetőség hozzáadása a Azure Portal](media/vm-02.png)

4. Az **operációs rendszerektől** jobbra kattintson a **továbbiak** elemre.

     ![További lehetőség a Azure Portal](media/vm-03.png)

5. Kattintson a **CentOS-alapú 7,3** -re, hogy ezt a lépéseket pontosan kövesse, vagy válasszon másik támogatott Linux-disztribúciót is.

     ![Operációs rendszer beállításai a Azure Portal](media/vm-04.png)

6. Az **alapvető** beállítások területen adja meg a **név**, a **Felhasználónév**, a **Hitelesítés típusa**, az **előfizetés** (utólagos elszámolású a fizetés AWS-stílusa) és az **erőforráscsoport** (használjon egy meglévőt, vagy hozzon létre egy TmaxSoft csoportot).

7. Ha elkészült (beleértve a **hitelesítési típushoz** tartozó nyilvános/titkos kulcspárt is), kattintson a **Submit (Küldés**) gombra.

> [!NOTE]
> Ha a **hitelesítési típushoz** egy nyilvános SSH-kulcsot használ, tekintse meg a következő szakaszban ismertetett lépéseket a nyilvános/titkos kulcspár létrehozásához, majd folytassa a lépéseket itt.

### <a name="generate-a-publicprivate-key-pair"></a>Nyilvános/titkos kulcspár létrehozása

Ha Windows operációs rendszert használ, PuTTYgen kell létrehoznia egy nyilvános/titkos kulcspár létrehozásához.

A nyilvános kulcs szabadon megosztható, de a titkos kulcsot teljesen titokban kell tartani, és soha nem szabad megosztani egy másik féllel. A kulcsok létrehozása után be kell illesztenie a **nyilvános SSH-kulcsot** a konfigurációba, és fel kell őket feltölteni a Linux rendszerű virtuális gépre. A rendszer a hitelesítő \_ kulcsokon belül tárolja a \~ felhasználói fiók kezdőkönyvtárának/.ssh könyvtárában. A Linux rendszerű virtuális gép ezután képes felismerni és érvényesíteni a kapcsolódást, miután megadta a társított **SSH titkos kulcsot** az SSH-ügyfélen (esetünkben, PuTTY).

Amikor új személyeknek adnak hozzáférést a virtuális géphez: 

- Minden egyes új egyéni saját nyilvános/titkos kulcsot hoz létre a PuTTYgen használatával.
- Az egyének külön tárolják saját titkos kulcsaikat, és a nyilvános kulcs adatait a virtuális gép rendszergazdájától küldik el.
- A rendszergazda beillesztette a nyilvános kulcs tartalmát a/.ssh/Authorized- \~ \_ kulcsok fájljába.
- Az új egyéni kapcsolódás a PuTTY használatával.

**Nyilvános/titkos kulcspár létrehozása**

1.  Töltse le a PuTTYgen <https://www.putty.org/> alkalmazást, és telepítse az összes alapértelmezett beállítással.

2.  A PuTTYgen megnyitásához keresse meg a PuTTY telepítési könyvtárat a C: \\ Program Files \\ Putty fájlban.

    ![Putty felület](media/puttygen-01.png)

3.  Kattintson a **Létrehozás** lehetőségre.

    ![A PuTTY Key Generator párbeszédpanelt megjelenítő képernyőkép, amely kiemeli a generált gombot.](media/puttygen-02.png)

4.  A létrehozást követően mentse a nyilvános kulcsot és a titkos kulcsot is. Illessze be a nyilvános kulcs tartalmát a **virtuális gép létrehozása \> alapismeretek** ablaktáblájának **SSH nyilvános kulcs** szakaszában (az előző szakasz 6. és 7. lépésében látható).

    ![A PuTTY Key Generator párbeszédpanel](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VIRTUÁLIS gépek funkcióinak konfigurálása

1. Azure Portal a **méret kiválasztása** panelen válassza ki a kívánt Linux-gép hardveres beállításait. A Tibero és a OpenFrame Kiindulópontul telepítésének *minimális* követelményei 2 processzor és 4 GB RAM, ahogy az ebben a példában szereplő telepítésben látható:

    ![Virtuális gép létrehozása – alapismeretek](media/create-vm-01.png)

2. Kattintson a **3 beállítások** lehetőségre, és használja az alapértelmezett beállításokat a választható funkciók konfigurálásához.
3. Tekintse át a fizetési adatokat.

    ![Virtuális gép létrehozása – vásárlás](media/create-vm-02.png)

4. Adja meg a beállításokat. Az Azure megkezdi a virtuális gép üzembe helyezését. Ez a folyamat általában néhány percet vesz igénybe.

5. A virtuális gép üzembe helyezésekor megjelenik az irányítópult, amely megjeleníti a konfiguráció során kiválasztott beállításokat. Jegyezze fel a **nyilvános IP-címet**.

    ![Tmax az Azure-irányítópulton](media/create-vm-03.png)

6. Nyissa meg a PuTTY eszközt.

7. Az **állomásnév** mezőben adja meg a felhasználónevét és a másolt nyilvános IP-címet. Például: **username \@ publicip**.

    ![A PuTTY konfiguráció párbeszédpanelt megjelenítő képernyőkép, amely kiemeli az állomásnév (vagy az IP-cím) mező értékét.](media/putty-01.png)

8. A **Kategória** mezőben kattintson a **kapcsolatok SSH \> - \> hitelesítés** elemre. Adja meg a **titkos kulcs** fájljának elérési útját.

    ![A PuTTY konfigurációja párbeszédpanel](media/putty-02.png)

9. Kattintson a **Megnyitás** gombra a PuTTY ablak elindításához. Ha a művelet sikeres, az Azure-on futó új CentOS virtuális géphez csatlakozik.

10. Ha root felhasználóként szeretne bejelentkezni, gépelje be a **sudo bash** értéket.

    ![Root felhasználói bejelentkezés a parancsablakban](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>A környezet és a csomagok beállítása

Most, hogy létrejött a virtuális gép, és be van jelentkezve, végre kell hajtania néhány telepítési lépést, és telepítenie kell a szükséges előtelepítési csomagokat.

1. Rendelje hozzá a **ofdemo** nevet a helyi IP-címhez a VI használatával a Hosts fájl () szerkesztéséhez `vi /etc/hosts` . Feltételezve, hogy az IP-192.168.96.148 ofdemo, ez a változás előtt van:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Ez a változás után:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Csoportok és felhasználók létrehozása:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Felhasználói oframe7 jelszavának módosítása:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. A kernel paramétereinek frissítése a/etc/sysctl.conf-ben:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Frissítse a kernel paramétereit dinamikusan újraindítás nélkül:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. A szükséges csomagok beszerzése: Ellenőrizze, hogy a kiszolgáló csatlakozik-e az internethez, töltse le a következő csomagokat, majd telepítse őket:

     - dos2unix
     - glibc
     - folyékony beszédű. i686 folyékonyan. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > A ncurses csomag telepítése után hozza létre a következő szimbolikus hivatkozásokat:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - GCC
     - GCC-c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - gdb

7. Java RPM-telepítés esetén tegye a következőket:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>A Tibero-adatbázis telepítése

A Tibero a OpenFrame Kiindulópontul-környezet számos kulcsfontosságú funkcióját biztosítja az Azure-ban:

- A rendszer a Tibero használja a OpenFrame Kiindulópontul belső adattárként a különböző rendszerfunkciókhoz.
- A VSAM-fájlok, például a KSDS, a RRDS és a ESDS, a Tibero-adatbázist belsőleg használják az adattárolás számára.
- A TACF-adattár a Tibero-ben van tárolva.
- A OpenFrame Kiindulópontul-katalógus információit a rendszer a Tibero tárolja.
- Az Tibero-adatbázis helyettesítőként használható az IBM DB2-ben az alkalmazásadatok tárolásához.

**A Tibero telepítése**

1. Ellenőrizze, hogy megtalálható-e a Tibero bináris telepítőfájl, és tekintse át a verziószámot.
2. Másolja a Tibero szoftvert a Tibero felhasználói fiókba (oframe). Például:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Nyissa meg \_ a. bash-profilt a VI () alkalmazásban, `vi .bash_profile` és illessze be a következőket:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. A bash-profil végrehajtásához írja be a parancssorba a következőt:

    ```
    source .bash_profile
    ```

5. Hozzon elő egy tipp-fájlt (a Tibero konfigurációs fájlját), majd nyissa meg a VI-ben. Például:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Módosítsa \$ \_ a TB Home/Client/config/tbdsn. TBR fájlt, és helyezze a 127.0.0.1 helyet a oflocalhost, ahogy az a következőképpen jelenik meg:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Hozza létre az adatbázist. Az alábbi kimenet jelenik meg:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. A Tibero újrahasznosításához először állítsa le a parancsot a `tbdown` parancs használatával. Például:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. A rendszerindítási Tibero használatával `tbboot` . Például:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Egy tablespace létrehozásához nyissa meg az adatbázist a SYS-felhasználó (sys/Tmax) használatával, majd hozza létre a szükséges tablespace-t az alapértelmezett kötet-és TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Most írja be a következő SQL-parancsokat:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Indítsa el a Tibero, és ellenőrizze, hogy fut-e a Tibero folyamatok:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Kimenet:

![Tibero kimenete](media/tibero-01.png)

## <a name="install-odbc"></a>Az ODBC telepítése

A OpenFrame Kiindulópontul lévő alkalmazások a nyílt forráskódú unixODBC projekt által biztosított ODBC API használatával kommunikálnak a Tibero-adatbázissal.

Az ODBC telepítése:

1. Győződjön meg arról, hogy a unixODBC-2.3.4. tar. gz telepítőfájl létezik, vagy használja a `wget unixODBC-2.3.4.tar.gz` parancsot. Például:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Bontsa ki a bináris fájlt. Például:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navigáljon a unixODBC-2.3.4 könyvtárba, és állítson elő a Makefile-t a számítógép adatainak ellenőrzése paranccsal. Például:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Alapértelmezés szerint a unixODBC a/usr/local-ben van telepítve, ezért `--prefix` egy értéket kell áthaladnia a hely módosításához. Hasonlóképpen, a konfigurációs fájlok alapértelmezés szerint telepítve vannak a/etc számítógépen, ezért a `--sysconfdir` kívánt hely értékét adja át.

4. Makefile végrehajtása: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Másolja a végrehajtható fájlt a program könyvtárába a fordítás után. Például:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. A VI használatával szerkessze a bash-profilt ( `vi ~/.bash_profile` ), és adja hozzá a következőket:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Alkalmazza az ODBC-t. A következő fájlokat ennek megfelelően szerkessze. Például:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Hozzon létre egy szimbolikus hivatkozást, és ellenőrizze a Tibero adatbázis-kapcsolatát:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

A következő kimenet jelenik meg:

![Az SQL-hez kapcsolódó ODBC-kimenet](media/odbc-01.png)

## <a name="install-openframe-base"></a>A OpenFrame Kiindulópontul alapszintű telepítése

Az alapalkalmazás-kiszolgáló telepítése a OpenFrame Kiindulópontul által az Azure-beli rendszer felügyeletéhez használt egyes szolgáltatások előtt történik, beleértve a tranzakció-kezelő kiszolgáló folyamatait is.

**A OpenFrame Kiindulópontul-alap telepítése**

1. Győződjön meg arról, hogy a Tibero telepítése sikeres volt, majd ellenőrizze, hogy a következő OpenFrame Kiindulópontul \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin telepítőfájl és Base. properties konfigurációs fájl található.

2. Frissítse a bash-profilt a következő Tibero-specifikus információkkal:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. A bash-profil végrehajtása:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Győződjön meg arról, hogy a Tibero folyamatok futnak. Például:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Alap](media/base-01.png)

     > [!IMPORTANT]
     > A telepítés előtt győződjön meg róla, hogy elindítja a Tibero.

5. Licenc előállítása a [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) -ben, és a megfelelő mappában helyezze el a OpenFrame Kiindulópontul Base, a Batch, a TACF és az OSC licenceket:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Töltse le a OpenFrame Kiindulópontul Base bináris és Base. properties fájlokat:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Hajtsa végre a telepítőt a Base. properties fájl használatával. Például:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Ha elkészült, a telepítés befejezése üzenet el lesz játszva.

8. A parancs használatával ellenőrizze a OpenFrame Kiindulópontul alapkönyvtárának struktúráját `ls -ltr` . Például:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Start OpenFrame Kiindulópontul Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot parancs kimenete](media/base-02.png)

10. Ellenőrizze, hogy a folyamat állapota készen áll-e a tmadmin parancs használatával az si-ben. A RDY az **állapot** oszlopban jelenik meg az egyes folyamatok esetében:

     ![tmadmin parancs kimenete](media/base-03.png)

11. OpenFrame Kiindulópontul-alap leállítása:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>A OpenFrame Kiindulópontul batch telepítése

A OpenFrame Kiindulópontul batch számos olyan összetevőből áll, amelyek a mainframe-alapú batch-környezeteket szimulálják, és a Batch-feladatok Azure-beli futtatására használatosak.

**A Batch telepítése**

1. Győződjön meg arról, hogy az alap telepítése sikeres volt, majd ellenőrizze, hogy a OpenFrame Kiindulópontul \_ Batch7 \_ 0 \_ Fix2 \_ MVS \_ Linux \_ x86 \_ 64. bin telepítőfájl és Batch. properties konfigurációs fájl található-e:

2. A parancssorba írja be a `vi batch.properties` Batch. properties fájl szerkesztését a VI használatával.

3. Módosítsa a paramétereket az alábbiak szerint:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. A Batch telepítőjének végrehajtásához írja be a parancssorba a következőt:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Ha a telepítés befejeződött, indítsa el a telepített OpenFrame Kiindulópontul-csomagokat a parancssorba való beírásával `tmboot` .

    ![tmboot kimenete](media/tmboot-01.png)

6. Írja be a `tmadmin` parancsot a parancssorba a OpenFrame Kiindulópontul folyamatának vizsgálatához.

    ![Tmax-adminisztrátor képernyő](media/tmadmin-01.png)

7. Hajtsa végre a következő parancsokat:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. A `tmdown` Batch indításához és leállításához használja a parancsot:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>A TACF telepítése

A TACF Manager egy OpenFrame Kiindulópontul szolgáltatás-modul, amely a RACF biztonság használatával vezérli a rendszerekhez és az erőforrásokhoz való felhasználói hozzáférést.

**A TACF telepítése**

1. Ellenőrizze, hogy a OpenFrame Kiindulópontul \_ Tacf7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin Installer-fájl és a tacf. properties konfigurációs fájl található-e.
2. Győződjön meg arról, hogy a Batch telepítése sikeres volt, majd a VI paranccsal nyissa meg a tacf. properties fájlt ( `vi tacf.properties` ).
3. Módosítsa a TACF paramétereit:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. A TACF telepítőjének befejezése után alkalmazza a TACF környezeti változóit. A parancssorba írja be a következőt:

     ```
     source \~/.bash\_profile
     ```

5. Futtassa a TACF telepítőjét. A parancssorba írja be a következőt:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A kimenet valahogy így néz ki:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. A parancssorba írja be a következőt: `tmboot` OpenFrame Kiindulópontul újraindítása. A kimenet valahogy így néz ki:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Ellenőrizze, hogy a folyamat állapota készen áll `tmadmin` -e a `si` parancs használatával. Például:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Az **állapot** OSZLOPBAN a RDY megjelenik:

    ![RDY az Állapot oszlopban](media/tmboot-02.png)

8. Hajtsa végre a következő parancsokat:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Állítsa le a kiszolgálót a `tmdown` parancs használatával. A kimenet valahogy így néz ki:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>A rendezés telepítése

A rendezés az Adatrendezésre szolgáló batch-tranzakciókban használt segédprogram.

**A rendezés telepítése**

1. Győződjön meg arról, hogy a Batch telepítése sikeres volt, majd ellenőrizze, hogy megtalálható-e a **\_ 2sp3-Linux64-2123-opt. tar. gz** telepítési fájl.

2. Futtassa a telepítőt a tulajdonságok fájl használatával. A parancssorba írja be a következőt:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Helyezze át a rendezési könyvtárat a Kezdőlap helyére. A parancssorba írja be a következőt:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Hozzon létre egy licenc alkönyvtárat, és másolja a licencfájlba. Például:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Nyissa meg a bash. profil a VI ( `vi .bash_profile` ) eszközt, és frissítse a következő módon:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. A bash-profil végrehajtásához írja be a következőt a parancssorba: `. .bash_profile`

7. Hozza létre a konfigurációs fájlt. Például:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Hozza létre a szimbolikus hivatkozást. Például:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. A parancs végrehajtásával ellenőrizze a rendezési telepítést `prosort -h` . Például:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>A OFCOBOL telepítése

A OFCOBOL a OpenFrame Kiindulópontul-fordító, amely a nagyszámítógép COBOL programját értelmezi. 

**A OFCOBOL telepítése**

1. Győződjön meg arról, hogy a Batch/online telepítés sikeresen befejeződött, majd ellenőrizze, hogy megtalálható-e a OpenFrame Kiindulópontul \_ COBOL3 \_ 0 \_ 40 \_ Linux \_ x86 \_ 64. bin Installer fájl.

2. A OFCOBOL-telepítő végrehajtásához írja be a következőt a parancssorba:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. A folytatáshoz olvassa el a licencszerződést, és nyomja le az ENTER billentyűt.

4. Fogadja el a licencszerződést. A telepítés befejezésekor a következő jelenik meg:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Nyissa meg a bash-profilt a VI () alkalmazásban, `vi .bash_profile` és ellenőrizze, hogy a frissítése OFCOBOL-változókkal történt-e.
6. Hajtsa végre a bash-profilt. A parancssorba írja be a következőt:

     ```
      source ~/.bash_profile
     ```

7. Másolja a OFCOBOL-licencet a telepített mappába. Például:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Lépjen a OpenFrame Kiindulópontul tjclrun. conf konfigurációs fájlra, és nyissa meg a VI-ben. Például:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Itt látható a SYSLIB szakasz a változás előtt:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   A módosítás után a SYSLIB szakasz a következő:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Tekintse át a OpenFrame Kiindulópontul \_ Cobol \_ InstallLog. log fájlt a VI-ben, és ellenőrizze, hogy nincsenek-e hibák. Például:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Használja az `ofcob --version` parancsot, és tekintse át a verziószámot a telepítés ellenőrzéséhez. Például:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Indítsa újra a OpenFrame Kiindulópontul a `tmdown/tmboot` parancs használatával.

## <a name="install-ofasm"></a>A OFASM telepítése

A OFASM a OpenFrame Kiindulópontul-fordító, amely a nagyvállalati szerelvények programjait értelmezi.

**A OFASM telepítése**

1. Győződjön meg arról, hogy a Batch/online telepítés sikeresen befejeződött, majd ellenőrizze, hogy megtalálható-e a OpenFrame Kiindulópontul \_ ASM3 \_ 0 \_ Linux \_ x86 \_ 64. bin telepítőfájl.

2. Hajtsa végre a telepítőt. Például:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. A folytatáshoz olvassa el a licencszerződést, és nyomja le az ENTER billentyűt.
4. Fogadja el a licencszerződést.
5. Ellenőrizze, hogy a bash-profil frissítése OFASM-változókkal történt-e. Például:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Nyissa meg a OpenFrame Kiindulópontul tjclrun. conf konfigurációs fájlt a VI-ben, és szerkessze a következő módon:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Itt látható a [SYSLIB] szakasz a változás *előtt* :

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     A változás *után* a [SYSLIB] szakasz a következő:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Nyissa meg a OpenFrame Kiindulópontul \_ ASM \_ InstallLog. log fájlt a VI-ben, és ellenőrizze, hogy nincsenek-e hibák. Például:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Indítsa újra a OpenFrame Kiindulópontul az alábbi parancsok egyikének kiadásával:

     ```
     tmdown / tmboot
     ```

     vagy

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Az OSC telepítése

Az OSC az IBM CICS hasonló OpenFrame Kiindulópontul-környezet, amely támogatja a nagy sebességű OLTP-tranzakciókat és más felügyeleti funkciókat.

**Az OSC telepítése**

1. Győződjön meg arról, hogy az alap telepítése sikeres volt, majd ellenőrizze, hogy a OpenFrame Kiindulópontul \_ OSC7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin Installer fájl és osc. properties konfigurációs fájl található-e.
2. Szerkessze a következő paramétereket az OSC. properties fájlban:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Hajtsa végre a telepítőt a tulajdonságok fájl használatával, ahogy az a következőképpen jelenik meg:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Ha elkészült, a "telepítés kész" üzenet jelenik meg.

4. Győződjön meg arról, hogy a bash-profil frissítése OSC-változókkal történik.
5. Tekintse át a OpenFrame Kiindulópontul \_ OSC7 \_ 0 \_ Fix2 \_ InstallLog. log fájlt. Ennek az alábbihoz hasonlónak kell lennie:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. A VI segítségével nyissa meg a ofsys. SEQ konfigurációs fájlt. Például:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Az \# Alap és a \# köteg szakaszban szerkessze a paramétereket az ábrán látható módon.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Másolja ki a licencfájl fájlját. Például:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Az OSC elindításához és leállításához inicializálja a CICS régió megosztott memóriáját a parancssorba való beírásával `osctdlinit OSCOIVP1` .

10. Futtassa `oscboot` a parancsot az OSC rendszerindításához. A kimenet valahogy így néz ki:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. A folyamat készenléti állapotának ellenőrzéséhez használja a `tmadmin` parancsot az si-ben. Az összes folyamatnak az **állapot** oszlopban kell megjelennie a RDY.

    ![RDY megjelenítő folyamatok](media/tmadmin-02.png)

12. Állítsa le az OSC-t a `oscdown` parancs használatával.

## <a name="install-jeus"></a>A JEUS telepítése

A JEUS (Java Enterprise felhasználói megoldás) a OpenFrame Kiindulópontul webalkalmazás-kiszolgáló megjelenítési rétegét biztosítja.

A JEUS telepítése előtt telepítse az Apache Ant-csomagot, amely a JEUS telepítéséhez szükséges kódtárakat és parancssori eszközöket biztosítja.

**Az Apache Ant telepítése**

1. Töltse le a hangya bináris fájlját a `wget` parancs használatával. Például:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. A `tar` segédprogram használatával bontsa ki a bináris fájlt, és helyezze át a megfelelő helyre. Például:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. A hatékonyság érdekében hozzon létre egy szimbolikus hivatkozást:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Nyissa meg a bash-profilt a VI () alkalmazásban, `vi .bash_profile` és frissítse a következő változókkal:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Alkalmazza a módosított környezeti változót. Például:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**A JEUS telepítése**

1. Bontsa ki a telepítőt a `tar` segédprogram használatával. Például:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Hozzon létre egy **jeus** mappát ( `mkdir jeus7` ), és bontsa ki a bináris fájlt.
3. Váltson a **telepítési** könyvtárra (vagy használja a JEUS paramétert a saját környezetéhez). Például:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Végrehajtás `ant clean-all` a Build végrehajtása előtt. A kimenet valahogy így néz ki:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Készítsen biztonsági másolatot a tartományi konfiguráció-sablon. properties fájlról. Például:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Nyissa meg a tartomány-config-template. properties fájlt a VI-ben:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Módosítás `jeus.password=jeusadmin nodename=Tmaxsoft` ide `jeus.password=tmax1234 nodename=ofdemo`

8. Futtassa a `ant install` parancsot a JEUS létrehozásához.
9.  Frissítse a. bash- \_ profilt a JEUS változókkal az alábbiak szerint:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Hajtsa végre a bash-profilt. Például:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. Nem *kötelező*. Hozzon létre egy aliast a JEUS-összetevők egyszerű leállításához és indításához:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. A telepítés ellenőrzéséhez indítsa el a tartományi felügyeleti kiszolgálót a következő módon:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ellenőrizze a webes bejelentkezés használatával a következő szintaxissal:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     <http://192.168.92.133:9736/webadmin/login.>A bejelentkezési képernyő például a következőt jeleníti meg:
    
     ![JEUS webadminisztrátor bejelentkezési képernyője](media/jeus-01.png)

     > [!NOTE]
     > Ha a port biztonságával kapcsolatos problémákat tapasztal, nyissa meg a 9736-es portot, vagy tiltsa le a tűzfalat ( `systemctl stop firewall` ).

14. A Kiszolgáló1-hez tartozó állomásnév módosításához kattintson a **zárolás & szerkesztés** elemre, majd a **Kiszolgáló1** elemre. A kiszolgáló ablakban módosítsa az állomásnevet a következőképpen:

    1.  Módosítsa a **csomópontnév** a **ofdemo** értékre.
    2.  Az ablak jobb oldalán kattintson az **OK gombra** .
    3.  Kattintson a **módosítások alkalmazása** gombra az ablak bal alsó részén, és a leíráshoz adja meg a *hostname Change (állomásnév módosítása*) értéket.

    ![JEUS webfelügyeleti képernyő](media/jeus-02.png)

15. Ellenőrizze, hogy a konfiguráció sikeres-e a megerősítő képernyőn.

    ![jeus_domain kiszolgáló képernyője](media/jeus-03.png)

16. Indítsa el a felügyelt kiszolgáló "Kiszolgáló1" nevű folyamatát a következő parancs használatával:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>A OFGW telepítése

A OFGW az a OpenFrame Kiindulópontul-átjáró, amely támogatja az 3270-es terminál-emulátor és az OSI-alap közötti kommunikációt, és kezeli a terminál emulátora és az OSI közötti munkameneteket.

**A OFGW telepítése**

1. Győződjön meg arról, hogy a JEUS telepítése sikeresen megtörtént, majd ellenőrizze, hogy a OFGW7 \_ 0 \_ 1 \_ általános. bin telepítőfájl található-e.
2. Hajtsa végre a telepítőt. Például:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. A következő helyszíneket használhatja a megfelelő kérésekhez:
     -   JEUS-kezdőkönyvtár
     -   JEUS tartomány neve
     -   JEUS-kiszolgáló neve
     -   Tibero-illesztőprogram
     -   Tmax csomópont-azonosító ofdemo

4. Fogadja el a többi alapértelmezett értéket, majd nyomja le az ENTER billentyűt a telepítőből való kilépéshez.

5. Ellenőrizze, hogy a OFGW URL-címe a várt módon működik-e:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     A következő képernyő jelenik meg:

    ![OpenFrame Kiindulópontul webterminál](media/ofgw-01.png)

## <a name="install-ofmanager"></a>A OFManager telepítése

A OFManager üzemeltetési és felügyeleti funkciókat biztosít a OpenFrame Kiindulópontul számára a webes környezetben.

**A OFManager telepítése**

1. Ellenőrizze, hogy megtalálható-e a OFManager7 \_ általános. bin telepítési fájlja.
2. Hajtsa végre a telepítőt. Például:

     ```
     OFManager7_Generic.bin
     ```

3.  Nyomja le az ENTER billentyűt a folytatáshoz, majd fogadja el a licencszerződést.
4.  Válassza a telepítési mappát.
5.  Fogadja el az alapértelmezett beállításokat.
6.  Válassza a Tibero-adatbázis elemet.
7.  Az ENTER billentyű lenyomásával kiléphet a telepítőből.
8.  Ellenőrizze, hogy a OFManager URL-címe a várt módon működik-e:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Megjelenik a kezdőképernyő:

![Tmax OpenFrame Kiindulópontul-kezelő bejelentkezési képernyője](media/ofmanager-01.png)

Ezzel befejezte a OpenFrame Kiindulópontul-összetevők telepítését.

## <a name="next-steps"></a>Következő lépések

Ha a nagyszámítógépek áttelepítését tervezi, a bővülő partneri ökoszisztémánk segítséget nyújt Önnek. A partneri megoldások kiválasztásával kapcsolatos részletes útmutatásért tekintse meg a [platform modernizációs szövetségét](https://datamigration.microsoft.com/).

-   [Bevezetés az Azure használatába](../../../../index.yml)
-   [A gazdagép integrációs kiszolgálójának dokumentációja](/host-integration-server/)
-   [Útmutató az Azure Virtual adatközponthoz](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
