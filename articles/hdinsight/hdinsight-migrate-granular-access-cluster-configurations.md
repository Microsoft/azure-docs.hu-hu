---
title: Részletes szerepköralapú hozzáférés-Azure HDInsight fürtkonfigurációkhoz
description: Ismerje meg a HDInsight-fürtkonfigurációk részletes szerepköralapú hozzáférésre való migrálásának részeként szükséges módosításokat.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774930"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrálás fürtkonfigurációk részletes szerepköralapú hozzáféréséhez

Fontos változásokat vezetünk be a bizalmas információkhoz való, pontosabb szerepköralapú hozzáférés támogatásához. A módosítások részeként szükség lehet néhány műveletre **2019. szeptember 3-ig,** ha az érintett [entitások/forgatókönyvek valamelyikét használja.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Mi változik?

Korábban a HDInsight API-n keresztül a tulajdonosi, közreműködői vagy [olvasói](../role-based-access-control/rbac-and-directory-admin-roles.md)Azure-szerepkörökkel rendelkező fürtfelhasználók beszerezhették a titkos kulcsokat, mivel azok bárki számára elérhetők voltak az `*/read` engedéllyel. A titkos kulcsok olyan értékek, amelyek a felhasználói szerepkör által megengedettnél magasabb szintű hozzáféréshez használhatók. Ezek közé tartoznak például a fürtátjáró HTTP-hitelesítő adatai, a tárfiókkulcsok és az adatbázis hitelesítő adatai.

2019. szeptember 3-án ezeknek a titkos kulcsoknak a eléréséhez engedélyre van szükség, ami azt jelenti, hogy az Olvasó szerepkörű felhasználók már nem `Microsoft.HDInsight/clusters/configurations/action` férhetnek hozzá. Az ezzel az engedéllyel rendelkező szerepkörök a Közreműködő, a Tulajdonos és az új HDInsight-fürtkezelői szerepkör (erről bővebben alább olvashat).

Egy új [HDInsight-fürtkezelői](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) szerepkört is bevezetünk, amely anélkül tudja lekérni a titkos adatokat, hogy rendszergazdai jogosultságot kap a Közreműködő vagy a Tulajdonos szerepkörhöz. Összegezve:

| Szerepkör                                  | Korábban                                                                                       | Tovább       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | – Olvasási hozzáférés, beleértve a titkos okat is.                                                                   | – Olvasási hozzáférés, **a titkos kulcsok kivételével** | 
| HDInsight-fürt operátora<br>(Új szerepkör) | N/A                                                                                              | - Olvasási/írási hozzáférés, beleértve a titkos okat is         | 
| Közreműködő                           | – Olvasási/írási hozzáférés, beleértve a titkos okat is.<br>– Az Összes Azure-erőforrástípus létrehozása és kezelése.<br>– Szkriptműveletek végrehajtása.     | Nincs változás besorolás |
| Tulajdonos                                 | – Olvasási/írási hozzáférés, beleértve a titkos elérést is.<br>- Teljes hozzáférés az összes erőforráshoz<br>– Hozzáférés delegálható másoknak.<br>– Szkriptműveletek végrehajtása. | Nincs változás besorolás |

A HDInsight-fürt operátora szerepkör-hozzárendelés felhasználóhoz való hozzáadásáról, amely olvasási/írási hozzáférést biztosít számukra a fürt titkos adataihoz, tekintse meg a [HDInsight-fürt](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)operátora szerepkör-hozzárendelés hozzáadása felhasználóhoz című szakaszt.

## <a name="am-i-affected-by-these-changes"></a>Érintenek ezek a változások?

Ez a következő entitásokat és forgatókönyveket érinti:

- [API:](#api)A vagy `/configurations` `/configurations/{configurationName}` végpontot használó felhasználók.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1-es vagy újabb verziója.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0-s vagy újabb verziót.
- [Az Azure Data Lake és Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) a 2.3.9000.1-es verziónál régebbi verzióban.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 3.15.0-s vagy újabb verziót.
- [.NET-hez készült SDK](#sdk-for-net)
    - [1.x vagy 2.x](#versions-1x-and-2x)verzió: A `GetClusterConfigurations` `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` `DisableHttp` ConfigurationsOperationsExtensions osztály , , vagy metódusát használó felhasználók.
    - [3.x és újabb verziók:](#versions-3x-and-up)A `Get` osztály , , vagy `Update` `EnableHttp` `DisableHttp` metódusát használó `ConfigurationsOperationsExtensions` felhasználók.
- [Pythonhoz készült SDK:](#sdk-for-python)A osztály `get` vagy `update` metódusát használó `ConfigurationsOperations` felhasználók.
- [Javához készült SDK:](#sdk-for-java)A osztály `update` vagy `get` metódusát használó `ConfigurationsInner` felhasználók.
- [Go SDK:](#sdk-for-go)A vagy `Get` `Update` metódust használó felhasználók a `ConfigurationsClient` strukturálból.
- [Az Az.HDInsight PowerShell](#azhdinsight-powershell) a 2.0.0-s verziónál régebbi.
Tekintse meg az alábbi szakaszokat (vagy használja a fenti hivatkozásokat) a forgatókönyv migrálási lépéseit.

### <a name="api"></a>API

A következő API-k módosulnak vagy elavultak lesznek:

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (bizalmas adatok eltávolítva)
    - Korábban egyéni konfigurációtípusok (beleértve a titkos adatokat is) beszerzésére használták.
    - 2019. szeptember 3-ával kezdődően ez az API-hívás mostantól egyedi konfigurációtípusokat ad vissza, amelyekben a titkos kulcsok nincsenek megadva. Az összes konfiguráció , köztük a titkos kulcsok beszerzéséhez használja az új POST /configurations hívást. Az átjáróbeállítások beszerzéséhez használja az új POST /getGatewaySettings hívást.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (elavult)
    - Korábban az összes konfiguráció lekérte (beleértve a titkos adatokat is)
    - 2019. szeptember 3-ával kezdődően ez az API-hívás elavult, és a továbbiakban nem lesz támogatott. A további konfigurációk beszerzéséhez használja az új POST /configurations hívást. A bizalmas paraméterekkel nem megadott konfigurációk beszerzéséhez használja a GET /configurations/{configurationName} hívást.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (elavult)
    - Korábban az átjáró hitelesítő adatainak frissítésére használták.
    - 2019. szeptember 3-ával kezdődően ez az API-hívás elavult, és már nem támogatott. Használja helyette az új POST /updateGatewaySettings használhatja.

A következő helyettesítő API-k vannak hozzáadva:</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ezzel az API-val szerezheti be az összes konfigurációt, beleértve a titkos kulcsokat is.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ezzel az API-val szerezheti be az átjáró beállításait.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ezzel az API-val frissítheti az átjáró beállításait (felhasználónév és/vagy jelszó).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Ha az 1.1.1-es vagy újabb verziót használja, a megszakítások elkerülése érdekében frissítsen a [Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) legújabb verziójára.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Ha a 3.20.0-s vagy újabb verziót használja, a megszakítások elkerülése érdekében frissítsen a [Azure Toolkit for IntelliJ beépülő](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) modul legújabb verziójára.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake és Stream Analytics Tools for Visual Studio

A megszakítások elkerülése érdekében frissítsen az Azure Data Lake 2.3.9000.1-es vagy újabb verziójára és az [Stream Analytics Tools for Visual Studio-re.](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)  A frissítéssel kapcsolatos segítségért tekintse meg a [Data Lake Tools for Visual Studio.](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Ha a 3.15.0-s vagy újabb verziót használja, a megszakítások elkerülése [érdekében frissítsen](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) a Azure Toolkit for Eclipse legújabb verziójára.

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>1.x és 2.x verziók

Frissítsen a .NET-hez készült HDInsight SDK [2.1.0-s](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) verziójára. Ha a módosítások által érintett metódust használ, minimális kódmódosításra lehet szükség:

- `ClusterOperationsExtensions.GetClusterConfigurations` A **többé nem ad** vissza olyan bizalmas paramétereket, mint a tárkulcsok (alaphely) vagy a HTTP-hitelesítő adatok (átjáró).
    - Az összes konfiguráció , köztük a bizalmas paraméterek lekérése a `ClusterOperationsExtensions.ListConfigurations` következő lépésekkel történik: .  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkezik felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi annak részletes szabályozását, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz.
    - Ha csak a HTTP-átjáró hitelesítő adatait kell lekérni, használja a következőt: `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` A elavult, és a váltotta fel `ClusterOperationsExtensions.GetGatewaySettings` a következővel: .

- `ClusterOperationsExtensions.ConfigureHttpSettings` A elavult, és a váltotta fel `ClusterOperationsExtensions.UpdateGatewaySettings` a következővel: .

- `ConfigurationsOperationsExtensions.EnableHttp` A `DisableHttp` és a mostantól elavult. A HTTP mostantól mindig engedélyezve van, ezért ezekre a metódusra már nincs szükség.

#### <a name="versions-3x-and-up"></a>3.x és újabb verziók

Frissítsen a HDInsight SDK for .NET [5.0.0-s](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) vagy újabb verziójára. Ha a módosítások által érintett metódust használ, minimális kódmódosításra lehet szükség:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) A **többé nem ad** vissza olyan bizalmas paramétereket, mint a tárkulcsok (alaphely) vagy a HTTP-hitelesítő adatok (átjáró).
    - Az összes konfiguráció , köztük a bizalmas paraméterek lekérése a [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) következő lépésekkel történik: .Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkezik felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi annak részletes szabályozását, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Ha csak a HTTP-átjáró hitelesítő adatait kell lekérni, használja a következőt: [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) A elavult, és a váltotta fel [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) a következővel: . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) A [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) és a mostantól elavult. A HTTP mostantól mindig engedélyezve van, ezért ezekre a metódusra már nincs szükség.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

Frissítsen a Pythonhoz készült HDInsight SDK [1.0.0-s](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) vagy újabb verziójára. Ha a módosítások által érintett metódust használ, minimális kódmódosításra lehet szükség:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) A **többé nem ad** vissza olyan bizalmas paramétereket, mint a tárkulcsok (alaphely) vagy a HTTP-hitelesítő adatok (átjáró).
    - Az összes konfiguráció , köztük a bizalmas paraméterek lekérése a [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) következő lépésekkel történik: .Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkezik felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi annak részletes szabályozását, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Ha csak a HTTP-átjáró hitelesítő adatait kell lekérni, használja a következőt: [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) A elavult, és a váltotta fel [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) a következővel: .

### <a name="sdk-for-java"></a>Javához készült SDK

Frissítsen a Java HDInsight SDK [1.0.0-s](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) vagy újabb verziójára. Ha a módosítások által érintett metódust használ, minimális kódmódosításra lehet szükség:

- `ConfigurationsInner.get` A **többé nem ad** vissza olyan bizalmas paramétereket, mint a tárkulcsok (alaphely) vagy a HTTP-hitelesítő adatok (átjáró).
- `ConfigurationsInner.update` A már elavult.

### <a name="sdk-for-go"></a>SDK For Go

Frissítsen a Go HDInsight SDK [27.1.0-s](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) vagy újabb verziójára. Ha a módosítások által érintett metódust használ, minimális kódmódosításra lehet szükség:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) A **többé nem ad** vissza olyan bizalmas paramétereket, mint a tárkulcsok (alaphely) vagy a HTTP-hitelesítő adatok (átjáró).
    - Az összes konfiguráció , köztük a bizalmas paraméterek lekérése a [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) következő lépésekkel történik: .Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkezik felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi annak részletes szabályozását, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Ha csak a HTTP-átjáró hitelesítő adatait kell lekérni, használja a következőt: [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) A elavult, és a váltotta fel [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) a következővel: .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
A megszakítások elkerülése érdekében frissítsen az [Az PowerShell 2.0.0-s](https://www.powershellgallery.com/packages/Az) vagy újabb verziójára.  Ha olyan metódust használ, amelyet ezek a módosítások érintenek, minimális kódmódosításra lehet szükség.
- `Grant-AzHDInsightHttpServicesAccess` A elavult, és az új parancsmag `Set-AzHDInsightGatewayCredential` váltotta fel.
- `Get-AzHDInsightJobOutput` frissítve lett, hogy támogassa a tárkulcshoz való részletes szerepköralapú hozzáférést.
    - Ez nem érinti a HDInsight-fürt operátor, közreműködő vagy tulajdonos szerepkörrel rendelkező felhasználóit.
    - A csak Olvasó szerepkörrel rendelkezik felhasználóknak explicit módon kell megadniuk a `DefaultStorageAccountKey` paramétert.
- `Revoke-AzHDInsightHttpServicesAccess` A már elavult. A HTTP mostantól mindig engedélyezve van, ezért erre a parancsmagra már nincs szükség.
 Lásd: [az az. HDInsight migrálási útmutató](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) további részletekért.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight-fürt operátori szerepkör-hozzárendelésének hozzáadása egy felhasználóhoz

A Tulajdonos [](../role-based-access-control/built-in-roles.md#owner) szerepkörű felhasználók hozzárendelheti a [HDInsight-fürtüzemeltető](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) szerepkört olyan felhasználókhoz, akik számára olvasási/írási hozzáférést szeretne az érzékeny HDInsight-fürtkonfigurációs értékekhez (például fürtátjáró hitelesítő adataihoz és tárfiókkulcsaihoz).

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

A szerepkör-hozzárendelés hozzáadásának legegyszerűbb módja az `az role assignment create` Azure CLI parancsának használata.

> [!NOTE]
> Ezt a parancsot egy Tulajdonos szerepkört használó felhasználónak kell futtatnia, mivel csak ő használhatja ezeket az engedélyeket. A annak a felhasználónak a szolgáltatásneve vagy e-mail-címe, akihez hozzá szeretné rendelni a `--assignee` HDInsight-fürt operátora szerepkört. Ha nem megfelelő engedélyekkel kapcsolatos hibaüzenetet kap, tekintse meg az alábbi gyakori kérdéseket.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Szerepkör megadása az erőforrás (fürt) szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Szerepkör megadása az erőforráscsoport szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Szerepkör megadása az előfizetés szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Alternatív megoldásként a Azure Portal HDInsight-fürt operátorának szerepkör-hozzárendelését is hozzáadhatja egy felhasználóhoz. Tekintse meg az [Azure-szerepkörök hozzárendelése a Azure Portal.](../role-based-access-control/role-assignments-portal.md)

## <a name="faq"></a>GYIK

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Miért jelenik meg a 403-as (tiltott művelet) válasz az API-kérelmek és/vagy az eszköz frissítésekor?

A fürtkonfigurációk már a részletes szerepköralapú hozzáférés-vezérlés mögött vannak, és a hozzáférésükhöz `Microsoft.HDInsight/clusters/configurations/*` engedélyre van szükség. Az engedély beszerzéséhez rendelje hozzá a HDInsight-fürt operátora, közreműködője vagy tulajdonosa szerepkört a konfigurációkhoz hozzáférni próbáló felhasználóhoz vagy szolgáltatásnévhez.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Miért látom a "Nem megfelelő jogosultságok a művelet végrehajtásához" részt a HDInsight-fürt operátora szerepkör másik felhasználóhoz vagy szolgáltatásnévhez való hozzárendeléséhez szükséges Azure CLI-parancs futtatásakor?

A tulajdonosi szerepkör mellett a parancsot végrehajtó felhasználónak vagy szolgáltatásnévnek megfelelő Azure AD-engedélyekkel kell rendelkeznie a hozzárendelt objektum-azonosítóinak kinézése érdekében. Ez az üzenet azt jelzi, hogy nincs elegendő Azure AD-engedély. Cserélje le a argumentumot a argumentumra, és adja meg paraméterként a hozzárendelt objektumazonosítóját a név (vagy felügyelt identitás esetén a rendszerbiztonsági tag `-–assignee` `–assignee-object-id` azonosítója) helyett. További információért tekintse meg az [az role assignment create dokumentáció választható](/cli/azure/role/assignment#az_role_assignment_create) paraméterek szakaszát.

Ha ez továbbra sem működik, forduljon az Azure AD-rendszergazdához a megfelelő engedélyek megszerzéséhez.

### <a name="what-will-happen-if-i-take-no-action"></a>Mi történik, ha nem történik semmi?

2019. szeptember 3-ával kezdődően a hívások többé nem adnak vissza információt, és a hívás többé nem ad vissza bizalmas paramétereket, például tárfiókkulcsokat vagy `GET /configurations` `POST /configurations/gateway` `GET /configurations/{configurationName}` fürtjelszót. Ugyanez vonatkozik a megfelelő SDK-metódusokra és PowerShell-parancsmagokra is.

Ha a fent említett Visual Studio, VSCode, IntelliJ vagy Eclipse valamelyik eszközének egy régebbi verzióját használja, azok a frissítésig nem fognak működni.

Részletesebb információkért tekintse meg a forgatókönyvhöz tartozó dokumentum megfelelő szakaszát.
