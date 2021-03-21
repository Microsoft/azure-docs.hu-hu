---
title: A Java használata az ACL-ek beállításához Azure Data Lake Storage Gen2
description: A Javához készült Azure Storage-kódtárak segítségével kezelheti a hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeknél engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654225"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>A Java használata a hozzáférés-vezérlési listák kezeléséhez Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan használhatja a Java-t a címtárak és fájlok hozzáférés-vezérlési listája beolvasására, beállítására és frissítésére. 

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-eket rekurzív módon is hozzáadhatja, frissítheti és eltávolíthatja a szülő könyvtár meglévő alárendelt elemein anélkül, hogy ezeket a módosításokat külön kellene elvégeznie az egyes alárendelt elemek esetében. 

[Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referenciák](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

- A következő biztonsági engedélyek egyike:

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.  

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre az ACL-beállításokat alkalmazni kívánja. Az ACL-ek rekurzív beállításához a cél tárolóban vagy a címtárban található összes alárendelt elem szerepel.
  
  - Storage-fiók kulcsa.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként nyissa meg [ezt a lapot](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) , és keresse meg a Java-könyvtár legújabb verzióját. Ezután nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adjon hozzá egy függőségi elemet, amely hivatkozik erre a verzióra.

Ha az ügyfélalkalmazás Azure Active Directory (AD) használatával történő hitelesítését tervezi, akkor vegyen fel egy függőséget az Azure Secret ügyféloldali függvénytárba. Lásd:  [a titkos ügyféloldali kódtár csomag hozzáadása a projekthez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ezután adja hozzá ezeket az importálási utasításokat a programkódhoz.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [Javához készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Szerezze be az ügyfél-azonosítót, az ügyfél titkos kulcsát és a bérlő AZONOSÍTÓját. Ehhez tekintse meg [a jogkivonat beszerzése az Azure ad-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört. A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> További példákért tekintse meg a Java-dokumentációhoz készült [Azure Identity ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL-ek beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL](#update-acls) -EK című szakaszát.  

Ha úgy dönt, hogy *Beállítja* az ACL-t, hozzá kell adnia egy bejegyzést a tulajdonos felhasználóhoz, egy bejegyzést a tulajdonos csoport számára, valamint egy bejegyzést az összes többi felhasználó számára. Ha többet szeretne megtudni a tulajdonos felhasználóról, a tulajdonos csoportról és az összes többi felhasználóról, tekintse meg a [felhasználók és az identitások](data-lake-storage-access-control.md#users-and-identities)című témakört.

Ez a szakasz a következőket mutatja be:

- Címtár ACL-listájának beállítása
- Fájl hozzáférés-vezérlési listájának beállítása
- ACL-ek rekurzív beállítása 

### <a name="set-the-acl-of-a-directory"></a>Címtár ACL-listájának beállítása

Ez a példa lekéri, majd beállítja a nevű könyvtár ACL-listáját `my-directory` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Lekérheti és beállíthatja egy tároló gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez adjon át egy üres karakterláncot ( `""` ) a **DataLakeFileSystemClient. getDirectoryClient** metódusba.

### <a name="set-the-acl-of-a-file"></a>Fájl hozzáférés-vezérlési listájának beállítása

Ez a példa lekéri, majd beállítja a nevű fájl ACL-listáját `upload-file.txt` . Ez a példa az olvasási, írási és végrehajtási engedélyeket adja meg, így a tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik, és minden más olvasási hozzáférést biztosít.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>ACL-ek rekurzív beállítása

Az ACL-eket rekurzív módon állíthatja be a **DataLakeDirectoryClient. setAccessControlRecursive** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál.

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, akkor meghívhatja a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusát, és **igaz** értéket adhat át. 

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának minden hívásában használatos. Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>ACL-ek frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk ACL-ek [beállítása](#set-acls) című szakaszát.

Ez a szakasz a következőket mutatja be:

- ACL frissítése
- Az ACL-ek rekurzív frissítése

### <a name="update-an-acl"></a>ACL frissítése

Először kérje le a címtár ACL-listáját a [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) metódus meghívásával. Másolja az ACL-bejegyzések listáját egy új, [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry)típusú **List** objektumra. Ezután keresse meg a frissíteni kívánt bejegyzést, és cserélje le a listára. Állítsa be az ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

Ez a példa egy nevű könyvtár ACL-listáját frissíti, `my-parent-directory` Ha lecseréli az összes többi felhasználó bejegyzését. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Lekérheti és beállíthatja egy tároló gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez adjon át egy üres karakterláncot ( `""` ) a **DataLakeFileSystemClient. getDirectoryClient** metódusba.

### <a name="update-acls-recursively"></a>Az ACL-ek rekurzív frissítése

Egy ACL rekurzív frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Az ACL-ek rekurzív frissítése a **DataLakeDirectoryClient. updateAccessControlRecursive** metódus meghívásával.  Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál. 

Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusát kell átadnia, és az értéke TRUE ( **igaz**) lehet. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának hívásakor használatos. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>ACL-Bejegyzések eltávolítása

Egy vagy több ACL-bejegyzést is eltávolíthat. Ez a szakasz a következőket mutatja be:

- ACL-bejegyzés eltávolítása
- ACL-bejegyzések rekurzív eltávolítása

### <a name="remove-an-acl-entry"></a>ACL-bejegyzés eltávolítása

Először kérje le a címtár ACL-listáját a [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) metódus meghívásával. Másolja az ACL-bejegyzések listáját egy új, [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry)típusú **List** objektumra. Ezután keresse meg a törölni kívánt bejegyzést, és hívja meg a **List** objektum **eltávolítási** metódusát. Állítsa be a frissített ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Az ACL-bejegyzések rekurzív eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása művelettel. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket.

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient. removeAccessControlRecursive** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál. 

Ha el kívánja távolítani egy **alapértelmezett** ACL-bejegyzést, akkor a PathAccessControlEntry **setDefaultScope** metódusát kell [](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) megadnia, és az értéke **true (igaz**) lehet.  

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy el kell-e távolítani a bejegyzést az alapértelmezett ACL-ből. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának hívásakor használatos.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A futásidejű vagy az engedélyekkel kapcsolatos hibák merülhetnek fel. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a `null` folytatási jogkivonat paraméter értékét átadja.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

Annak érdekében, hogy a folyamat zavartalanul befejeződjön, hívja meg a [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektum **setContinueOnFailure** metódusát, és adjon meg egy **igaz** értéket.

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Lásd még

- [API-referenciadokumentáció](/java/api/overview/azure/storage-file-datalake-readme)
- [Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)
- [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)