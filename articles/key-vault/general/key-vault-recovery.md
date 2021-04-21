---
title: Azure Key Vault helyreállítás áttekintésének | Microsoft Docs
description: Key Vault helyreállítási szolgáltatások célja, hogy megakadályozzák a kulcstartó és a kulcstartóban tárolt titkos kulcsok, kulcsok és tanúsítványok véletlen vagy rosszindulatú törlését.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c3ffbba9546ada54a42c3f2c2aa5d98da599b353
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749730"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault helyreállítása helyreállított törlés és végleges törlés elleni védelemmel

Ez a cikk a helyreállítás két funkcióját Azure Key Vault, a helyreállító törlést és a végleges törlés elleni védelmet. Ez a dokumentum áttekintést nyújt ezekről a funkciókról, és bemutatja, hogyan kezelheti őket a Azure Portal, az Azure CLI és a Azure PowerShell.

További információ a Key Vault:
- [A Key Vault áttekintése](overview.md)
- [Azure Key Vault kulcsok, titkos kulcsok és tanúsítványok áttekintése](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/dotnet)
* [PowerShell-modul.](/powershell/azure/install-az-ps)
* [Azure CLI](/cli/azure/install-azure-cli)
* Egy Key Vault – létrehozhat egyet az [Azure CLI Azure Portal](../general/quick-create-cli.md) [](../general/quick-create-powershell.md) vagy a Azure PowerShell [](../general/quick-create-portal.md)
* A felhasználónak a következő engedélyekre lesz szüksége (előfizetési szinten) a műveletek végrehajtásához a soft-deleted tárolókon:

  | Engedély | Description |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|A törölt kulcstartó tulajdonságainak megtekintése|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Törölt kulcstartó végleges törlése|


## <a name="what-are-soft-delete-and-purge-protection"></a>Mi az a soft-delete és a végleges törlés elleni védelem?

[A helyreállító törlés](soft-delete-overview.md) és a végleges törlés elleni védelem két különböző kulcstartó-helyreállítási funkció.

> [!IMPORTANT]
> A soft delete bekapcsolása kritikus fontosságú annak biztosításához, hogy kulcstartói és hitelesítő adatai védve vannak a véletlen törléssel ellen. A soft delete bekapcsolása azonban nem megfelelő változásnak számít, mert előfordulhat, hogy módosítania kell az alkalmazáslogikát, vagy további engedélyeket kell adnia a szolgáltatásnévnek. Mielőtt bekapcsolja a soft delete parancsot az alábbi utasítások alapján, ellenőrizze, hogy az alkalmazás kompatibilis-e a módosítással az itt található dokumentum [ **használatával.**](soft-delete-change.md)

**A soft delete** célja, hogy megakadályozza a kulcstartó, valamint a kulcstartóban tárolt kulcsok, titkos kulcsok és tanúsítványok véletlen törlését. Gondoljon úgy a soft-delete parancsra, mint egy lomtárra. Kulcstartó vagy Key Vault-objektum törlésekor az a felhasználó által konfigurálható megőrzési időszak vagy alapértelmezés szerint 90 napig helyreállítható marad. A kulcstartók a törölt állapotban  is törölhetők, ami azt jelenti, hogy véglegesen törölve lesznek. Így újból létrehozhat kulcstartókat és kulcstartó-objektumokat ugyanazokkal a névvel. A kulcstartók és objektumok helyreállításához és törléséhez emelt szintű hozzáférési házirend-engedélyekre van szükség. **A soft delete engedélyezése után az nem tiltható le.**

Fontos megjegyezni, hogy a **kulcstartók** nevei globálisan egyediek, így nem fog tudni kulcstartót létrehozni ugyanazokkal a névvel, mint a kulcstartó a törölt állapotban. Hasonlóképpen a kulcsok, titkos kulcsok és tanúsítványok nevei egyediek a kulcstartón belül. Nem hozhat létre olyan titkos adatokat, kulcsokat vagy tanúsítványokat, amelyek neve megegyezik egy másikkal a törölt állapotban.

**A végleges törlés elleni** védelem célja, hogy megakadályozza a kulcstartó, a kulcsok, a titkos kódok és a tanúsítványok rosszindulatú belső felhasználó által való törlését. Gondoljon erre úgy, mint egy időalapú zárolású lomtárra. Az elemeket a konfigurálható megőrzési időszak során bármikor helyreállíthatja. **A kulcstartót a megőrzési időszak végéig nem törölheti vagy törölheti véglegesen.** A megőrzési időtartam eltelte után a rendszer automatikusan kiüríti a Key Vault- vagy Key Vault-objektumot.

> [!NOTE]
> A Véglegesen kiürítés elleni védelem úgy lett kialakítva, hogy egyetlen rendszergazdai szerepkör vagy engedély se bírálja felül, tiltsa le vagy kerülje meg a véglegesen védett védelmet. **Ha a véglegesen kiürítés elleni védelem engedélyezve van, azt senki sem tilthatja le vagy bírálhatja felül, beleértve a Microsoftot is.** Ez azt jelenti, hogy a kulcstartó nevének újrahasználása előtt helyre kell állítania egy törölt kulcstartót, vagy meg kell várnia, amíg a megőrzési idő le fog állni.

A soft-delete parancsról a Azure Key Vault [áttekintésében olvashat.](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Ellenőrizze, hogy a kulcstartón engedélyezve van-e a soft delete, és engedélyezze a soft delete parancsot

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Kattintson a "Tulajdonságok" panelre.
1. Ellenőrizze, hogy a helyreállítható törlés melletti választógomb a "Helyreállítás engedélyezése" beállításra van-e beállítva.
1. Ha a kulcstartón nincs engedélyezve a soft-delete, kattintson a választógombra a soft delete engedélyezéséhez, majd kattintson a "Mentés" gombra.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="A Tulajdonságokon a Soft-delete (Soft-delete) ki van emelve, ahogyan az engedélyezés értéke is.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Hozzáférés megadása egy szolgáltatásnévhez a törölt titkos kulcsok végleges törlése és helyreállítása érdekében

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Kattintson a "Hozzáférési szabályzat" panelre.
1. A táblázatban keresse meg annak a rendszerbiztonsági tagnak a sorát, amely számára hozzáférést kíván adni (vagy adjon hozzá egy új rendszerbiztonsági sort).
1. Kattintson a kulcsok, tanúsítványok és titkos kulcsok legördülő gombra.
1. Görgessen a legördülő lista aljára, és kattintson a "Helyreállítás" és a "Végleges törlés" lehetőségre.
1. A rendszerbiztonsági tagnak a legtöbb művelet végrehajtásához get és list funkcióra is szüksége lesz.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="A bal oldali navigációs panelen a Hozzáférési szabályzatok ki van emelve. A Hozzáférési szabályzatok alatt megjelenik a Titkos pozíciók legördülő lista, és négy elem van kiválasztva: Get, List, Recover és Purge.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Helyreállíthatóan törölt kulcstartók listából, helyreállításából vagy végleges törléséből

1. Jelentkezzen be az Azure Portalra.
1. Kattintson a lap tetején található keresősávra.
1. A "Legutóbbi szolgáltatások" alatt kattintson a "Key Vault" elemre. Ne kattintson az egyes kulcstartókra.
1. A képernyő tetején kattintson a "Törölt tárolók kezelése" lehetőségre
1. Megnyílik egy helyi panel a képernyő jobb oldalán.
1. Válassza ki előfizetését.
1. Ha a kulcstartót vissza lehet törölni, az a jobb oldali helyi panelen jelenik meg.
1. Ha túl sok tároló van, kattintson a helyi panel alján található "További betöltése" elemre, vagy a CLI vagy a PowerShell használatával lekérte az eredményeket.
1. Ha megtalálja a helyreállítani vagy véglegesen véglegesen megtisztítani kívánt tárolót, jelölje be a mellette található jelölőnégyzetet.
1. Válassza a helyi panel alján található helyreállítás lehetőséget, ha helyre szeretné állítani a kulcstartót.
1. Válassza a végleges törlés lehetőséget, ha véglegesen törölni szeretné a kulcstartót.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="A Kulcstartóknál ki van emelve a Törölt tárolók kezelése lehetőség.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="A Törölt kulcstartók kezelése lehetőségnél az egyetlen felsorolt kulcstartó ki van emelve és ki van jelölve, a Helyreállítás gomb pedig ki van emelve.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Helyreállíthatóan törölt titkos kulcsok, kulcsok és tanúsítványok listzása, helyreállítása vagy végleges törlése

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Válassza ki a kezelni kívánt titkos kulcs típusának megfelelő panelt (kulcsok, titkos kulcsok vagy tanúsítványok).
1. A képernyő tetején kattintson a "Törölt (kulcsok, titkos kulcsok vagy tanúsítványok) kezelése elemre.
1. A képernyő jobb oldalán megjelenik egy helyi panel.
1. Ha a titkos kulcs, a kulcs vagy a tanúsítvány nem jelenik meg a listában, akkor az nem lesz a listában a nem törölt állapotban.
1. Válassza ki a kezelni kívánt titkos kulcsot, kulcsot vagy tanúsítványt.
1. Válassza ki a helyi panel alján található helyreállítás vagy végleges ürülés lehetőséget.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="A Kulcsok beállításban ki van emelve a Törölt kulcsok kezelése lehetőség.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Annak ellenőrzése, hogy a kulcstartón engedélyezve van-e a soft-delete

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* A kulcstartón a soft-delete engedélyezése

    Alapértelmezés szerint minden új kulcstartóban engedélyezve van a soft delete funkció. Ha jelenleg olyan kulcstartóval rendelkezik, amely nem rendelkezik engedélyezett törlési funkcióval, a következő paranccsal engedélyezheti a soft delete parancsot.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Kulcstartó törlése (helyreállítható, ha a helyreállítható törlés engedélyezve van)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Az összes soft-deleted key vault list (Az összes le lehet törölni a kulcstartót)

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Helyreállíthatóan törölt kulcstartó helyreállítása

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Véglegesen törölt kulcstartó végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A KULCSTARTÓT)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* A kulcstartó végleges kiürítés elleni védelmének engedélyezése

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Tanúsítványok (CLI)

* Hozzáférés megadása a tanúsítványok végleges kiürítéshez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Tanúsítvány törlése

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Törölt tanúsítványok listája

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt tanúsítvány helyreállítása

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Véglegesen törölt tanúsítvány végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A TANÚSÍTVÁNYT)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Kulcsok (CLI)

* Hozzáférés megadása a kulcsok végleges kiürítéshez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Törlés billentyű

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Törölt kulcsok listából

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt kulcs helyreállítása

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Véglegesen törölt kulcs végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A KULCSOT)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Titkos kulcsok (CLI)

* Hozzáférés megadása titkos kulcsok végleges kiürítéshez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Titkos adatokat törölhet

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Törölt titkos kulcsok listába való belistzása

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt titkos adatok helyreállítása

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* A soft-deleted titkos adatokat véglegesen **töröljük (FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A TITKOS ADATOKAT)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Annak ellenőrzése, hogy a kulcstartón engedélyezve van-e a soft-delete

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Kulcstartó törlése

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Az összes soft-deleted key vault list (Az összes soft-deleted key vault list

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Helyreállíthatóan törölt kulcstartó helyreállítása

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* A soft-deleted key-vault végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A KULCSTARTÓT)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* A kulcstartó végleges kiürítés elleni védelmének engedélyezése

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Tanúsítványok (PowerShell)

* Engedélyek megadása a tanúsítványok helyreállításához és végleges kiürítéshez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Tanúsítvány törlése

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Egy kulcstartó összes törölt tanúsítványának felsorolása

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Tanúsítvány helyreállítása törölt állapotban

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Véglegesen törölt tanúsítvány végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A TANÚSÍTVÁNYT)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Kulcsok (PowerShell)

* Engedélyek megadása a kulcsok helyreállításához és végleges kiürítéshez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Kulcs törlése

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Egy kulcstartó összes törölt tanúsítványának felsorolása

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Helyreállíthatóan törölt kulcs helyreállítása

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Véglegesen törölt kulcs végleges törlése **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A KULCSOT)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Titkos kulcsok (PowerShell)

* Engedélyek megadása a titkos kulcsok helyreállításához és végleges kiürítéshez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword nevű titkos jelszó törlése

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Az összes törölt titkos kulcs felsorolása egy kulcstartóban

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Törölt állapotban maradó titkos adatok helyreállítása

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Törölt állapotban véglegesen töröl egy titkos adatokat **(FIGYELMEZTETÉS! EZ A MŰVELET VÉGLEGESEN TÖRLI A KULCSOT)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault PowerShell-parancsmagok használata](/powershell/module/az.keyvault)
- [Key Vault Azure CLI-parancsok használata](/cli/azure/keyvault)
- [Azure Key Vault biztonsági mentés](backup.md)
- [A naplózás Key Vault engedélyezése](howto-logging.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](security-overview.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- [Ajánlott eljárások a kulcstartók használatára](security-overview.md)