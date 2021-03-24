---
title: Az Azure Spring Cloud hitelesítése Key Vaultekkel a GitHub-műveletekben
description: A Key Vault használata az Azure Spring Cloud-hoz készült CI/CD-munkafolyamattal a GitHub-műveletekkel
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878136"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Az Azure Spring Cloud hitelesítése Key Vaultekkel a GitHub-műveletekben

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A Key Vault biztonságos hely a kulcsok tárolásához. A vállalati felhasználóknak hitelesítő adatokat kell tárolniuk a CI/CD-környezetekhez az általuk vezérelt hatókörben. A Key vaultban a hitelesítő adatok lekéréséhez szükséges kulcsot az erőforrás hatókörére kell korlátozni.  Csak a kulcstartó hatóköréhez férhet hozzá, nem pedig a teljes Azure-hatókörhöz. Olyan kulcs, amely csak egy erős mezőt nyit meg, amely nem egy főkulcs, amely az épületben lévő összes ajtót meg tudja nyitni. Lehetőség van arra, hogy egy kulcsot egy másik kulccsal lásson el, ami hasznos lehet egy vel-munkafolyamatban. 

## <a name="generate-credential"></a>Hitelesítő adat előállítása
A Key Vault eléréséhez szükséges kulcs létrehozásához hajtsa végre az alábbi parancsot a helyi gépen:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
A paraméter által megadott hatókör `--scopes` korlátozza az erőforráshoz való hozzáférést.  Csak az erős dobozhoz fér hozzá.

Eredményekkel:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Ezután mentse a GitHub- **titkokat** a GitHub- [tárház beállítása és az Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)-ban való hitelesítés című cikkben leírtak szerint.

## <a name="add-access-policies-for-the-credential"></a>Hozzáférési szabályzatok hozzáadása a hitelesítő adatokhoz
A fent létrehozott hitelesítő adatok csak általános információkat kaphatnak a Key Vaultről, és nem az általa tárolt tartalmakról.  A Key Vaultban tárolt titkok beszerzéséhez hozzáférési szabályzatokat kell beállítania a hitelesítő adatokhoz.

Lépjen a Azure Portal **Key Vault** irányítópultra, kattintson a **hozzáférés-vezérlés** menüre, majd nyissa meg a **szerepkör-hozzárendelések** lapot. Válassza  ki a **Type** és `This resource` a **hatókörhöz** tartozó alkalmazásokat.  Ekkor meg kell jelennie az előző lépésben létrehozott hitelesítő adatoknak:

 ![Hozzáférési szabályzat beállítása](./media/github-actions/key-vault1.png)

Másolja a hitelesítő adat nevét, például: `azure-cli-2020-01-19-04-39-02` . Nyissa meg a **hozzáférési házirendek** menüt, kattintson a **+ hozzáférési házirend hozzáadása** hivatkozásra.  Válassza `Secret Management` a **sablon** lehetőséget, majd válassza a **rendszerbiztonsági tag** lehetőséget. Illessze be a hitelesítő adat nevét a **fő** / **Select** beviteli mezőbe:

 ![Válassza ezt:](./media/github-actions/key-vault2.png)

 Kattintson a **Hozzáadás** gombra a **hozzáférési házirend hozzáadása** párbeszédpanelen, majd kattintson a **Mentés** elemre.

## <a name="generate-full-scope-azure-credential"></a>Teljes hatókörű Azure-beli hitelesítő adatok előállítása
Ez az a főkulcs, amely megnyitja az összes ajtót az épületben. Az eljárás hasonló az előző lépéshez, de itt a főkulcs létrehozásához a hatókört módosítjuk:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Újra, eredmények:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Másolja a teljes JSON-karakterláncot.  Vissza **Key Vault** irányítópultra. Nyissa meg a **titkok** menüt, majd kattintson a **létrehozó/importálás** gombra. Adja meg a titok nevét, például: `AZURE-CREDENTIALS-FOR-SPRING` . Illessze be a JSON hitelesítőadat-karakterláncot az **érték** beviteli mezőbe. Észreveheti, hogy az érték beviteli mezője egy egysoros szövegmező, nem többsoros szövegmező.  Itt beillesztheti a teljes JSON-karakterláncot.

 ![Teljes hatókörű hitelesítő adat](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Hitelesítő adatok egyesítése a GitHub-műveletekben
Adja meg a vel-folyamat végrehajtásakor használt hitelesítő adatokat:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Következő lépések
* [Spring Cloud GitHub-műveletek](./spring-cloud-howto-github-actions.md)
