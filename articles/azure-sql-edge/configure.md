---
title: Az Azure SQL Edge konfigurálása
description: Tudnivalók az Azure SQL Edge konfigurálásáról.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392333"
---
# <a name="configure-azure-sql-edge"></a>Az Azure SQL Edge konfigurálása

Az Azure SQL Edge a következő két lehetőség egyikének használatával támogatja a konfigurálást:

- Környezeti változók
- A/var/opt/MSSQL mappába helyezett MSSQL. conf fájl

> [!NOTE]
> A környezeti változók beállítása felülbírálja az MSSQL. conf fájlban megadott beállításokat.

## <a name="configure-by-using-environment-variables"></a>Konfigurálás környezeti változók használatával

Az Azure SQL Edge számos különböző környezeti változót tesz elérhetővé, amelyek segítségével konfigurálhatja az SQL Edge-tárolót. Ezek a környezeti változók a SQL Server on Linux számára rendelkezésre álló részhalmazok. SQL Server on Linux környezeti változókról további információért lásd: [környezeti változók](/sql/linux/sql-server-linux-configure-environment-variables/).

A következő új környezeti változók lettek hozzáadva az Azure SQL Edge-hez. 

| Környezeti változó | Description | Értékek |     
|-----|-----| ---------- | 
| **PlanId** | Megadja az inicializálás során használandó Azure SQL Edge SKU-t. Ez a környezeti változó csak akkor szükséges, ha Azure IoT Edge használatával telepíti az Azure SQL Edge-t. | **asde-Developer-on-IOT-Edge** vagy **asde-Premium-on-IOT-Edge** | 
| **MSSQL_TELEMETRY_ENABLED** | A használati és diagnosztikai adatok gyűjtésének engedélyezése vagy letiltása. | TRUE (igaz) vagy FALSE (hamis) |  
| **MSSQL_TELEMETRY_DIR** | Beállítja a használati és diagnosztikai adatgyűjtési naplófájlok célhelyének könyvtárát. | Mappa helye az SQL Edge-tárolón belül. Ez a mappa csatlakoztatási pontok vagy adatmennyiségek használatával képezhető le egy gazdagép kötetére. | 
| **MSSQL_PACKAGE** | Megadja a telepítendő dacpac vagy bacpac-csomag helyét. | A dacpac-vagy bacpac-csomagokat tartalmazó mappa, fájl vagy SAS URL-cím. További információkért lásd [: SQL Database DACPAC és BACPAC-csomagok üzembe helyezése az SQL Edge-ben](deploy-dacpac.md). |


Az Azure SQL Edge nem támogatja az alábbi SQL Server on Linux környezeti változót. Ha meg van adva, a rendszer a tároló inicializálása során figyelmen kívül hagyja ezt a környezeti változót.

| Környezeti változó | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Rendelkezésre állási csoport engedélyezése. Az **1** érték például engedélyezve van, és a **0** le van tiltva. |

> [!IMPORTANT]
> Az SQL Edge **MSSQL_PID** környezeti változója csak a **prémium** és a **fejlesztői** értéket fogadja el érvényes értékként. Az Azure SQL Edge nem támogatja az inicializálást termékkulcs használatával.

### <a name="specify-the-environment-variables"></a>Környezeti változók meghatározása

Az SQL Edge környezeti változóinak megadása, amikor a [Azure Portalon](deploy-portal.md)keresztül telepíti a szolgáltatást. A modul központi telepítésének **környezeti változók** szakaszában vagy a **tároló létrehozása lehetőség** részeként is hozzáadhatja őket.

Értékek hozzáadása **környezeti változókban**.

![Beállítás környezeti változók listája alapján](media/configure/set-environment-variables.png)

Értékek hozzáadása a **tároló létrehozási beállításaiban**.

![Beállítás a tároló létrehozása lehetőség használatával](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> A leválasztott központi telepítési módban környezeti változók is megadhatók a `-e` vagy a `--env` parancs használatával `--env-file` `docker run` .

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurálás az MSSQL. conf fájl használatával

Az Azure SQL Edge nem tartalmazza az [MSSQL-conf konfigurációs segédprogramot](/sql/linux/sql-server-linux-configure-mssql-conf/) , mint például a SQL Server on Linux. Manuálisan kell konfigurálnia az MSSQL. conf fájlt, és az SQL Edge-modul/var/opt/MSSQL/mappájára leképezett állandó tárolóban kell elhelyeznie. Ha az SQL Edge-t az Azure Marketplace-ről telepíti, ez a leképezés a **tároló létrehozási beállításai** között a **csatlakoztatások** lehetőséggel van megadva.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

A következő új MSSQL. conf-beállítások lettek hozzáadva az Azure SQL Edge-hez. 

|Beállítás|Leírás|
|:---|:---|
|**customerfeedback** | Válassza ki, hogy SQL Server küldjön-e visszajelzést a Microsoftnak. További információ: a [használati és diagnosztikai adatok gyűjtésének letiltása](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Beállítja a használati és diagnosztikai adatgyűjtési naplófájlok célhelyének könyvtárát. További információ: [a használati és diagnosztikai adatgyűjtés helyi ellenőrzése](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

A következő MSSQL. conf beállítások nem alkalmazhatók az SQL Edge-re:

|Beállítás|Leírás|
|:---|:---|
|**Ügyfélvisszajelzés** | Válassza ki, hogy SQL Server küldjön-e visszajelzést a Microsoftnak. |
|**Adatbázisbeli levelezési profil** | Állítsa be SQL Server on Linux alapértelmezett adatbázis-levelezési profilját. |
|**Magas rendelkezésre állás** | Rendelkezésre állási csoportok engedélyezése. |
|**Microsoft Distributed Transaction Coordinator** | Az MSDTC konfigurálása és hibakeresése Linux rendszeren. További elosztott tranzakciókkal kapcsolatos konfigurációs beállítások nem támogatottak az SQL Edge esetében. További információ ezekről a további konfigurációs lehetőségekről: az [MSDTC konfigurálása](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**A ML-szolgáltatások végfelhasználói licencszerződései** | Az R-és Python-LICENCSZERZŐDÉSek elfogadása Azure Machine Learning csomagokhoz. Csak SQL Server 2019-es verzióra vonatkozik.|
|**outboundnetworkaccess** |Engedélyezze a kimenő hálózati hozzáférést [Machine learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, Python-és Java-bővítményekhez.|

Az alábbi minta MSSQL. conf fájl az SQL Edge esetében működik. Az MSSQL. conf fájl formátumával kapcsolatos további információkért lásd az [MSSQL. conf formátumot](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Az Azure SQL Edge futtatása nem gyökérszintű felhasználóként

Alapértelmezés szerint az Azure SQL Edge-tárolók nem gyökérszintű felhasználóval vagy csoporttal futnak. Az Azure Marketplace-en (vagy a Docker Run használatával) való üzembe helyezéskor, kivéve, ha egy másik felhasználó/csoport van megadva, az SQL Edge-tárolók az MSSQL (nem root) felhasználóként kezdődnek. Ha másik nem root felhasználót szeretne megadni az üzembe helyezés során, adja hozzá a `*"User": "<name|uid>[:<group|gid>]"*` kulcs-érték párokat a tároló létrehozási beállításai között. Az alábbi példában az SQL Edge úgy van konfigurálva, hogy felhasználóként induljon el `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Annak engedélyezéséhez, hogy a nem legfelső szintű felhasználó hozzáférjen a csatlakoztatott köteteken található adatbázis-fájlokhoz, győződjön meg arról, hogy a tárolóban futtatott felhasználó/csoport olvasási & írási engedéllyel rendelkezik az állandó fájl tárolásához. Az alábbi példában a nem root felhasználót a fájlok tulajdonosaként user_id 10001-es értékre állítjuk. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Frissítés korábbi CTP-kiadásokból

Az Azure SQL Edge korábbi CTPs úgy lettek konfigurálva, hogy legfelső szintű felhasználóként fussanak. A korábbi CTPs való frissítéskor a következő beállítások érhetők el.

- Továbbra is használhatja a root felhasználót – a root felhasználó használatának folytatásához adja hozzá a `*"User": "0:0"*` kulcs-érték párokat a tároló létrehozása lehetőség alatt.
- Az alapértelmezett MSSQL-felhasználó használata – az alapértelmezett MSSQL-felhasználó használata, kövesse az alábbi lépéseket
  - Adjon hozzá egy MSSQL nevű felhasználót a Docker-gazdagépen. Az alábbi példában egy 10001-es AZONOSÍTÓJÚ felhasználói MSSQL-t adunk hozzá. Ezt a felhasználót a rendszer a legfelső szintű csoportba is felveszi.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Módosítsa az engedélyt arra a könyvtárra/csatlakoztatási kötetre, ahol az adatbázisfájl található 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Másik nem gyökérszintű felhasználói fiók használata – másik nem gyökérszintű felhasználói fiók használata
  - A tároló-létrehozási beállítások módosításával adja meg a `*"User": "user_name | user_id*` kulcs-érték párokat a tároló létrehozása lehetőség alatt. Cserélje le user_name vagy user_id a Docker-gazdagépről származó tényleges user_name vagy user_idra. 
  - Módosítsa a könyvtár/csatlakoztatási kötet engedélyeit.

## <a name="persist-your-data"></a>Adatmegőrzés

Az Azure SQL Edge konfigurációjának módosításai és az adatbázisfájlok a tárolóban maradnak, még akkor is, ha a tárolót a és a rendszerre indítja újra `docker stop` `docker start` . Ha azonban eltávolítja a tárolót a `docker rm` -val, a tárolóban lévő összes elem törlődik, beleértve az Azure SQL Edge és az adatbázisait is. A következő szakasz azt ismerteti, hogyan használhatók az **adatkötetek az adatbázisfájlok megőrzésére** , még akkor is, ha a társított tárolók törlődnek.

> [!IMPORTANT]
> Az Azure SQL Edge esetében fontos, hogy tisztában legyen az adatmegőrzéssel a Docker szolgáltatásban. Az ebben a szakaszban található vitán kívül lásd: a Docker dokumentációja a [Docker-tárolókban tárolt adatok kezelésével](https://docs.docker.com/engine/tutorials/dockervolumes/)kapcsolatban.

### <a name="mount-a-host-directory-as-data-volume"></a>Gazdagép könyvtárának csatlakoztatása adatkötetként

Az első lehetőség az, hogy az állomáson lévő könyvtárat egy adatkötetként csatlakoztassa a tárolóban. Ehhez használja a `docker run` parancsot a `-v <host directory>:/var/opt/mssql` jelzővel. Ez lehetővé teszi, hogy a tároló-végrehajtások között visszaálljon az adatgyűjtés.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Ezzel a technikával a Docker-on kívül is megoszthatja és megtekintheti a gazdagépen lévő fájlokat.

> [!IMPORTANT]
> A **Windowson futó Docker** gazdagép-kötetének leképezése jelenleg nem támogatja a teljes könyvtár leképezését `/var/opt/mssql` . Leképezheti azonban egy alkönyvtárat is, például a `/var/opt/mssql/data` gazdagépet.

> [!IMPORTANT]
> Jelenleg nem támogatott a gazdagép mennyiségi hozzárendelése a Docker-hez a **Mac gépen** az Azure SQL Edge-lemezképpel. Használja helyette az adatmennyiség-tárolókat. Ez a korlátozás a `/var/opt/mssql` címtárra vonatkozik. A csatlakoztatott könyvtárból való olvasás jól működik. Például csatlakoztathat egy gazdagép-könyvtárat a-v használatával Mac gépen, és visszaállíthatja a biztonsági mentést egy. bak fájlból, amely a gazdagépen található.

### <a name="use-data-volume-containers"></a>Adatmennyiség-tárolók használata

A második lehetőség egy adatmennyiség-tároló használata. Adatmennyiség-tároló létrehozásához adjon meg egy kötet nevet a paraméterrel rendelkező gazdagép könyvtára helyett `-v` . A következő példa egy **sqlvolume** nevű megosztott adatkötetet hoz létre.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Ez a technika a futtatási parancsban lévő adatmennyiség implicit létrehozásához nem működik a Docker régebbi verzióival. Ebben az esetben használja a Docker dokumentációjában leírt explicit lépéseket, [és hozzon létre és csatlakoztatjon egy adatmennyiség-tárolót](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Ha leállítja és eltávolítja a tárolót, az adatmennyiség továbbra is fennáll. Megtekintheti a `docker volume ls` paranccsal.

```bash
docker volume ls
```

Ha ezt követően egy másik tárolót hoz létre ugyanazzal a kötettel, akkor az új tároló ugyanazt az Azure SQL Edge-adatmennyiséget használja, amely a kötetben található.

Adatmennyiség-tároló eltávolításához használja az `docker volume rm` parancsot.

> [!WARNING]
> Ha törli az adatmennyiség-tárolót, a tárolóban lévő összes Azure SQL Edge-adatbázis *véglegesen* törölve lesz.


## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás az Azure SQL Edge-hez](connect.md)
- [Teljes körű IoT-megoldás kiépítése az SQL Edge használatával](tutorial-deploy-azure-resources.md)