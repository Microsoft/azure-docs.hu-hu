---
title: Azure HDInsight ID Broker (HIB)
description: Ismerje meg az Azure HDInsight ID Broker szolgáltatást a tartományhoz csatlakoztatott Apache Hadoop fürtök hitelesítésének egyszerűsítése érdekében.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 47ba11260c3b58566963e5a3ffac80ca461a8a23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946818"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID Broker (HIB)

Ez a cikk az Azure HDInsight ID Broker szolgáltatás beállítását és használatát ismerteti. Ezzel a funkcióval modern OAuth-hitelesítést érhet el az Apache Ambari, miközben a többtényezős hitelesítés kényszerítése nélkül, anélkül, hogy örökölt jelszó-kivonatokat kellene Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Áttekintés

A HDInsight ID Broker a következő helyzetekben egyszerűsíti az összetett hitelesítési beállításokat:

* A szervezet támaszkodik az összevonásra, hogy hitelesítse a felhasználókat a felhőalapú erőforrásokhoz való hozzáféréshez. Korábban a HDInsight Enterprise Security Package-fürtök használatához engedélyeznie kell a jelszó-kivonatok szinkronizálását a helyszíni környezetből a Azure Active Directory (Azure AD) szolgáltatásba. Ez a követelmény bizonyos szervezeteknél nehéz vagy nemkívánatos lehet.
* A szervezet szeretné kikényszeríteni a többtényezős hitelesítés használatát webalapú vagy HTTP-alapú hozzáféréshez az Apache Ambari és más fürterőforrás-szolgáltatásokhoz.

A HDInsight ID Broker olyan hitelesítési infrastruktúrát biztosít, amely lehetővé teszi a OAuth (modern) és a Kerberos (örökölt) protokoll közötti áttérést anélkül, hogy a jelszó-kivonatokat az Azure-AD DSba kellene szinkronizálnia. Ez az infrastruktúra egy Windows Server rendszerű virtuális gépen (VM) futó összetevőkből áll, amelyeken engedélyezve van a HDInsight ID Broker csomópont, valamint a fürt átjárójának csomópontjai.

A következő táblázat segítségével határozhatja meg a szervezet igényei alapján a legjobb hitelesítési lehetőséget.

|Hitelesítési lehetőségek |HDInsight-konfiguráció | Megfontolandó tényezők |
|---|---|---|
| Teljes OAuth | Enterprise Security Package + HDInsight ID Broker | A legbiztonságosabb megoldás. (Többtényezős hitelesítés támogatott.) *Nincs* szükség pass hash-szinkronizálásra. Nem rendelkezik SSH/kinit parancsot/keytab kiterjesztésű hozzáféréssel a helyszíni fiókokhoz, amelyek nem rendelkeznek jelszó-kivonattal az Azure AD DSban. A csak felhőalapú fiókok továbbra is SSH/kinit parancsot/keytab kiterjesztésű. Webalapú hozzáférés a Ambari-hez a OAuth-en keresztül. A OAuth támogatásához szükséges örökölt alkalmazások (például JDBC/ODBC) frissítése.|
| OAuth + alapszintű hitelesítés | Enterprise Security Package + HDInsight ID Broker | Webalapú hozzáférés a Ambari-hez a OAuth-en keresztül. Az örökölt alkalmazások továbbra is az alapszintű hitelesítést használják. A többtényezős hitelesítést le kell tiltani az alapszintű hitelesítéshez való hozzáféréshez. *Nincs* szükség pass hash-szinkronizálásra. Nem rendelkezik SSH/kinit parancsot/keytab kiterjesztésű hozzáféréssel a helyszíni fiókokhoz, amelyek nem rendelkeznek jelszó-kivonattal az Azure AD DSban. A csak felhőalapú fiókok továbbra is SSH/kinit parancsot. |
| Teljesen egyszerű hitelesítés | Enterprise Security Package | A legtöbb hasonló a helyszíni telepítésekhez. Az Azure AD DS jelszó-kivonatolási szinkronizálás szükséges. A helyszíni fiókok SSH-/kinit parancsot-vagy keytab kiterjesztésű-t is használhatnak. A többtényezős hitelesítést le kell tiltani, ha a biztonsági mentést végző tároló Azure Data Lake Storage Gen2. |

Az alábbi ábrán az összes felhasználó, beleértve az összevont felhasználókat is modern OAuth-alapú hitelesítési folyamat látható, miután a HDInsight ID Broker engedélyezve van:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="A HDInsight ID Broker hitelesítési folyamatát ábrázoló diagram.":::

Ebben a diagramban az ügyfélnek (azaz egy böngészőnek vagy alkalmazásnak) először a OAuth-jogkivonatot kell megvásárolnia. Ezután a tokent egy HTTP-kérelemben mutatja be az átjárónak. Ha már bejelentkezett más Azure-szolgáltatásokba, például a Azure Portalba, egyszeri bejelentkezéssel bejelentkezhet a HDInsight-fürtbe.

A rendszer továbbra is sok olyan örökölt alkalmazást támogat, amelyek csak az alapszintű hitelesítést támogatják (azaz a felhasználónevet és a jelszót). Ilyen esetekben továbbra is használhatja a HTTP alapszintű hitelesítést a fürt átjáróinak való kapcsolódáshoz. Ebben a telepítőben biztosítania kell, hogy az átjáró-csomópontok hálózati kapcsolata legyen a Active Directory összevonási szolgáltatások (AD FS) (AD FS) végponttal, hogy az átjáró-csomópontok közvetlen vonalát biztosítsa.

Az alábbi ábrán az összevont felhasználók alapszintű hitelesítési folyamata látható. Először az átjáró megkísérli végrehajtani a hitelesítést a [ROPC flow](../../active-directory/develop/v2-oauth-ropc.md)használatával. Ha az Azure AD-ben nincsenek szinkronizálva a jelszó-kivonatok, az AD FS végpont felfedéséhez, és a AD FS végponthoz való hozzáféréssel végrehajtja a hitelesítést.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Az alapszintű hitelesítéssel rendelkező architektúrát ábrázoló diagram.":::


## <a name="enable-hdinsight-id-broker"></a>HDInsight-azonosító-átvitelszervező engedélyezése

Enterprise Security Package-fürt létrehozása a HDInsight ID Broker engedélyezésével:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Enterprise Security Package-fürt alapvető létrehozási lépéseinek követése. További információ: HDInsight- [fürt létrehozása Enterprise Security Packagesal](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Válassza a **HDInsight-azonosító-átvitelszervező engedélyezése** lehetőséget.

A HDInsight ID Broker szolgáltatás egy további virtuális gépet helyez el a fürthöz. Ez a virtuális gép a HDInsight ID Broker csomópont, és a hitelesítést támogató kiszolgálói összetevőket tartalmaz. A HDInsight ID Broker csomópont tartományhoz van csatlakoztatva az Azure AD DS tartományhoz.

![A HDInsight ID Broker engedélyezését bemutató diagram.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat

Ha új szerepkört ad hozzá a `idbrokernode` következő attribútumokkal a sablon számítási profiljához, a fürt a HDINSIGHT ID Broker csomóponttal lesz létrehozva:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Az ARM-sablonok teljes mintájának megtekintéséhez tekintse meg az [itt](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)közzétett sablont.


## <a name="tool-integration"></a>Eszköz-integráció

A HDInsight-eszközök natív módon támogatják a OAuth. Ezeket az eszközöket modern, OAuth-alapú hozzáféréshez használhatja a fürtökhöz. A HDInsight [IntelliJ beépülő modult](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) Java-alapú alkalmazásokhoz, például a scalahez is használhatja. A [Spark és a kaptár eszközei a Visual Studio Code](../hdinsight-for-vscode.md) -hoz PySpark és kaptári feladatokhoz is használhatók. Az eszközök a Batch és az interaktív feladatok használatát is támogatják.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-hozzáférés jelszó-kivonat nélkül az Azure-ban AD DS

|SSH-beállítások |Megfontolandó tényezők |
|---|---|
| Helyi VM-fiók (például sshuser) | Ezt a fiókot a fürt létrehozási időpontjában kell megadnia. Ehhez a fiókhoz nincs Kerberos-hitelesítés. |
| Csak felhőalapú fiók (például alice@contoso.onmicrosoft.com ) | A jelszó kivonata az Azure AD DSban érhető el. A Kerberos-hitelesítés SSH Kerberos használatával lehetséges. |
| Helyszíni fiók (például alice@contoso.com ) | Az SSH Kerberos-hitelesítés csak akkor lehetséges, ha az Azure AD DSban jelszó-kivonat áll rendelkezésre. Ellenkező esetben ez a felhasználó nem tud SSH-t a fürthöz csatlakozni. |

Ha az SSH-t egy tartományhoz csatlakoztatott virtuális gépre vagy a parancs futtatására szeretné futtatni `kinit` , meg kell adnia egy jelszót. Az SSH Kerberos-hitelesítéshez szükséges, hogy a kivonat elérhető legyen az Azure AD DSban. Ha csak rendszergazdai forgatókönyvek esetén kívánja használni az SSH-t, létrehozhat egy csak felhőalapú fiókot, és az SSH-val a fürthöz is használhatja azt. Más helyszíni felhasználók továbbra is használhatják a Ambari vagy a HDInsight eszközt vagy a HTTP alapszintű hitelesítést anélkül, hogy az Azure AD DSban elérhető jelszó-kivonatot kellene használni.

Ha a szervezet nem szinkronizálja a jelszó-kivonatokat az Azure AD DSba, ajánlott eljárásként hozzon létre egy csak felhőalapú felhasználót az Azure AD-ben. Ezután rendelje hozzá a fürtöt rendszergazdaként a fürt létrehozásakor, és használja azt felügyeleti célokra. A használatával SSH-n keresztül érheti el a virtuális gépek gyökerét.

A hitelesítési problémák elhárításához tekintse meg [ezt az útmutatót](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>A OAuth-t használó ügyfelek HDInsight-átjáróhoz való kapcsolódáshoz a HDInsight ID Broker használatával

A HDInsight ID Broker beállításban az átjáróhoz csatlakozó egyéni alkalmazások és ügyfelek frissíthetők a szükséges OAuth-token beszerzéséhez. Kövesse a [jelen dokumentumban](../../storage/common/storage-auth-aad-app.md) ismertetett lépéseket a jogkivonat megszerzéséhez a következő információkkal:

*    OAuth erőforrás URI-ja: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    Engedély: (név: cluster. ReadWrite, azonosító: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Az OAuth-jogkivonat beszerzése után a HTTP-kérelem engedélyezési fejlécében használja a fürt átjáróját (például https:// <clustername> -int.azurehdinsight.net). Az Apache Livy API-hoz hasonló minta curl-parancs a következő példához hasonlóan néz ki:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

A Beeline és a Livy használata esetén az [itt](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) megadott példákkal is követheti az ügyfelet a OAuth használatára és a fürthöz való kapcsolódásra.

## <a name="faq"></a>GYIK
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Milyen alkalmazást hoz létre a HDInsight a HRE-ben?
Minden egyes fürthöz egy harmadik féltől származó alkalmazás lesz regisztrálva a HRE-ben a fürt URI-ja, mint a identifierUri (például `https://clustername.azurehdinsight.net` ).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>A felhasználók miért kérik a hozzájárulásukat a HIB-kompatibilis fürtök használata előtt?
A HRE-ben minden harmadik féltől származó alkalmazáshoz beleegyezett, mielőtt hitelesíteni tudja a felhasználókat vagy az adatelérést.

### <a name="can-the-consent-be-approved-programatically"></a>Jóvá lehet-e hagyni a jóváhagyást programozott módon?
A Microsoft Graph API lehetővé teszi a beleegyezikés automatizálását: az [API dokumentációja](/graph/api/resources/oauth2permissiongrant) , amely automatizálja a beleegyezik, a következőt:

* Regisztráljon egy alkalmazást, és adja meg az Application. ReadWrite. All engedélyeket az alkalmazáshoz, hogy hozzáférjen Microsoft Graph
* Miután létrehozta a fürtöt, az azonosító URI alapján kérdezi le a fürtöt.
* Az alkalmazáshoz tartozó engedély regisztrálása

Ha a fürt törölve lett, a HDInsight törli az alkalmazást, és nincs szükség semmilyen beleegyező adattörlésre.

 


## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürt konfigurálása Enterprise Security Package használatával Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
* [Fürtteljesítmény monitorozása](../hdinsight-key-scenarios-to-monitor.md)