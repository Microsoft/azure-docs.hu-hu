---
title: Részletes szerepköralapú hozzáférés az Azure HDInsight-fürthöz
description: Ismerje meg az áttelepítés részeként szükséges módosításokat a HDInsight-fürtök részletes szerepköralapú hozzáféréséhez.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a30768f4904c9e5be2edc020f12260cf3a54c889
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425889"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrálás fürtkonfigurációk részletes szerepköralapú hozzáféréséhez

Néhány fontos módosítást vezetünk be, hogy a bizalmas adatok beszerzése érdekében részletesebb szerepköralapú hozzáférést lehessen támogatni. A változtatások részeként előfordulhat, hogy a 2019. **szeptember 3-án** kell végrehajtania egy műveletet, ha az [érintett entitások/forgatókönyvek](#am-i-affected-by-these-changes)valamelyikét használja.

## <a name="what-is-changing"></a>Mi változik?

Korábban a HDInsight API-val a tulajdonosi, közreműködő vagy olvasó [Azure-szerepköröket](../role-based-access-control/rbac-and-directory-admin-roles.md)birtokló felhasználók is megszerezhették a titkos kulcsokat, mivel azok mindenki számára elérhetővé válnak az `*/read` engedéllyel. A titkos kulcs olyan értékként van meghatározva, amely a felhasználó szerepkörének magasabb szintű hozzáférésének megszerzéséhez használható. Ezek közé tartoznak például a fürt átjárójának HTTP-hitelesítő adatai, a Storage-fiók kulcsainak és az adatbázis hitelesítő adatai.

2019. szeptember 3-ától a titkos kulcsokhoz való hozzáféréshez `Microsoft.HDInsight/clusters/configurations/action` engedély szükséges, ami azt jelenti, hogy a felhasználók már nem férhetnek hozzá az olvasó szerepkörrel rendelkező felhasználókhoz. Az ezzel az engedéllyel rendelkező szerepkörök a közreműködők, a tulajdonosok és az új HDInsight-fürt szerepkör (továbbiak az alább láthatók).

Egy új [HDInsight-fürtszolgáltatási](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) szerepkört is bevezetünk, amely lehetővé teszi a titkok beolvasását anélkül, hogy a közreműködő vagy a tulajdonos rendszergazdai engedélyeit kellene megadnia. Összegezve:

| Szerepkör                                  | Korábban                                                                                       | Továbbítás folyamatban       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | – Olvasási hozzáférés, beleértve a titkokat is.                                                                   | – Olvasási hozzáférés, a titkok **kivételével** |           |   |   |
| HDInsight-fürt operátora<br>(Új szerepkör) | N/A                                                                                              | – Olvasási/írási hozzáférés, beleértve a titkos kulcsokat         |   |   |
| Közreműködő                           | – Olvasási/írási hozzáférés, beleértve a titkos kulcsokat is.<br>– Az Azure-erőforrások összes típusának létrehozása és kezelése.<br>– Parancsfájl-műveletek végrehajtása.     | Nincs változás besorolás |
| Tulajdonos                                 | – Olvasási/írási hozzáférés, beleértve a titkokat.<br>– Teljes hozzáférés az összes erőforráshoz<br>– A másokhoz való hozzáférés delegálása.<br>– Parancsfájl-műveletek végrehajtása. | Nincs változás besorolás |

Arról, hogy hogyan adhat hozzá a HDInsight-kezelő szerepkör-hozzárendelést egy felhasználóhoz, hogy olvasási/írási hozzáférést biztosítson a fürt titkos kulcsaihoz, tekintse meg az alábbi szakaszt, és [adja hozzá a HDInsight-kezelő szerepkör-hozzárendelést egy felhasználóhoz](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Érintem ezeket a módosításokat?

A következő entitások és forgatókönyvek érintettek:

- [API](#api): a vagy a `/configurations` `/configurations/{configurationName}` végpontokat használó felhasználók.
- [Azure HDInsight-eszközök a Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1-es vagy újabb verziójához.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0 vagy újabb verzió.
- [A Visual studióhoz készült Azure Data Lake és stream Analytics eszközök a](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1 verzióban.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 3.15.0 vagy újabb verzió.
- [.NET-hez készült SDK](#sdk-for-net)
    - [1. x vagy 2. x verzió](#versions-1x-and-2x): a (z),,, `GetClusterConfigurations` `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` vagy `DisableHttp` metódust használó felhasználók a ConfigurationsOperationsExtensions osztályból.
    - [3. x és újabb verziók](#versions-3x-and-up): a `Get` osztályból a,, `Update` `EnableHttp` vagy `DisableHttp` metódust használó felhasználók `ConfigurationsOperationsExtensions` .
- [Pythonhoz készült SDK](#sdk-for-python): a `get` `update` osztályból származó vagy metódusokat használó felhasználók `ConfigurationsOperations` .
- [Javához készült SDK](#sdk-for-java): az `update` `get` osztályból származó vagy metódusokat használó felhasználók `ConfigurationsInner` .
- [SDK for go](#sdk-for-go): a `Get` struct-t vagy metódusokat használó felhasználók `Update` `ConfigurationsClient` .
- [Az az. HDInsight PowerShell](#azhdinsight-powershell) a verzió 2.0.0.
Tekintse meg az alábbi részeket (vagy használja a fenti hivatkozásokat) a forgatókönyv áttelepítési lépéseinek megtekintéséhez.

### <a name="api"></a>API

A következő API-k módosulnak vagy elavultak lesznek:

- [**/Configurations/{configurationName} beolvasása**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (bizalmas adatok eltávolítva)
    - Korábban az egyes konfigurációs típusok (például titkok) beszerzésére használták.
    - Az API-hívás 2019 szeptember 3-ától kezdődően az egyes konfigurációs típusokat is visszaadja a titkos kulcsokkal. Az összes konfiguráció, köztük a titkok beszerzéséhez használja az új POST/configurations hívást. Az átjáró beállításainak beszerzéséhez használja az új POST/getGatewaySettings hívást.
- [**/Configurations beolvasása**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (elavult)
    - Korábban az összes konfiguráció beszerzésére használták (beleértve a titkokat is)
    - A 2019. szeptember 3-ától kezdődően ez az API-hívás elavult lesz, és már nem támogatott. Az összes elérhető konfiguráció beszerzéséhez használja az új POST/configurations hívást. A bizalmas paraméterekkel rendelkező konfigurációk beszerzéséhez használja a GET/configurations/{configurationName} hívást.
- [**/Configurations/{configurationName} utáni**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (elavult)
    - Korábban az átjáró hitelesítő adatainak frissítésére használták.
    - A 2019. szeptember 3-ától kezdődően ez az API-hívás elavult lesz, és már nem támogatott. Ehelyett használja az új POST/updateGatewaySettings.

A következő helyettesítő API-k lettek hozzáadva:</span>

- [**/Configurations közzététele**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ezzel az API-val hozzájuthat az összes konfigurációhoz, beleértve a titkokat is.
- [**/GetGatewaySettings közzététele**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ezt az API-t használhatja az átjáró beállításainak beszerzéséhez.
- [**/UpdateGatewaySettings közzététele**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ezzel az API-val frissítheti az átjáró beállításait (Felhasználónév és/vagy jelszó).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure HDInsight Tools

Ha az 1.1.1-es vagy újabb verzióját használja, frissítsen az [Azure HDInsight Tools for Visual Studio Code legújabb verziójára](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) a megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Ha a 3.20.0 vagy az alábbi verziót használja, a megszakítások elkerülése érdekében frissítsen az [Azure Toolkit for IntelliJ beépülő modul legújabb verziójára](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) .

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>A Visual studióhoz készült Azure Data Lake és Stream Analytics eszközök

A megszakítások elkerülése érdekében frissítsen a 2.3.9000.1 vagy újabb verzióra a [Azure Data Lake és a stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) használatával.  A frissítéssel kapcsolatos segítségért tekintse meg a dokumentációt és a [Visual studióhoz készült Data Lake Tools frissítést](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Ha a 3.15.0 vagy az alábbi verziót használja, a megszakítások elkerülése érdekében frissítsen a [Azure Toolkit for Eclipse legújabb verziójára](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) .

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>1. x és 2. x verziók

Frissítse a .NET-hez készült HDInsight SDK [verziójának 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) . Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- `ClusterOperationsExtensions.GetClusterConfigurations` a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas paramétereket is, használja a jövőt `ClusterOperationsExtensions.ListConfigurations` .  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz.
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja a következőt: `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` a már elavult, és lecserélte `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` a már elavult, és lecserélte `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` és `DisableHttp` már elavultak. A HTTP mostantól mindig engedélyezve van, így ezek a metódusok már nem szükségesek.

#### <a name="versions-3x-and-up"></a>3. x és újabb verziók

Frissítsen a .NET-hez készült HDInsight SDK 5.0.0 vagy újabb [verziójára](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) . Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas paramétereket is, használja a jövőt [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) .Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja a következőt: [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) a már elavult, és lecserélte [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) és [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) már elavultak. A HTTP mostantól mindig engedélyezve van, így ezek a metódusok már nem szükségesek.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

A Pythonhoz készült HDInsight SDK-hoz tartozó 1.0.0 vagy újabb [verzióra](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) történő frissítés. Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas paramétereket is, használja a jövőt [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja a következőt: [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) a már elavult, és lecserélte [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>Javához készült SDK

Frissítse a Java-hoz készült HDInsight SDK-val kapcsolatos 1.0.0 vagy újabb [verzióra](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) . Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- `ConfigurationsInner.get` a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
- `ConfigurationsInner.update` már elavult.

### <a name="sdk-for-go"></a>SDK for go

Frissítsen a Go-hoz készült HDInsight SDK 27.1.0 vagy újabb [verziójára](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) . Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas paramétereket is, használja a jövőt [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) .Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja a következőt: [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) a már elavult, és lecserélte [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az az. HDInsight PowerShell
A megszakítások elkerülése érdekében frissítsen az [az PowerShell Version 2.0.0 vagy újabb verzióra](https://www.powershellgallery.com/packages/Az) .  Ha a módosítások által érintett módszert használ, minimális kód-módosításokra lehet szükség.
- `Grant-AzHDInsightHttpServicesAccess` már elavult, és az új parancsmag váltotta fel `Set-AzHDInsightGatewayCredential` .
- `Get-AzHDInsightJobOutput` frissítve lett, hogy támogassa a részletes szerepköralapú hozzáférést a tárolási kulcshoz.
    - Ez nem érinti a HDInsight-fürt operátor, közreműködő vagy tulajdonos szerepkörrel rendelkező felhasználóit.
    - A csak az olvasó szerepkörrel rendelkező felhasználók számára explicit módon kell megadni a `DefaultStorageAccountKey` paramétert.
- `Revoke-AzHDInsightHttpServicesAccess` már elavult. A HTTP mostantól mindig engedélyezve van, ezért erre a parancsmagra már nincs szükség.
 Lásd az az [. További részleteket a HDInsight áttelepítési útmutatójában](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) talál.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight-fürt szerepkör-hozzárendelésének hozzáadása egy felhasználóhoz

A [tulajdonosi](../role-based-access-control/built-in-roles.md#owner) szerepkörrel rendelkező felhasználók hozzárendelhet a [HDInsight-kezelő](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) szerepkört azokhoz a felhasználókhoz, akik számára olvasási/írási hozzáférést szeretne adni a bizalmas HDInsight-fürt konfigurációs értékeihez (például a fürt átjárójának hitelesítő adatait és a Storage-fiók kulcsait).

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

A szerepkör-hozzárendelés hozzáadásának legegyszerűbb módja a parancs használata az `az role assignment create` Azure CLI-ben.

> [!NOTE]
> Ezt a parancsot a tulajdonosi szerepkörrel rendelkező felhasználónak kell futtatnia, mivel csak ezek az engedélyek adhatók meg. A azon `--assignee` felhasználó egyszerű szolgáltatásnév vagy e-mail-címe, akihez a HDInsight-kezelő szerepkört hozzá szeretné rendelni. Ha nem rendelkezik megfelelő engedélyekkel, tekintse meg az alábbi gyakori kérdéseket.

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

A Azure Portal segítségével hozzáadhatja a HDInsight-fürt operátori szerepkör-hozzárendelését egy felhasználóhoz. Tekintse meg a dokumentációt, [rendeljen Azure-szerepköröket a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>GYIK

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Miért jelenik meg a 403-as (tiltott művelet) válasz az API-kérelmek és/vagy az eszköz frissítésekor?

A fürt konfigurációi mostantól a részletes szerepköralapú hozzáférés-vezérlés mögött vannak, és az `Microsoft.HDInsight/clusters/configurations/*` engedélyek elérését igénylik. Az engedély beszerzéséhez rendelje hozzá a HDInsight-kezelőt, a közreműködőt vagy a tulajdonosi szerepkört a felhasználóhoz vagy az egyszerű szolgáltatáshoz, és próbálja meg elérni a konfigurációkat.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Miért tekintheti meg a "nincs elegendő jogosultság a művelet végrehajtásához" kifejezést, ha az Azure CLI-parancs futtatásával rendeli hozzá a HDInsight-kezelő szerepkört egy másik felhasználóhoz vagy egyszerű szolgáltatáshoz?

A tulajdonosi szerepkör mellett a parancsot végrehajtó felhasználónak vagy szolgáltatásnak elegendő Azure AD-engedéllyel kell rendelkeznie ahhoz, hogy megkeresse a megbízott objektum-azonosítóit. Ez az üzenet nem megfelelő Azure AD-engedélyeket jelez. Próbálja meg lecserélni az argumentumot a (z) értékre, `-–assignee` `–assignee-object-id` és adja meg a hozzárendelt objektum azonosítóját paraméterként a név helyett (vagy egy felügyelt identitás esetén a résztvevő azonosítóját). További információért tekintse meg az az [role hozzárendelés Create dokumentációjának](/cli/azure/role/assignment#az-role-assignment-create) választható paraméterek szakaszát.

Ha ez továbbra sem működik, kérje meg az Azure AD-rendszergazdát, hogy szerezze be a megfelelő engedélyeket.

### <a name="what-will-happen-if-i-take-no-action"></a>Mi történik, ha nem végezek műveletet?

A 2019 szeptember 3-ától kezdődően a `GET /configurations` `POST /configurations/gateway` hívások nem adnak vissza semmilyen információt, és a `GET /configurations/{configurationName}` hívás többé nem ad vissza bizalmas paramétereket, például a Storage-fiók kulcsait vagy a fürt jelszavát. Ugyanez vonatkozik a megfelelő SDK-módszerekre és a PowerShell-parancsmagokra is.

Ha a fent említett Visual Studio-, VSCode-, IntelliJ-vagy Eclipse-eszközök valamelyikének egy régebbi verzióját használja, a továbbiakban nem fog működni, amíg nem frissíti.

Részletesebb információkért tekintse meg a jelen dokumentum megfelelő szakaszát a forgatókönyvhöz.