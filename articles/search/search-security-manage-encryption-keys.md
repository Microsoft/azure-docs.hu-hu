---
title: REST-alapú titkosítás az ügyfél által felügyelt kulcsokkal
titleSuffix: Azure Cognitive Search
description: Az indexeken és az Azure-ban található szinonimák használatával kiegészítheti a kiszolgálóoldali titkosítást, Cognitive Search a Azure Key Vaultban létrehozott és felügyelt kulcsokkal.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119288"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure-ban Cognitive Search

Az Azure Cognitive Search automatikusan titkosítja az indexelt tartalmat a [szolgáltatás által felügyelt kulcsokkal](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Ha további védelemre van szükség, az alapértelmezett titkosítás kiegészítő titkosítási réteggel kiegészíthető a Azure Key Vaultban létrehozott és kezelt kulcsok használatával. Ez a cikk végigvezeti az ügyfél által felügyelt kulcsos titkosítás beállításának lépésein.

Az ügyfél által felügyelt kulcs titkosítása [Azure Key Vault](../key-vault/general/overview.md)függ. Létrehozhatja saját titkosítási kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-jait a titkosítási kulcsok létrehozásához. A Azure Key Vault használatával a [naplózás engedélyezésekor](../key-vault/general/logging.md)is naplózhatja a kulcshasználat.  

Az ügyfél által felügyelt kulcsokkal történő titkosítást az egyes indexekre vagy a szinonimák leképezésére alkalmazza a rendszer, amikor ezek az objektumok létre lettek hozva, és nincs megadva a keresési szolgáltatás szintjén. Csak az új objektumokat lehet titkosítani. A már létező tartalmak nem titkosíthatók.

A kulcsoknak nem kell ugyanabban a kulcstartóban lenniük. Egyetlen keresési szolgáltatás több titkosított indexet vagy szinonimát is képes tárolni, amelyek mindegyike a saját ügyfél által felügyelt titkosítási kulcsokkal van titkosítva, különböző kulcstartókban tárolva. Az indexek és a szinonimák is használhatók ugyanabban a szolgáltatásban, amely nem titkosított az ügyfél által felügyelt kulcsokkal.

>[!Important]
> Ha az ügyfél által felügyelt kulcsokat implementálja, ügyeljen arra, hogy szigorú eljárásokat kövessen a Key Vault-kulcsok elforgatása során, és Active Directory az alkalmazás titkait és regisztrációját. Mindig frissítse az összes titkosított tartalmat új titkok és kulcsok használatára a régiek törlése előtt. Ha kihagyja ezt a lépést, a tartalmat nem lehet visszafejteni.

## <a name="double-encryption"></a>Dupla titkosítás

A 2020. augusztus 1. és bizonyos régiókban létrehozott szolgáltatások esetében az ügyfél által felügyelt kulcs titkosításának hatóköre magában foglalja az ideiglenes lemezeket, így [teljes körű titkosítás](search-security-overview.md#double-encryption)érhető el, amely jelenleg elérhető ezekben a régiókban: 

+ USA 2. nyugati régiója
+ USA keleti régiója
+ USA déli középső régiója
+ USA-beli államigazgatás – Virginia
+ USA-beli államigazgatás – Arizona

Ha egy másik régiót használ, vagy az augusztus 1. előtt létrehozott szolgáltatás, akkor a felügyelt kulcs titkosítása csak az adatlemezre korlátozódik, a szolgáltatás által használt ideiglenes lemezek kivételével.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben a következő eszközöket és szolgáltatásokat használják.

+ Az [Azure Cognitive Search](search-create-service-portal.md) [számlázható](search-sku-tier.md#tier-descriptions) szinten (bármely régióban alapszintű vagy újabb).
+ [Azure Key Vault](../key-vault/general/overview.md)a Key Vault a [Azure Portal](../key-vault//general/quick-create-portal.md), az [Azure CLI](../key-vault//general/quick-create-cli.md)vagy a [Azure PowerShell](../key-vault//general/quick-create-powershell.md)használatával hozható létre. ugyanabban az előfizetésben, mint az Azure Cognitive Search. A Key vaultnak engedélyezve kell lennie a helyreállítható **törlési** és **kiürítési védelemmel** .
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Ha még nem rendelkezik ilyennel, [állítson be egy új bérlőt](../active-directory/develop/quickstart-create-new-tenant.md).

Olyan keresési alkalmazással kell rendelkeznie, amely létrehozhatja a titkosított objektumot. Ebben a kódban egy Key Vault-kulcsra és Active Directory regisztrációs információra fog hivatkozni. Ez a kód lehet egy működő alkalmazás vagy prototípus-kód, például a [C# kód minta DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> A [Poster](search-get-started-rest.md), a [Visual Studio Code](search-get-started-vs-code.md)vagy a [Azure PowerShell](./search-get-started-powershell.md)használatával meghívhatja a REST API-kat, amelyek indexeket és szinonimákat hoznak létre, amelyek tartalmazzák a titkosítási kulcs paraméterét. Jelenleg nem támogatott a kulcsok indexekhez vagy szinonimához való hozzáadásához szükséges portál támogatása.

## <a name="1---enable-key-recovery"></a>1 – kulcshelyreállítás engedélyezése

Az ügyfél által felügyelt kulcsokkal való titkosítás jellegéből adódóan az Azure Key Vault-kulcs törlését követően senki nem kérheti le az adatait. Ha meg szeretné akadályozni az adatvesztést a véletlen Key Vaulti törlések miatt, a Key vaulton engedélyezni kell a törlés és a kiürítés védelmét. A Soft-delete alapértelmezés szerint engedélyezve van, ezért csak akkor lesz probléma, ha szándékosan letiltotta. A védelem kiürítése alapértelmezés szerint nincs engedélyezve, de az ügyfél által felügyelt kulcs titkosításához Cognitive Search szükséges. [További információ: a](../key-vault/general/soft-delete-overview.md) védelmi áttekintések eltávolítása és [kiürítése](../key-vault/general/soft-delete-overview.md#purge-protection) .

Mindkét tulajdonságot megadhatja a portál, a PowerShell vagy az Azure CLI-parancsok használatával.

### <a name="using-azure-portal"></a>Az Azure Portal használata

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a Key Vault áttekintés lapját.

1. Az **alapok** területen található **Áttekintés** oldalon engedélyezze a helyreállítható **törlést** és a **védelem** törlését.

### <a name="using-powershell"></a>A PowerShell használata

1. Az `Connect-AzAccount` Azure-beli hitelesítő adatok beállításához futtassa a parancsot.

1. Futtassa a következő parancsot a Key vaulthoz való kapcsolódáshoz, `<vault_name>` érvényes névvel helyettesítve:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. A Azure Key Vault a törlés engedélyezve beállítással jön létre. Ha le van tiltva a tárolón, futtassa a következő parancsot:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Kiürítési védelem engedélyezése:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Mentse a frissítéseket:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

+ Ha telepítette az [Azure CLI](/cli/azure/install-azure-cli)-t, a következő parancs futtatásával engedélyezheti a szükséges tulajdonságokat.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 – kulcs létrehozása Key Vaultban

Hagyja ki ezt a lépést, ha már rendelkezik egy kulccsal Azure Key Vaultban.

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a Key Vault áttekintés lapját.

1. Válassza a bal oldalon a **kulcsok** elemet, majd válassza a **+ előállítás/importálás** lehetőséget.

1. A **kulcs létrehozása** ablaktáblán a **Beállítások** listájából válassza ki a kulcs létrehozásához használni kívánt módszert. **Létrehozhat egy új** kulcsot, **feltöltheti** a meglévő kulcsot, vagy használhatja a **visszaállítás biztonsági mentést** a kulcsok biztonsági másolatának kiválasztásához.

1. Adja meg a kulcs **nevét** , és ha kívánja, válassza ki a többi fontos tulajdonságot.

1. A telepítés elindításához válassza a **Létrehozás** lehetőséget.

1. Jegyezze fel a kulcs azonosítóját, amely a kulcs **értékének URI-ja**, a **kulcs neve** és a **kulcs verziószáma**. Az Azure Cognitive Search-ben titkosított index definiálásához szüksége lesz az azonosítóra.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Új Key Vault-kulcs létrehozása":::

## <a name="3---register-an-app-in-active-directory"></a>3 – alkalmazás regisztrálása Active Directory

1. A [Azure Portalban](https://portal.azure.com)keresse meg az előfizetéséhez Azure Active Directory erőforrást.

1. A bal oldalon a **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.

1. Adja meg a regisztráció nevét, például egy nevet, amely hasonló a keresési alkalmazás nevéhez. Válassza a **Regisztráció** lehetőséget.

1. Az alkalmazás regisztrációjának létrehozása után másolja ki az alkalmazás AZONOSÍTÓját. Ezt a karakterláncot meg kell adnia az alkalmazás számára. 

   Ha a [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)halad át, illessze be ezt az értéket a fájl **appsettings.jsba** .

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Az alkalmazás azonosítója az Essentials szakaszban":::

1. Ezután válassza ki a **tanúsítványok & a titkokat** a bal oldalon.

1. Válassza az **Új titkos ügyfélkód** lehetőséget. Adjon egy megjelenítendő nevet a titkos kulcsnak, majd válassza a **Hozzáadás** lehetőséget.

1. Másolja ki az alkalmazás titkos kulcsát. Ha átlépi a mintát, illessze be ezt az értéket a fájl **appsettings.jsba** .

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Alkalmazás titkos kódja":::

## <a name="4---grant-key-access-permissions"></a>4 – kulcsokra vonatkozó hozzáférési engedélyek megadása

Ebben a lépésben egy hozzáférési szabályzatot fog létrehozni Key Vaultban. Ez a szabályzat lehetővé teszi, hogy az alkalmazás regisztrálva legyen Active Directory engedéllyel az ügyfél által felügyelt kulcs használatához.

A hozzáférési engedélyeket bármikor visszavonhatja. A visszavonás után a Key vaultot használó keresési szolgáltatás indexe vagy szinonimája használhatatlan lesz. A Key Vault-hozzáférési engedélyek későbbi visszaállításakor a rendszer visszaállítja a index\synonym-leképezési hozzáférést. További információt a [kulcstartó biztonságos elérését](../key-vault/general/secure-your-key-vault.md)ismertető témakörben talál.

1. Még mindig a Azure Portalban nyissa meg a Key Vault **Áttekintés** lapját. 

1. Válassza ki a **hozzáférési házirendeket** a bal oldalon, majd válassza a **+ hozzáférési házirend hozzáadása** lehetőséget.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Új Key Vault hozzáférési szabályzat hozzáadása":::

1. Válassza a **rendszerbiztonsági tag kiválasztása** lehetőséget, és válassza ki a Active Directory regisztrált alkalmazást. A név alapján kereshet.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="A Key Vault hozzáférési szabályzatának kiválasztása":::

1. A **legfontosabb engedélyek** területen válassza a *beolvasás*, a *kulcs kicsomagolása* és a *becsomagolási kulcs* elemet.

1. A **titkos engedélyek** területen válassza a *beolvasás* lehetőséget.

1. A **tanúsítvány engedélyei** területen válassza a *beolvasás* lehetőséget.

1. Válassza a **Hozzáadás** , majd a **Mentés** lehetőséget.

> [!Important]
> Az Azure Cognitive Search titkosított tartalma úgy van konfigurálva, hogy egy meghatározott Azure Key Vault-kulcsot használjon egy adott **verzióval**. Ha megváltoztatja a kulcsot vagy a verziót, az indexet vagy a szinonima-térképet frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, a kulcs elérésének elvesztése után nem fogja tudni visszafejteni a tartalmat.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 – tartalom titkosítása

Ha egy ügyfél által felügyelt kulcsot szeretne felvenni egy indexre, adatforrásra, készségkészlet, indexelő vagy szinonimára, akkor a [Search REST API](/rest/api/searchservice/) vagy egy SDK-t kell használnia. A portál nem teszi lehetővé a szinonimák leképezését vagy a titkosítási tulajdonságokat. Ha érvényes API-indexeket használ, az adatforrások, a szakértelmével, az indexelő és a szinonimák leképezései támogatják a legfelső szintű **encryptionKey** tulajdonságot.

Ebben a példában a REST API a Azure Key Vault és a Azure Active Directory értékeit használja:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Ezek a kulcstartó-részletek egyike sem minősül titkosnak, és könnyen lekérhető, ha megkeresi a megfelelő Azure Key Vault kulcsot tartalmazó lapot Azure Portal.

## <a name="example-index-encryption"></a>Példa: index encryption

Hozzon létre egy titkosított indexet a [create index Azure Cognitive Search REST API](/rest/api/searchservice/create-index)használatával. A `encryptionKey` tulajdonság használatával határozza meg a használandó titkosítási kulcsot.
> [!Note]
> Ezek a kulcstartó-részletek egyike sem minősül titkosnak, és könnyen lekérhető, ha megkeresi a megfelelő Azure Key Vault kulcsot tartalmazó lapot Azure Portal.

## <a name="rest-examples"></a>REST-példák

Ez a szakasz a titkosított indexek és a szinonimák megfeleltetésének teljes JSON-fájlját mutatja

### <a name="index-encryption"></a>Index titkosítása

Az új indexnek a REST API használatával történő létrehozásának részletei a [create index (REST API)](/rest/api/searchservice/create-index)helyen találhatók, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása az index definíciójának részeként:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Most már elküldheti az index-létrehozási kérelmet, majd megkezdheti a normál index használatát.

### <a name="synonym-map-encryption"></a>Szinonimák leképezésének titkosítása

Hozzon létre egy titkosított szinonima-leképezést a [szinonimák leképezése Azure Cognitive Search REST API](/rest/api/searchservice/create-synonym-map)használatával. A `encryptionKey` tulajdonság használatával határozza meg a használandó titkosítási kulcsot.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Most már elküldheti a szinonima-hozzárendelési kérést, majd normál módon megkezdheti a használatát.

## <a name="example-data-source-encryption"></a>Példa: adatforrás titkosítása

Hozzon létre egy titkosított adatforrást az [adatforrás létrehozása (Azure Cognitive Search REST API)](/rest/api/searchservice/create-data-source)használatával. A `encryptionKey` tulajdonság használatával határozza meg a használandó titkosítási kulcsot.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Most már elküldheti az adatforrás-létrehozási kérést, majd megkezdheti a szokásos használatát.

## <a name="example-skillset-encryption"></a>Példa: Készségkészlet titkosítás

Hozzon létre egy titkosított készségkészlet a [create Készségkészlet Azure Cognitive Search REST API](/rest/api/searchservice/create-skillset)használatával. A `encryptionKey` tulajdonság használatával határozza meg a használandó titkosítási kulcsot.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Most már elküldheti a készségkészlet-létrehozási kérelmet, majd megkezdheti a szokásos használatát.

## <a name="example-indexer-encryption"></a>Példa: indexelő titkosítás

Hozzon létre egy titkosított indexelő a [create indexelő Azure Cognitive Search REST API](/rest/api/searchservice/create-indexer)használatával. A `encryptionKey` tulajdonság használatával határozza meg a használandó titkosítási kulcsot.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Most már elküldheti az indexelő létrehozására vonatkozó kérést, majd megkezdheti a szokásos használatát.

>[!Important]
> Habár `encryptionKey` nem adható hozzá a meglévő keresési indexekhez vagy a szinonima térképekhez, a három kulcstartó részleteinek (például a kulcs verziójának frissítése) különböző értékeit is frissítheti. Új Key Vault kulcsra vagy új kulcs verzióra való váltáskor a kulcsot használó keresési indexeket vagy szinonimákat először frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, mivel a kulcs elérésének elvesztése után nem tudja visszafejteni a tartalmat. Bár a Key Vault-hozzáférési engedélyek későbbi visszaállítása a tartalom-hozzáférés visszaállítását eredményezi.

## <a name="simpler-alternative-trusted-service"></a>Egyszerűbb alternatíva: megbízható szolgáltatás

A bérlői konfigurációtól és a hitelesítési követelményektől függően előfordulhat, hogy a Key Vault-kulcs eléréséhez egyszerűbb módszert kell alkalmazni. Active Directory-alkalmazás létrehozása és használata helyett a keresési szolgáltatás megbízható szolgáltatást tesz lehetővé, ha a rendszer által felügyelt identitást engedélyez. Ezt követően a megbízható keresési szolgáltatást biztonsági alapelvként használhatja, nem pedig AD-regisztrált alkalmazásként a Key Vault-kulcs eléréséhez.

Ezzel a módszerrel kihagyhatja az alkalmazás regisztrációjának és az alkalmazás titkos kulcsainak lépéseit, és leegyszerűsíti a titkosítási kulcs definícióját, hogy csak a Key Vault-összetevők (URI, tároló neve, kulcs verziója) legyenek.

A felügyelt identitás általában lehetővé teszi, hogy a keresési szolgáltatás a hitelesítő adatok (ApplicationID vagy ApplicationSecret) a kódban való tárolása nélkül hitelesítse Azure Key Vault. Az ilyen típusú felügyelt identitás életciklusa a keresési szolgáltatás életciklusához van kötve, amely csak egyetlen felügyelt identitással rendelkezhet. A felügyelt identitások működésével kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md).

1. Végezzen megbízható szolgáltatást a keresési szolgáltatásban.
   ![A rendszerhez rendelt felügyelt identitás bekapcsolása](./media/search-managed-identities/turn-on-system-assigned-identity.png "A rendszerhez rendelt felügyelt identitás bekapcsolása")

1. Amikor Azure Key Vault hozzáférési szabályzatot állít be, válassza ki a megbízható keresési szolgáltatást (az AD-regisztrált alkalmazás helyett). Rendelje hozzá ugyanezeket az engedélyeket (több beolvasás, becsomagolás, kicsomagolás), ahogyan az a hozzáférési kulcs engedélyeinek megadása lépés utasításai szerint.

1. A `encryptionKey` Active Directory tulajdonságainak kihagyása az egyszerűsített szerkezettel.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Az egyszerűsített megközelítés bevezetését megakadályozó feltételek a következők:

+ A keresési szolgáltatás hozzáférési engedélyeit nem lehet közvetlenül a kulcstartóhoz adni (például ha a keresési szolgáltatás más Active Directory bérlő, mint a Azure Key Vault).

+ Egyetlen keresési szolgáltatás szükséges több titkosított indexes\synonym-Térkép üzemeltetéséhez, amelyek mindegyike egy másik Kulcstartóból származó másik kulcsot használ, ahol minden kulcstartónak **más identitást** kell használnia a hitelesítéshez. Mivel a keresési szolgáltatás csak egyetlen felügyelt identitással rendelkezhet, a több identitásra vonatkozó követelmények nem foglalják magukban a forgatókönyv egyszerűsített megközelítését.  

## <a name="work-with-encrypted-content"></a>Titkosított tartalom használata

Az ügyfél által felügyelt kulcs titkosításával a további titkosítási/visszafejtési munkák miatt megfigyelheti az indexelési és a lekérdezési késést is. Az Azure Cognitive Search nem naplózza a titkosítási tevékenységeket, de a Key Vault naplózási szolgáltatáson keresztül figyelheti a kulcsokhoz való hozzáférést. Javasoljuk, hogy [engedélyezze a naplózást](../key-vault/general/logging.md) a Key Vault konfigurációjának részeként.

A kulcs rotációja várhatóan az idő múlásával történik. A kulcsok elforgatásakor fontos, hogy kövesse ezt a sorozatot:

1. [Az index vagy a szinonima Térkép által használt kulcs meghatározása](search-security-get-encryption-keys.md).
1. [Hozzon létre egy új kulcsot a Key vaultban](../key-vault/keys/quick-create-portal.md), de hagyja elérhetővé az eredeti kulcsot.
1. Az új értékek használatához [frissítse a encryptionKey tulajdonságait](/rest/api/searchservice/update-index) egy index vagy szinonima leképezésben. Csak azokat az objektumokat lehet frissíteni, amelyeket eredetileg ezzel a tulajdonsággal hoztak létre, hogy más értéket használjanak.
1. Tiltsa le vagy törölje az előző kulcsot a Key vaultban. A kulcs-hozzáférés figyelése az új kulcs használatának ellenőrzéséhez.

A keresési szolgáltatás teljesítménybeli okokból akár több óráig is gyorsítótárazza a kulcsot. Ha letiltja vagy törli a kulcsot anélkül, hogy újat kellene benyújtania, a lekérdezések ideiglenesen is működni fognak, amíg a gyorsítótár le nem jár. Ha azonban a keresési szolgáltatás nem tudja visszafejteni a tartalmat, a következő üzenet jelenik meg: "Hozzáférés megtiltva. Lehetséges, hogy a használt lekérdezési kulcsot visszavonták – próbálkozzon újra. " 

## <a name="next-steps"></a>Következő lépések

Ha nem ismeri az Azure biztonsági architektúráját, tekintse át az [Azure biztonsági dokumentációját](../security/index.yml), és különösen a következő cikket:

> [!div class="nextstepaction"]
> [Inaktív adatok titkosítása](../security/fundamentals/encryption-atrest.md)