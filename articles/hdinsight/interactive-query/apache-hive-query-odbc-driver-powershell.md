---
title: Lekérdezés Apache Hive ODBC-illesztővel & PowerShell – Azure HDInsight
description: A Microsoft kaptár ODBC-illesztővel és a PowerShell-lel lekérdezheti Apache Hive fürtöket az Azure HDInsight.
keywords: struktúra, kaptár ODBC, PowerShell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871605"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Oktatóanyag: lekérdezés Apache Hive ODBC-vel és PowerShell-lel

A Microsoft ODBC-illesztők rugalmas módszert biztosítanak különböző típusú adatforrásokkal való kommunikációra, beleértve a Apache Hivet is. Az ODBC-illesztőket használó parancsfájlok olyan parancsfájlokat írhatnak, mint például a PowerShell, amelyekkel megnyitható egy kapcsolódás a kaptár-fürthöz, át kell adni a választott lekérdezést, és megjeleníti az eredményeket.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Töltse le és telepítse a Microsoft kaptár ODBC-illesztőt
> * A fürthöz kapcsolódó Apache Hive ODBC-adatforrás létrehozása
> * Minta adatainak lekérdezése a fürtből a PowerShell használatával

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Egy interaktív lekérdezési fürt a HDInsight-on. Az első lépésekhez tekintse meg [Az Azure HDInsight megismerését](../hdinsight-hadoop-provision-linux-clusters.md)ismertető témakört. Válassza az **interaktív lekérdezés** lehetőséget a fürt típusaként.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft kaptár ODBC-illesztő telepítése

Töltse le és telepítse a [Microsoft kaptár ODBC-illesztőt](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>ODBC-adatforrás létrehozása Apache Hive

A következő lépések bemutatják, hogyan hozhat létre Apache Hive ODBC-adatforrást.

1. A Windowsban navigáljon a  >  **Windows felügyeleti eszközök**  >  **ODBC-adatforrások (32 bites)/(64 bites)** elindításához.  Megnyílik egy **ODBC adatforrás-rendszergazda** ablak.

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="OBDC-adatforrás rendszergazdája" border="true":::

1. A **felhasználói DSN** lapon válassza a **Hozzáadás** lehetőséget az **új adatforrás létrehozása** ablak megnyitásához.

1. Válassza a **Microsoft kaptár ODBC-illesztő** lehetőséget, majd kattintson a **Befejezés** gombra a **Microsoft kaptár ODBC-illesztő DSN-telepítő** ablak megnyitásához.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Állomás (ok) |Írja be a következő szöveget: `CLUSTERNAME.azurehdinsight.net`. Például: `myHDICluster.azurehdinsight.net` |
   |  Port |Használja a **443** számú portot.|
   |  Adatbázis |Használja az **alapértelmezett értéket**. |
   |  Mechanizmus |A **Windows Azure HDInsight szolgáltatás** kiválasztása |
   |  Felhasználónév |Adja meg a HDInsight-fürt HTTP-felhasználói felhasználónevét. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszavát. Jelölje be a **Jelszó mentése (titkosított)** jelölőnégyzetet.|

1. Nem kötelező: válassza a **Speciális beállítások lehetőséget**.  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem próbálkozik a TSQL konvertálásával a HiveQL-be. Ezt a lehetőséget csak akkor használja, ha 100%-ban biztos abban, hogy tiszta HiveQL-utasításokat küld. SQL Server vagy Azure SQL Databasehoz való csatlakozáskor nincs bejelölve. |
   |  Beolvasott sorok száma blokkban |Nagy számú rekord beolvasása esetén a paraméter finomhangolása az optimális teljesítmény biztosítása érdekében szükséges lehet. |
   |  Alapértelmezett karakterlánc-oszlop hossza, Bináris oszlop hossza, decimális oszlop mérete |Az adattípus hossza és pontossága befolyásolhatja az adatvisszaadás módját. A pontosság és a csonkítás elvesztése miatt helytelen adatokat ad vissza. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Speciális DSN-konfigurációs beállítások" border="true":::

1. Válassza a **teszt** lehetőséget az adatforrás teszteléséhez. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye a **sikert** mutatja.  

1. A teszt ablak bezárásához kattintson **az OK gombra** .  

1. Kattintson az **OK** gombra a **Microsoft kaptár ODBC illesztőprogram-DSN telepítési** ablak bezárásához.  

1. Kattintson az **OK** gombra az **ODBC-adatforrás felügyeleti** ablakának bezárásához.  

## <a name="query-data-with-powershell"></a>Adatlekérdezés a PowerShell-lel

A következő PowerShell-szkript egy olyan függvény, amely az ODBC-vel kérdezi le a kaptár-fürtöt.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Az alábbi kódrészlet a fenti függvény használatával hajt végre egy lekérdezést az oktatóanyag elején létrehozott interaktív lekérdezési fürtön. Cserélje le a `DATASOURCENAME` értéket a **Microsoft kaptár ODBC-illesztőprogram DSN-telepítő** képernyőjén megadott **adatforrás nevére** . Ha a rendszer a hitelesítő adatok megadását kéri, adja meg a fürt létrehozásakor a **fürt bejelentkezési felhasználóneve** és a **fürt bejelentkezési jelszava** alatt megadott felhasználónevet és jelszót.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a HDInsight-fürtöt és a Storage-fiókot. Ehhez válassza ki azt az erőforráscsoportot, amelyben a fürtöt létrehozták, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan használhatja a Microsoft kaptár ODBC-illesztőt és a PowerShellt az Azure HDInsight interaktív lekérdezési fürt adatainak lekéréséhez.

> [!div class="nextstepaction"]
> [Az Excel és a Apache Hive összekötése az ODBC használatával](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
