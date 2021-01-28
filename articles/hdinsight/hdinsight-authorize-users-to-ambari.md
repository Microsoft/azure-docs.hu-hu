---
title: Felhasználók engedélyezése Ambari-nézetekhez – Azure HDInsight
description: Ambari-felhasználók és-csoportok engedélyeinek kezelése a HDInsight-fürtökhöz az ESP-vel engedélyezve.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 0b6d4dacf43d80f371a2a4d286af1972198c3be3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942709"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Az Apache Ambari Views használatának engedélyezése felhasználók számára

A [HDInsight-fürtöket engedélyező Enterprise Security Package (ESP)](./domain-joined/hdinsight-security-overview.md) nagyvállalati szintű képességeket biztosít, beleértve a Azure Active Directory-alapú hitelesítést is. Szinkronizálhatja az Azure AD-csoportokhoz hozzáadott [új felhasználókat](hdinsight-sync-aad-users-to-cluster.md) , akik hozzáférést kaptak a fürthöz, így az adott felhasználók bizonyos műveleteket hajthatnak végre. A felhasználók, csoportok és engedélyek használata az [Apache Ambari](https://ambari.apache.org/) -ben mind az ESP HDInsight-fürtök, mind a standard HDInsight-fürtök esetében támogatott.

Active Directory felhasználók tartományi hitelesítő adataikkal jelentkezhetnek be a fürt csomópontjaira. A tartományi hitelesítő adataikat is használhatják a fürt interakciójának hitelesítésére más jóváhagyott végpontokkal, például a [Hue](https://gethue.com/), a Ambari views, az ODBC, a JDBC, a PowerShell és a REST API-k segítségével.

> [!WARNING]  
> Ne változtassa meg a Ambari watchdog (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A jelszó módosítása megszakítja a parancsfájl-műveletek használatát, vagy skálázási műveleteket hajt végre a fürtön.

Ha még nem tette meg, kövesse az [alábbi utasításokat](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) egy új ESP-fürt kiépítéséhez.

## <a name="access-the-ambari-management-page"></a>A Ambari-kezelés lap elérése

Az [Apache Ambari webes felületén](hdinsight-hadoop-manage-ambari.md)található **Ambari-kezelés lap** megnyitásához keresse meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` . Adja meg a fürt létrehozásakor megadott Fürtfelügyelő-felhasználónevet és-jelszót. Ezután a Ambari-irányítópulton válassza a **felügyelet Ambari** alatt a **felügyeleti** menü alatt:

![Apache Ambari-irányítópult kezelése](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Felhasználók hozzáadása

### <a name="add-users-through-the-portal"></a>Felhasználók hozzáadása a portálon keresztül

1. A felügyelet lapon válassza a **felhasználók** lehetőséget.

    ![Apache Ambari-kezelési oldal felhasználói](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Válassza a **+ helyi felhasználó létrehozása** lehetőséget.

1. Adja meg a **felhasználónevet** és a **jelszót**. Válassza a **Mentés** lehetőséget.

### <a name="add-users-through-powershell"></a>Felhasználók hozzáadása a PowerShell-lel

Szerkessze az alábbi változókat `CLUSTERNAME` `NEWUSER` `PASSWORD` a megfelelő értékekre való lecserélésével.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Felhasználók hozzáadása a Curlon keresztül

Az alábbi változók szerkesztéséhez cserélje le a,, `CLUSTERNAME` `ADMINPASSWORD` `NEWUSER` , és a `USERPASSWORD` értéket a megfelelő értékekre. A szkript a bash használatával hajtható végre. Kisebb módosítások szükségesek a Windows-parancssorhoz.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Engedélyek megadása Apache Hive nézetekhez

A Ambari többek között a [Apache Hive](https://hive.apache.org/) és az [Apache TEZ](https://tez.apache.org/)megtekintésére szolgáló példányokat tartalmaz. A kaptárak egy vagy több példányához való hozzáférés megadásához nyissa meg a **Ambari-kezelés lapot**.

1. A felügyelet lapon válassza ki a **nézetek** hivatkozást a bal oldali **nézetek** menüpont alatt.

    ![Apache Ambari views – hivatkozások](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. A nézetek lapon bontsa ki a **struktúra** sort. Létezik egy alapértelmezett struktúra nézet, amely akkor jön létre, amikor a kaptár szolgáltatás bekerül a fürtbe. Szükség szerint több kaptár-nézetet is létrehozhat. Válasszon kaptár nézetet:

    ![HDInsight nézetek – Apache Hive nézet](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Görgessen a nézet oldalának aljára. Az *engedélyek* szakaszban két lehetőség közül választhat a tartományi felhasználók számára a nézetre vonatkozó engedélyek megadásához:

**Engedély megadása a felhasználóknak** ![ Engedély megadása a felhasználóknak](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Engedélyek megadása ezekhez a csoportokhoz** ![ Engedélyek megadása ezekhez a csoportokhoz](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Felhasználó hozzáadásához kattintson a **felhasználó hozzáadása** gombra.

   * Kezdje el beírni a felhasználónevet, és látni fogja a korábban definiált nevek legördülő listáját.

     ![Apache Ambari-felhasználó automatikus kiegészítése](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Válassza ki vagy fejezze be a beírást, a felhasználónevet. Ha új felhasználóként szeretné felvenni ezt a felhasználónevet, kattintson az **új** gombra.

   * A módosítások mentéséhez jelölje be a **kék jelölőnégyzetet**.

     ![Apache Ambari felhasználói engedélyek megadása](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Csoport hozzáadásához kattintson a **Csoport hozzáadása** gombra.

   * Kezdje el beírni a csoport nevét. A meglévő csoportnév kiválasztásának vagy új csoport hozzáadásának folyamata megegyezik a felhasználók hozzáadásával.
   * A módosítások mentéséhez jelölje be a **kék jelölőnégyzetet**.

     ![Apache Ambari-engedélyek megadása](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

A felhasználók közvetlenül a nézethez való hozzáadása akkor hasznos, ha engedélyeket szeretne rendelni egy felhasználóhoz a nézet használatához, de nem szeretné, hogy egy olyan csoport tagja legyen, amely további engedélyekkel rendelkezik. A felügyeleti terhelés csökkentése érdekében egyszerűbb lehet engedélyeket rendelni a csoportokhoz.

## <a name="grant-permissions-to-apache-tez-views"></a>Engedélyek megadása az Apache TEZ-nézetekhez

Az [Apache TEZ](https://tez.apache.org/) -példányok lehetővé teszik a felhasználók számára az összes TEZ-feladat figyelését és hibakeresését, amelyeket [Apache Hive](https://hive.apache.org/) lekérdezések és [Apache Pig](https://pig.apache.org/) -parancsfájlok küldenek. A fürt üzembe helyezésekor létrejön egy alapértelmezett TEZ-nézet példánya.

Ha felhasználókat és csoportokat szeretne hozzárendelni egy TEZ-példányhoz, bontsa ki a **TEZ** sort a nézetek lapon a korábban leírtak szerint.

![HDInsight nézetek – Apache TEZ nézet](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Felhasználók vagy csoportok hozzáadásához ismételje meg a 3-5. lépést az előző szakaszban.

## <a name="assign-users-to-roles"></a>Felhasználók társítása szerepkörökhöz

A felhasználók és csoportok öt biztonsági szerepkörrel rendelkeznek, amelyek a csökkenő hozzáférési engedélyek sorrendjében vannak felsorolva:

* Fürt rendszergazdája
* Fürt operátora
* Szolgáltatás-rendszergazda
* Szolgáltatás operátora
* Fürt felhasználója

A szerepkörök kezeléséhez nyissa meg a **Ambari-kezelés lapot**, majd válassza ki a **szerepkörök** hivatkozást a *fürtök* menü bal oldali csoportjában.

![Apache Ambari-szerepkörök menü hivatkozásai](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Az egyes szerepkörökhöz tartozó engedélyek listájának megjelenítéséhez kattintson a szerepkörök lap **szerepkörök** táblázat fejléce melletti kék kérdőjelre.

![Apache Ambari-szerepkörök menü hivatkozásának engedélyei](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari-szerepkörök menü hivatkozásának engedélyei")

Ezen az oldalon két különböző nézet használható a felhasználók és csoportok szerepköreinek kezeléséhez: letiltás és Listázás.

### <a name="block-view"></a>Nézet tiltása

A blokk nézet a saját sorában jeleníti meg az egyes szerepköröket, és megadja a **szerepkörök hozzárendelését ezekhez a felhasználókhoz** , és **hozzárendeli a szerepköröket** az előzőekben leírtak szerint.

![Apache Ambari-szerepkörök blokkolási nézete](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Listanézet

A listanézet gyors szerkesztési képességeket biztosít két kategóriában: felhasználók és csoportok.

* A listanézet Users (felhasználók) kategóriája megjeleníti az összes felhasználó listáját, így a legördülő listában minden felhasználóhoz kiválaszthat egy szerepkört.

    ![Apache Ambari-szerepkörök listája nézet – felhasználók](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A listanézet csoportok kategóriája az összes csoportot és az egyes csoportokhoz rendelt szerepkört jeleníti meg. A példánkban a csoportok listájának szinkronizálása a fürt tartományi beállításainak **hozzáférés felhasználói csoport** tulajdonságában megadott Azure ad-csoportokból történik. Lásd: [HDInsight-fürt létrehozása az ESP-vel engedélyezve](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Apache Ambari-szerepkörök listája nézet – csoportok](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    A fenti képen a "hiveusers" csoport a *fürt felhasználói* szerepköréhez van rendelve. Ez egy írásvédett szerepkör, amely lehetővé teszi, hogy az adott csoport felhasználói megtekintsék a szolgáltatás konfigurációit és a fürt metrikáit.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Bejelentkezés a Ambari csak megtekintésre szolgáló felhasználóként

Az Azure AD tartományi felhasználó "hiveuser1" engedélyeit hozzárendeljük a Kaptárhoz és a TEZ nézetekhez. Amikor elindítja a Ambari webes felhasználói felületét, és beírja a felhasználó tartományi hitelesítő adatait (az Azure AD felhasználóneve e-mail formátumban és jelszóval), a rendszer átirányítja a felhasználót a Ambari-nézetek lapra. Innen a felhasználó bármelyik elérhető nézetet kiválaszthatja. A felhasználó nem látogathatja meg a webhely bármely másik részét, beleértve az irányítópultot, a szolgáltatásokat, a gazdagépeket, a riasztásokat vagy a felügyeleti lapokat.

![Apache Ambari-felhasználó csak nézetekkel](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Jelentkezzen be a Ambari-ba fürt felhasználóként

Az Azure AD-tartományhoz tartozó "hiveuser2" felhasználót hozzárendelte a *fürt felhasználói* szerepköréhez. Ez a szerepkör képes elérni az irányítópultot és az összes menüelemet. A fürt felhasználóinak kevesebb engedélyezett lehetősége van, mint a rendszergazda. Például a hiveuser2 megtekintheti az egyes szolgáltatások konfigurációit, de nem szerkesztheti őket.

![Apache Ambari-irányítópult megjelenítése](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Következő lépések

* [Apache Hive házirendek konfigurálása a HDInsight-ben ESP-vel](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md)
* [A Apache Hive nézet használata a HDInsight Apache Hadoop](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD-felhasználók szinkronizálása a fürttel](hdinsight-sync-aad-users-to-cluster.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](./hdinsight-hadoop-manage-ambari-rest-api.md)