---
title: Titkosítás az ügyfelek által kezelt kulcsokkal
titleSuffix: Azure Cognitive Search
description: Egészítenie ki az indexek és szinonimatérképek kiszolgálóoldali titkosítását a Azure Cognitive Search által létrehozott és a Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813613"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Felhasználó által kezelt kulcsok konfigurálása adattitkosításhoz a Azure Cognitive Search

Azure Cognitive Search szolgáltatás által felügyelt kulcsokkal automatikusan titkosítja az indexelt [tartalmakat.](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components) Ha nagyobb védelemre van szükség, kiegészítheti az alapértelmezett titkosítást egy további titkosítási réteggel, az itt létrehozott és Azure Key Vault. Ez a cikk végigvezeti az ügyfél által felügyelt kulcstitkosítás beállításának lépésein.

Az ügyfél által kezelt [](../key-vault/general/overview.md)kulcstitkosítás a(Azure Key Vault. Létrehozhatja saját titkosítási kulcsait, és kulcstartóban tárolhatja őket, vagy használhatja Azure Key Vault API-ját a titkosítási kulcsok létrehozásához. A Azure Key Vault naplót is naplót kaphat a kulcshasználatról, ha [engedélyezi a naplózást.](../key-vault/general/logging.md)  

Az ügyfél által kezelt kulcsokkal való titkosítás az objektumok létrehozásakor az egyes indexek vagy szinonimatérképek esetén lesz alkalmazva, és nem a keresési szolgáltatás szintjén van megadva. Csak új objektumok titkosíthatóak. A már létező tartalmak nem titkosíthatóak.

A kulcsoknak nem kell ugyanabban a kulcstartóban lennie. Egyetlen keresési szolgáltatás több titkosított indexet vagy szinonimatérképet is képes tárolni, amelyek mindegyikét a saját, ügyfél által felügyelt titkosítási kulcsokkal titkosítják, és különböző kulcstartókban vannak tárolva. Ugyanazon szolgáltatásban olyan indexekkel és szinonimatérképekkel is lehet, amelyek nincsenek ügyfél által kezelt kulcsokkal titkosítva.

>[!Important]
> Ha ügyfél által kezelt kulcsokat implement, mindig kövesse a szigorú eljárásokat a kulcstartókulcsok rutinszerű rotációja során, és Active Directory titkos alkalmazáskulcsokat és -regisztrációt. A régiek törlése előtt mindig frissítse az összes titkosított tartalmat, hogy az új titkos kódokat és kulcsokat használja. Ha kihagyja ezt a lépést, a tartalom nem lesz visszafejthető.

## <a name="double-encryption"></a>Dupla titkosítás

A 2020. augusztus 1. után és adott régiókban létrehozott szolgáltatások esetében az ügyfél által felügyelt kulcstitkosítás hatóköre ideiglenes lemezeket is magában foglal, így teljes dupla titkosítást [biztosít,](search-security-overview.md#double-encryption)amely jelenleg az alábbi régiókban érhető el: 

+ USA 2. nyugati régiója
+ USA keleti régiója
+ USA déli középső régiója
+ USA-beli államigazgatás – Virginia
+ USA-beli államigazgatás – Arizona

Ha másik régiót vagy augusztus 1. előtt létrehozott szolgáltatást használ, akkor a felügyelt kulcstitkosítás csak az adatlemezre korlátozódik, kivéve a szolgáltatás által használt ideiglenes lemezeket.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben a következő eszközöket és szolgáltatásokat használjuk.

+ [Azure Cognitive Search](search-create-service-portal.md) számlázható [](search-sku-tier.md#tier-descriptions) szinten (alapszintű vagy magasabb, bármely régióban).
+ [Azure Key Vault](../key-vault/general/overview.md), kulcstartót a [Azure Portal,](../key-vault//general/quick-create-portal.md) [az Azure CLI](../key-vault//general/quick-create-cli.md)vagy [a](../key-vault//general/quick-create-powershell.md)Azure PowerShell. ugyanabban az előfizetésben, mint Azure Cognitive Search. A kulcstartón engedélyezni kell a **soft-delete** és **a purge protection** beállítást.
+ [Azure Active Directory:](../active-directory/fundamentals/active-directory-whatis.md). Ha még nem rendelkezik bérlővel, állítson be [egy új bérlőt.](../active-directory/develop/quickstart-create-new-tenant.md)

Olyan keresőalkalmazással kell lennie, amely képes létrehozni a titkosított objektumot. Ebben a kódban egy key vault-kulcsra fog hivatkozni, és Active Directory adatokat. Ez a kód lehet egy működő alkalmazás, vagy egy prototípus kódja, például a [DotNetHowToEncryptionUsingCMK C#-kódminta.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)

> [!TIP]
> A [Postman,](search-get-started-rest.md) [a Visual Studio Code](search-get-started-vs-code.md)vagy a [Azure PowerShell](./search-get-started-powershell.md)használatával hívhatja meg a titkosítási kulcsparamétert is magában foglaló indexeket és szinonimatérképeket létrehozására képes REST API-kat. A portál jelenleg nem támogatja kulcs hozzáadását az indexek vagy szinonimatérképek számára.

## <a name="1---enable-key-recovery"></a>1 – Kulcs helyreállításának engedélyezése

Az ügyfél által felügyelt kulcsokkal való titkosítás természetéből adódóan senki sem tudja lekérni az adatokat az Azure Key Vault-kulcs törlésekor. A kulcs véletlen törlése által okozott adatvesztés Key Vault a kulcstartón engedélyezni kell a törlés és végleges törlés elleni védelmet. A soft-delete alapértelmezés szerint engedélyezve van, így csak akkor fog problémákba ütközni, ha szándékosan letiltotta. A véglegesen kiürítés elleni védelem alapértelmezés szerint nincs engedélyezve, de az ügyfél által felügyelt kulcstitkosításhoz szükséges a Cognitive Search. További információ: A [törlés és végleges](../key-vault/general/soft-delete-overview.md) törlés elleni védelem áttekintése. [](../key-vault/general/soft-delete-overview.md#purge-protection)

Mindkét tulajdonságot a portál, a PowerShell vagy az Azure CLI parancsaival állíthatja be.

### <a name="using-azure-portal"></a>Az Azure Portal használata

1. [Jelentkezzen be a Azure Portal,](https://portal.azure.com) és nyissa meg a Kulcstartó áttekintő lapját.

1. Az Áttekintés **lap Alapvető** szolgáltatások **területén** engedélyezze a **Soft-delete** és **a Purge protection (Védelem végleges törlése) beállítását.**

### <a name="using-powershell"></a>A PowerShell használata

1. Az `Connect-AzAccount` Azure-beli hitelesítő adatok beállítását az futtatásával állíthatja be.

1. Futtassa a következő parancsot a kulcstartóhoz való csatlakozáshoz, és cserélje le a kódot `<vault_name>` egy érvényes névre:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault a rendszer a soft-delete engedélyezése esetén jön létre. Ha le van tiltva a tárolón, futtassa a következő parancsot:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Véglegesen kiürítés elleni védelem engedélyezése:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Mentse a frissítéseket:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

+ Ha telepítette az [Azure CLI-t,](/cli/azure/install-azure-cli)a következő parancs futtatásával engedélyezheti a szükséges tulajdonságokat.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 – Kulcs létrehozása a Key Vault

Hagyja ki ezt a lépést, ha már rendelkezik kulccsal a Azure Key Vault.

1. [Jelentkezzen be a Azure Portal,](https://portal.azure.com) és nyissa meg a Kulcstartó áttekintő lapját.

1. A **bal oldalon** válassza a Kulcsok, majd a + **Generálás/Importálás lehetőséget.**

1. A **Kulcs létrehozása panel beállítások** listájából válassza ki a kulcs létrehozásához használni kívánt metódust.  Létrehozhat egy **új** **kulcsot,** feltölthet egy meglévő kulcsot, vagy használhatja a **Biztonsági** másolat visszaállítása lehetőséget a kulcs biztonsági másolatának kiválasztásához.

1. Adja meg a **kulcs** nevét, és ha szükséges, válasszon más kulcstulajdonságokat.

1. Az **üzembe helyezéshez** válassza a Létrehozás lehetőséget.

1. Jegyezze fel a kulcsazonosítót – ez a kulcs értékből  **,** a kulcsnévből és a **kulcsverzióból áll.** Az azonosítóra szüksége lesz egy titkosított index meghatározásához a Azure Cognitive Search.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Új key vault-kulcs létrehozása":::

## <a name="3---register-an-app-in-active-directory"></a>3 – Alkalmazás regisztrálása a Active Directory

1. A [Azure Portal](https://portal.azure.com)keresse meg az Azure Active Directory erőforrását.

1. A bal oldali Kezelés **alatt** válassza **a** Alkalmazásregisztrációk, majd az Új regisztráció **lehetőséget.**

1. Nevezze el a regisztrációt, például a keresési alkalmazás nevéhez hasonló nevet. Válassza a **Regisztráció** lehetőséget.

1. Az alkalmazásregisztráció létrehozása után másolja ki az alkalmazásazonosítót. Ezt a sztringet meg kell adnia az alkalmazásnak. 

   Ha végiglépked a [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)parancson, illessze be ezt az értéket **appsettings.jsfájlban található fájlba.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Alkalmazásazonosító az Alapvető szolgáltatások szakaszban":::

1. Ezután válassza a **Certificates & secrets (Tanúsítványok és** titkos kulcsok) lehetőséget a bal oldalon.

1. Válassza az **Új titkos ügyfélkód** lehetőséget. Adjon megjelenítendő nevet a titkos nak, majd válassza a **Hozzáadás lehetőséget.**

1. Másolja ki az alkalmazás titkos ját. Ha végiglépked a mintán, illessze be ezt az értéket **appsettings.jsfájlban található fájlba.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Alkalmazás titkos kódja":::

## <a name="4---grant-key-access-permissions"></a>4 – Kulcs-hozzáférési engedélyek megadása

Ebben a lépésben egy hozzáférési szabályzatot fog létrehozni a Key Vault. Ez a szabályzat engedélyt ad a Active Directory regisztrált alkalmazásnak az ügyfél által kezelt kulcs használatára.

A hozzáférési engedélyek bármikor visszavonhatóak. A visszavonás után a kulcstartót használó keresési szolgáltatás indexe vagy szinonimatérképe használhatatlanná válik. A Key Vault hozzáférési engedélyeinek későbbi visszaállítása visszaállítja az index-szinonimatérkép-hozzáférést. További információ: [Kulcstartóhoz való biztonságos hozzáférés.](../key-vault/general/security-features.md)

1. Még mindig a Azure Portal nyissa meg a kulcstartó Áttekintés **lapját.** 

1. Válassza a **bal oldalon a** Hozzáférési szabályzatok, majd a **+ Hozzáférési szabályzat hozzáadása lehetőséget.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Új kulcstartó-hozzáférési szabályzat hozzáadása":::

1. Válassza **a Rendszerbiztonsági tag kiválasztása** lehetőséget, majd válassza ki az alkalmazáshoz Active Directory. Név szerint is rákereshet.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Kulcstartó-hozzáférési szabályzatnév kiválasztása":::

1. A **Kulcsengedélyek között válassza** a *Lekért,* *Kulcs kicsomagzása és* Kulcs *becsomagolása lehetőséget.*

1. A **Secret Permissions (Titkos engedélyek) mezőben** válassza a *Get (Lekért) lehetőséget.*

1. A **Tanúsítványengedélyek mezőben** válassza a *Lekér lehetőséget.*

1. Válassza a **Hozzáadás,** majd a **Mentés lehetőséget.**

> [!Important]
> A titkosítási Azure Cognitive Search úgy van konfigurálva, hogy egy adott Azure Key Vault kulcsát használja egy adott **verzióval.** Ha módosítja a kulcsot vagy a verziót, az előző kulcs\verzió  törlése előtt frissítenie kell az index- vagy szinonimatérképet az új kulcs\verzió használatára. Ha ezt nem teszi meg, akkor az index vagy a szinonimatérkép használhatatlanná válik, és a kulcshoz való hozzáférés elveszése után nem tudja visszafejteni a tartalmat.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 – Tartalom titkosítása

Ha ügyfél által felügyelt kulcsot szeretne hozzáadni egy indexhez, adatforráshoz, képességkészlethez, indexelőhöz vagy szinonimatérképhez, a Search REST API [vagy](/rest/api/searchservice/) egy SDK-t kell használnia. A portál nem teszi elérhetővé a szinonimatérképeket és a titkosítási tulajdonságokat. Érvényes API-indexek használata esetén az adatforrások, képességkészletek, indexelők és szinonimatérképek támogatják a legfelső szintű **encryptionKey tulajdonságot.**

Ez a példa a REST API a következő értékek Azure Key Vault és Azure Active Directory:

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
> A Key Vault adatai közül egyik sem számít titkosnak, és könnyen lekérhető, ha a kulcs Azure Key Vault a megfelelő Azure Portal.

## <a name="example-index-encryption"></a>Példa: Indextitkosítás

Hozzon létre egy titkosított indexet az [Index létrehozása Azure Cognitive Search REST API.](/rest/api/searchservice/create-index) Használja a `encryptionKey` tulajdonságot a használni kívánt titkosítási kulcs megadásához.
> [!Note]
> Ezen kulcstartóadatok egyike sem számít titkosnak, és könnyen lekérhető a kulcstartó megfelelő Azure Key Vault oldalán a Azure Portal.

## <a name="rest-examples"></a>REST-példák

Ebben a szakaszban egy titkosított index és szinonimatérkép teljes JSON-fájlja látható

### <a name="index-encryption"></a>Indextitkosítás

Az új index létrehozása a REST API használatával a Create [Index (REST API)](/rest/api/searchservice/create-index)(Index létrehozása ( REST API) )címen található, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása az indexdefiníció részeként:

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

Most már elküldheti az index-létrehozási kérelmet, majd elkezdheti használni az indexet a szokásos módon.

### <a name="synonym-map-encryption"></a>Szinonimatérkép titkosítása

Hozzon létre egy titkosított szinonimatérképet a [Szinonimatérkép létrehozása Azure Cognitive Search REST API.](/rest/api/searchservice/create-synonym-map) Használja a `encryptionKey` tulajdonságot a használni kívánt titkosítási kulcs megadásához.

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

Most már elküldheti a szinonimatérkép-létrehozási kérelmet, majd elkezdheti a normál módon használni.

## <a name="example-data-source-encryption"></a>Példa: Adatforrás titkosítása

Hozzon létre egy titkosított adatforrást az Adatforrás létrehozása [(Azure Cognitive Search REST API) használatával.](/rest/api/searchservice/create-data-source) Használja a `encryptionKey` tulajdonságot a használni kívánt titkosítási kulcs megadásához.

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

Most már elküldheti az adatforrás-létrehozási kérelmet, majd elkezdheti a normál módon használni.

## <a name="example-skillset-encryption"></a>Példa: Képességkészlet-titkosítás

Hozzon létre egy titkosított képességkészletet a [Képességkészlet létrehozása Azure Cognitive Search REST API.](/rest/api/searchservice/create-skillset) Használja a `encryptionKey` tulajdonságot a használni kívánt titkosítási kulcs megadásához.

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

Most már elküldheti a képességkészlet-létrehozási kérelmet, majd elkezdheti normál módon használni.

## <a name="example-indexer-encryption"></a>Példa: Indexelőtitkosítás

Hozzon létre egy titkosított indexelőt az [Indexelő létrehozása Azure Cognitive Search REST API.](/rest/api/searchservice/create-indexer) Használja a `encryptionKey` tulajdonságot a használni kívánt titkosítási kulcs megadásához.

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

Most már elküldheti az indexelő létrehozási kérelmét, majd elkezdheti a normál módon használni.

>[!Important]
> Bár nem lehet hozzáadni a meglévő keresési indexhez vagy szinonimatérképhez, frissíthető úgy, hogy különböző értékeket ad meg a három kulcstartó bármelyikének adataihoz (például a kulcsverzió `encryptionKey` frissítéséhez). Key Vault Új kulcs vagy új kulcsverzió módosításakor a kulcsot használó keresési indexet vagy szinonimatérképet először  frissíteni kell az új kulcs\verzió használatára az előző kulcs\verzió törlése előtt. Ha ezt elmulasztja, az index vagy a szinonimatérkép használhatatlanná válik, mivel a kulcshoz való hozzáférés elveszése után nem tudja visszafejteni a tartalmat. Bár a Key Vault hozzáférési engedélyeinek későbbi visszaállítása visszaállítja a tartalom-hozzáférést.

## <a name="simpler-alternative-trusted-service"></a>Egyszerűbb alternatíva: Megbízható szolgáltatás

A bérlő konfigurációs és hitelesítési követelményeitől függően előfordulhat, hogy egy egyszerűbb módszert is megvalósíthat a kulcstartókulcsok eléréséhez. A szolgáltatásalkalmazások létrehozása és Active Directory helyett megbízható szolgáltatásként is lehet keresési szolgáltatást létrehozni egy rendszer által felügyelt identitás engedélyezésével. Ezután a megbízható keresési szolgáltatást az AD-hez regisztrált alkalmazás helyett biztonsági alapelvként használná a kulcstartó kulcsának eléréséhez.

Ez a megközelítés lehetővé teszi, hogy kihagyja az alkalmazásregisztráció és az alkalmazás titkos kulcsának lépéseit, és leegyszerűsíti a titkosítási kulcs definícióját csak a Key Vault összetevőire (URI, tároló neve, kulcsverzió).

A felügyelt identitások általában lehetővé teszik, hogy a keresési szolgáltatás anélkül Azure Key Vault hitelesítő adatokat (ApplicationID vagy ApplicationSecret) tároljon a kódban. Az ilyen típusú felügyelt identitások életciklusa a keresési szolgáltatás életciklusához van kötve, amely csak egy felügyelt identitással lehet. A felügyelt identitások használatával kapcsolatos további információkért lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md).

1. A keresési szolgáltatás legyen megbízható szolgáltatás.
   ![Rendszer által hozzárendelt felügyelt identitás bekapcsolás](./media/search-managed-identities/turn-on-system-assigned-identity.png "Rendszer által hozzárendelt felügyelt identitás bekapcsolás")

1. Amikor hozzáférési szabályzatot Azure Key Vault, válassza a megbízható keresési szolgáltatást alapelvként (az AD-ban regisztrált alkalmazás helyett). Rendelje hozzá ugyanazokhoz az engedélyekhez (több GET, WRAP, UNWRAP) a hozzáférési kulcs engedélyeinek megadása lépés utasításainak megfelelően.

1. Használja a egyszerűsített szerkezetét, amely kihagyja a Active Directory `encryptionKey` tulajdonságait.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Olyan feltételek, amelyek megakadályozzák ennek az egyszerűsített megközelítésnek az alkalmazását:

+ Közvetlenül nem adhat hozzáférési engedélyeket a keresési szolgáltatásnak a Key Vaulthoz (például ha a keresési szolgáltatás egy másik Active Directory van, mint a Azure Key Vault).

+ Egyetlen keresési szolgáltatásra van szükség több titkosított index/szinonimatérkép gazdagépéhez, amelyek mindegyikéhez más kulcs szükséges, mint egy másik Kulcstartóhoz, ahol minden kulcstartónak más identitást kell **használnia a** hitelesítéshez. Mivel a keresési szolgáltatás csak egy felügyelt identitással lehet, a több identitásra vonatkozó követelmények kizárják a forgatókönyv egyszerűsített megközelítését.  

## <a name="work-with-encrypted-content"></a>Titkosított tartalommal való munka

Az ügyfél által felügyelt kulcstitkosítással az indexelés és a lekérdezések esetén is késést fog látni az extra titkosítási/visszafejtési munka miatt. Azure Cognitive Search nem naplóz titkosítási tevékenységet, de a kulcstartó-naplózással monitorozhatja a kulcselérést. Javasoljuk, hogy [engedélyezze a naplózást](../key-vault/general/logging.md) a Key Vault konfigurációjának részeként.

A kulcsrotáció várhatóan idővel meg fog következni. A kulcsok elforgatásakor fontos a következő sorrendet követni:

1. [Határozza meg az index- vagy szinonimatérkép által használt kulcsot.](search-security-get-encryption-keys.md)
1. [Hozzon létre egy új kulcsot a kulcstartóban,](../key-vault/keys/quick-create-portal.md)de hagyja elérhetően az eredeti kulcsot.
1. [Frissítse az encryptionKey tulajdonságokat](/rest/api/searchservice/update-index) egy index- vagy szinonimatérképen az új értékek használatára. Csak az ezzel a tulajdonsággal eredetileg létrehozott objektumok frissíthetők egy másik érték használatára.
1. Tiltsa le vagy törölje az előző kulcsot a kulcstartóban. A kulcselérés figyelése annak ellenőrzéséhez, hogy az új kulcs van-e használva.

Teljesítménybeli okokból a keresési szolgáltatás akár több óráig is gyorsítótárazza a kulcsot. Ha egy új kulcs létrehozása nélkül tiltja le vagy törli a kulcsot, a lekérdezések ideiglenesen továbbra is működni fognak, amíg a gyorsítótár le nem jár. Ha azonban a keresési szolgáltatás nem tudja visszafejteni a tartalmat, a következő üzenet jelenik meg: "Tiltott hozzáférés. Előfordulhat, hogy a használt lekérdezési kulcs vissza lett vonva – kérjük, próbálja újra." 

## <a name="next-steps"></a>Következő lépések

Ha nem ismeri az Azure biztonsági architektúráját, tekintse át az [Azure Security](../security/index.yml)dokumentációját, és különösen ezt a cikket:

> [!div class="nextstepaction"]
> [Inaktív adatok titkosítása](../security/fundamentals/encryption-atrest.md)