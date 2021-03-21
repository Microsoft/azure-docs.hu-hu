---
title: Az Azure SQL Edge üzembe helyezése a Docker-ben – Azure SQL Edge
description: Tudnivalók az Azure SQL Edge és a Docker üzembe helyezéséről
keywords: SQL Edge, tároló, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392078"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Az Azure SQL Edge üzembe helyezése a Docker-vel

Ebben a rövid útmutatóban a Docker használatával lehívhatja és futtathatja az Azure SQL Edge-tároló rendszerképét. Ezután kapcsolódjon a **Sqlcmd** az első adatbázis létrehozásához és a lekérdezések futtatásához.

Ez a rendszerkép az Ubuntu 18,04-alapú Azure SQL Edge-t tartalmazza. A szolgáltatás a Docker-motor 1.8-as vagy Linux-alapú, illetve a Mac/Windows rendszerhez készült Docker használatával használható.

## <a name="prerequisites"></a>Előfeltételek

- Docker-motor 1.8 + a Mac/Windows rendszerhez támogatott Linux-disztribúción vagy Docker-on. További információ: a [Docker telepítése](https://docs.docker.com/engine/installation/). Mivel az Azure SQL Edge-lemezképek Ubuntu 18,04-alapúak, javasolt, hogy Ubuntu 18,04 Docker-gazdagépet használjon.
- Docker **overlay2** -tároló illesztőprogramja. Ez az alapértelmezett érték a legtöbb felhasználó számára. Ha úgy találja, hogy nem használja ezt a tárolási szolgáltatót, és módosítania kell, tekintse meg a [overlay2 konfigurálásához használható Docker dokumentációjában](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)található utasításokat és figyelmeztetéseket.
- Legalább 10 GB lemezterület.
- Legalább 1 GB RAM.
- [Az Azure SQL Edge hardveres követelményei](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>A tároló rendszerképének lekérése és futtatása

Az alábbi lépések megkezdése előtt győződjön meg arról, hogy a cikk tetején a kívánt rendszerhéj (bash, PowerShell vagy cmd) van kiválasztva.

1. Az Azure SQL Edge-tároló rendszerképének lekérése a Microsoft Container Registryból.

    - Az Azure SQL Edge-tároló rendszerképének lekérése
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> A cikkben szereplő bash-parancsok `sudo` használatakor. MacOS & Windows rendszeren előfordulhat, hogy a sudo nem kötelező. Linux rendszeren, ha nem szeretné a sudo használatával futtatni a Docker-t, konfigurálhat egy Docker-csoportot, és hozzáadhat felhasználókat a csoporthoz. További információ: [Telepítés utáni lépések a Linux](https://docs.docker.com/engine/install/linux-postinstall/)rendszerhez.

Az előző parancs lekéri az Azure SQL Edge-tároló legújabb lemezképeit. Az összes elérhető lemezkép megjelenítéséhez tekintse meg [Az Azure-SQL-egde Docker hub lapot](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. A tároló rendszerképének a Docker használatával történő futtatásához használja a következő parancsot egy bash rendszerhéjból (Linux/macOS) vagy emelt szintű PowerShell-parancssorból.
    
    - Fejlesztői kiadásként futó Azure SQL Edge-példány elindítása
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Prémium kiadásként futó Azure SQL Edge-példány elindítása
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Ha a Windows PowerShell használatával futtatja ezeket a parancsokat, az idézőjelek helyett dupla idézőjeleket használ.


    > [!NOTE]
    > A jelszónak a Microsoft SQL Database motor alapértelmezett jelszavas házirendjének kell lennie, ellenkező esetben a tároló nem tudja beállítani az SQL Engine-t, és nem fog működni. Alapértelmezés szerint a jelszónak legalább 8 karakterből kell állnia, és tartalmaznia kell karaktereket a következő négy készletből háromból: nagybetűk, kisbetűk, alap 10 számjegy és szimbólumok. A naplófájlt a [Docker-naplók](https://docs.docker.com/engine/reference/commandline/logs/) parancs végrehajtásával ellenőrizheti.
    
    Az alábbi táblázat az előző példában szereplő paraméterek leírását tartalmazza `docker run` :

    | Paraméter | Leírás |
    |-----|-----|
    | **-e "ACCEPT_EULA = Y"** |  Állítsa a **ACCEPT_EULA** változót bármelyik értékre, hogy erősítse meg a [végfelhasználói](https://go.microsoft.com/fwlink/?linkid=2139274)licencszerződés elfogadását. Az Azure SQL Edge-rendszerkép kötelező beállítása. |
    | **-e "MSSQL_SA_PASSWORD = yourStrong (!) Jelszó** | Adja meg a saját erős jelszót, amely legalább 8 karakterből áll, és megfelel az [Azure SQL Edge jelszavával kapcsolatos követelményeknek](/sql/relational-databases/security/password-policy). Az Azure SQL Edge-rendszerkép kötelező beállítása. |
    | **– p 1433:1433** | Rendelje le a TCP-portot a gazdagép-környezetben (első érték) a tárolóban található TCP-porttal (második érték). Ebben a példában az Azure SQL Edge figyeli a tárolóban lévő TCP 1433-et, és ez a 1433-es porton keresztül érhető el a gazdagépen. |
    | **--név azuresqledge** | Véletlenszerűen generált helyett adjon meg egyéni nevet a tárolóhoz. Ha egynél több tárolót futtat, ezt a nevet nem használhatja újra. |
    | **d** | A tároló futtatása a háttérben (démon) |

    Az összes Azure SQL Edge környezeti változó teljes listájáért lásd: az [Azure SQL Edge konfigurálása környezeti változókkal](configure.md#configure-by-using-environment-variables). Az Azure SQL Edge-tárolók konfigurálásához használhatja az [MSSQL. conf fájlt](configure.md#configure-by-using-an-mssqlconf-file) is.

3. A Docker-tárolók megtekintéséhez használja az `docker ps` parancsot.
   
   ```bash
    sudo docker ps -a
   ```

4. Ha az **állapot** oszlopban a **fel** állapot látható, akkor az Azure SQL Edge fut a tárolóban, és figyeli a **portok** oszlopban megadott portot. Ha az Azure SQL Edge-tároló **állapot** oszlopa **ki** van zárva, tekintse meg az Azure SQL Edge dokumentációjának hibaelhárítási szakaszát.

    Az `-h` (állomásnév) paraméter is hasznos, de ez az oktatóanyag nem használható az egyszerűség kedvéért. Ezzel megváltoztatja a tároló belső nevét egy egyéni értékre. Ezt a nevet fogja látni a következő Transact-SQL-lekérdezésben:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    A beállítás `-h` és `--name` az ugyanarra az értékre jó módszer a cél tároló egyszerű azonosítására.

5. Utolsó lépésként változtassa meg a SA jelszavát, mert az `SA_PASSWORD` látható a `ps -eax` kimenetben, és az azonos nevű környezeti változóban van tárolva. Lásd az alábbi lépéseket.

## <a name="change-the-sa-password"></a> Az SA-jelszó módosítása

Az **sa** -fiók egy rendszergazda az Azure SQL Edge-példányon, amelyet a rendszer a telepítés során hoz létre. Az Azure SQL Edge-tároló létrehozása után a `MSSQL_SA_PASSWORD` megadott környezeti változó a tárolóban való futtatással felderíthető `echo $SA_PASSWORD` . Biztonsági okokból módosítsa a SA jelszavát.

1. Válassza ki az SA-felhasználóhoz használni kívánt erős jelszót.

2. `docker exec`A paranccsal a **Sqlcmd** futtatásával módosíthatja a jelszót a Transact-SQL használatával. A következő példában cserélje le a régi jelszót, `<YourStrong!Passw0rd>` és az új jelszót, a `<YourNewStrong!Passw0rd>` saját jelszavas értékeivel.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Kapcsolódás az Azure SQL Edge-hez

A következő lépések az Azure SQL Edge parancssori eszközt, az **Sqlcmd**-t használják a tárolón belül az Azure SQL Edge-hez való kapcsolódáshoz.

> [!NOTE]
> a Sqlcmd eszköz nem érhető el az SQL Edge-tárolók ARM64-verzióján belül.

1. A `docker exec -it` paranccsal interaktív bash-rendszerhéj indítható el a futó tárolóban. A következő példában `azuresqledge` a `--name` paraméter a tároló létrehozásakor megadott nevet adja meg.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. A tárolón belül a helyi kapcsolat Sqlcmd. A Sqlcmd alapértelmezés szerint nem található az elérési úton, ezért meg kell adnia a teljes elérési utat.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > A parancssorból kihagyhatja a jelszót, hogy a rendszer megkérje a jelszó megadását.

3. Ha a művelet sikeres, egy **Sqlcmd** parancssorba kell beolvasnia: `1>` .

## <a name="create-and-query-data"></a>Adatlekérdezés létrehozása és lekérdezése

A következő szakasz végigvezeti a **Sqlcmd** és a Transact-SQL használatával egy új adatbázis létrehozásához, az adathozzáadáshoz és egy egyszerű lekérdezés futtatásához.

### <a name="create-a-new-database"></a>Új adatbázis létrehozása

A következő lépések egy nevű új adatbázist hoznak létre `TestDB` .

1. A **Sqlcmd** parancssorba illessze be a következő Transact-SQL parancsot egy teszt adatbázis létrehozásához:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. A következő sorban írjon egy lekérdezést a kiszolgálón található összes adatbázis nevének visszaküldéséhez:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Adat beszúrása

Ezután hozzon létre egy új táblát, `Inventory` és szúrjon be két új sort.

1. A **Sqlcmd** -parancssorban váltson a kontextusra az új `TestDB` adatbázisra:

   ```sql
   USE TestDB
   ```

2. Új nevű tábla létrehozása `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Az új táblába szúrja be az adatbevitelt:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`Az előző parancsok végrehajtásához írja be a következőt:

   ```sql
   GO
   ```

### <a name="select-data"></a>Adatok kiválasztása

Most futtasson egy lekérdezést a tábla adatainak visszaküldéséhez `Inventory` .

1. A **Sqlcmd** parancssorában adjon meg egy lekérdezést, amely a tábla azon sorait adja vissza, amelyekben a mennyiség meghaladja a 152-et `Inventory` :

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Hajtsa végre a parancsot:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Kilépés a Sqlcmd-parancssorból

1. A **Sqlcmd** -munkamenet befejezéséhez írja be a következőt `QUIT` :

   ```sql
   QUIT
   ```

2. A tárolóban lévő interaktív parancssorból való kilépéshez írja be a következőt: `exit` . Az interaktív bash-rendszerhéjból való kilépés után a tároló továbbra is fut.

## <a name="connect-from-outside-the-container"></a>A tárolón kívülről való kapcsolat

Az SQL-kapcsolatokat támogató külső Linux, Windows vagy macOS rendszerű eszközről is csatlakozhat az Azure SQL Edge-példányhoz a Docker-gépen. Az SQL Edge-tárolón kívülről való csatlakozásról a [csatlakozás és az Azure SQL Edge lekérdezése](connect.md)című témakörben olvashat bővebben.

## <a name="remove-your-container"></a>Tároló eltávolítása

Ha el szeretné távolítani az oktatóanyagban használt Azure SQL Edge-tárolót, futtassa a következő parancsokat:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> A tároló leállítása és eltávolítása véglegesen törli a tárolóban lévő összes Azure SQL Edge-adatkészletet. Ha meg kell őriznie az adatait, [hozzon létre és másoljan ki egy biztonságimásolat-fájlt a tárolóból](backup-restore.md) , vagy használjon [tároló adatmegőrzési technikát](configure.md#persist-your-data).

## <a name="next-steps"></a>Következő lépések

- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével](tutorial-deploy-azure-resources.md).
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)