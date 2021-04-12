---
title: A Azure Key Vault Recovery áttekintése | Microsoft Docs
description: Key Vault a helyreállítási funkciók célja, hogy megakadályozza a Key vaultban tárolt kulcstartók, titkos kulcsok és tanúsítványok véletlen vagy rosszindulatú törlését.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a8e8e791f0dbe18322ad43364ae4ffd09b430caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790384"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault helyreállítási felügyelet a Soft delete és a Purge Protection szolgáltatással

Ez a cikk a Azure Key Vault két helyreállítási funkcióját ismerteti, a Soft delete és a Purge Protection szolgáltatást. Ez a dokumentum áttekintést nyújt ezekről a funkciókról, és bemutatja, hogyan felügyelheti őket a Azure Portal, az Azure CLI és a Azure PowerShell használatával.

További információ a Key Vaultról:
- [A Key Vault áttekintése](overview.md)
- [A kulcsok, a titkok és a tanúsítványok Azure Key Vault áttekintése](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [PowerShell-modul](/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* A Key Vault létrehozhat egyet [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)használatával, vagy [Azure PowerShell](../general/quick-create-powershell.md)
* A felhasználónak a következő engedélyekre van szüksége (előfizetési szinten), hogy végrehajtsa a műveleteket a Soft Deleted-tárolók esetén:

  | Engedély | Leírás |
  |---|---|
  |Microsoft. Key Vault/Locations/deletedVaults/READ|A törölt kulcstartó tulajdonságainak megtekintése|
  |Microsoft. kulcstartó/Locations/deletedVaults/Purge/művelet|Helyreállítható törölhető kulcstartó törlése|


## <a name="what-are-soft-delete-and-purge-protection"></a>Mi a Soft delete és a Purge Protection

A [Soft delete](soft-delete-overview.md) és a Purge Protection két különböző Key Vault helyreállítási funkció.

> [!IMPORTANT]
> A Soft delete bekapcsolása kritikus fontosságú annak biztosítása érdekében, hogy a kulcstartók és a hitelesítő adatok a véletlen törléssel védve legyenek. A Soft delete bekapcsolása azonban nem változik, mivel előfordulhat, hogy módosítania kell az alkalmazás logikáját, vagy további engedélyeket kell megadnia az egyszerű szolgáltatásokhoz. Mielőtt a Soft delete bekapcsolja az alábbi utasításokat, ellenőrizze, hogy az alkalmazás kompatibilis-e a dokumentum használatával [ .](soft-delete-change.md)

A **Soft delete** a Key vaultban tárolt kulcstartó és kulcsok, titkos kódok és tanúsítványok véletlen törlésének megelőzésére szolgál. Gondolja át, hogy a Soft-DELETE, például egy Lomtár. Ha töröl egy kulcstartót vagy egy Key Vault-objektumot, az a felhasználó által konfigurálható megőrzési időtartam vagy az alapértelmezett 90 nap után helyreállítható marad. A Soft Deleted állapotú kulcstartók **is törölhetők, ami azt** jelenti, hogy véglegesen törlődnek. Ez lehetővé teszi, hogy újra létrehozza a kulcstartókat és a Key Vault-objektumokat ugyanazzal a névvel. A kulcstartók és objektumok helyreállításához és törléséhez emelt szintű hozzáférési házirendre van szükség. **Ha a helyreállított törlés engedélyezve van, nem lehet letiltani.**

Fontos megjegyezni, hogy a **Key Vault-nevek globálisan egyediek**, ezért nem hozhat létre olyan kulcstartót, amelynek a neve nem azonos a helyreállítható törölt állapotú kulcstartóval. Hasonlóképpen a kulcsok, titkos kódok és tanúsítványok nevei is egyediek a kulcstartón belül. Nem hozhat létre olyan titkos kulcsot, kulcsot vagy tanúsítványt, amelynek a neve megegyezik egy másikkal a helyreállítható törölt állapotban.

A **védelem kiürítése** arra szolgál, hogy megakadályozza a kulcstároló, a kulcsok, a titkos kódok és a tanúsítványok rosszindulatú bennfentesek általi törlését. Gondoljon erre a Lomtárra egy időalapú zárolással. Az elemeket bármikor helyreállíthatja a konfigurálható megőrzési időtartam alatt. **A Key vaultot nem lehet véglegesen törölni vagy kiüríteni, amíg a megőrzési idő el nem telik.** Ha a megőrzési idő eltelt, a Key Vault vagy a Key Vault objektum automatikusan törlődik.

> [!NOTE]
> A védelem kiürítése úgy lett kialakítva, hogy egyetlen rendszergazdai szerepkör vagy engedély se legyen felülbírálva, letiltva vagy megkerüléssel. **Ha a kiürítési védelem engedélyezve van, nem tiltható le, és nem bírálható el senki, például a Microsoft.** Ez azt jelenti, hogy helyre kell állítani egy törölt kulcstartót, vagy várnia kell, amíg a megőrzési időtartam el nem telik a kulcstároló nevének újrahasználata előtt.

A helyreállítható törléssel kapcsolatos további információkért lásd: [Azure Key Vault-törlés – áttekintés](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Annak ellenőrzése, hogy engedélyezve van-e a Soft delete engedélyezése a kulcstartóban, és engedélyezze a helyreállítható törlést

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Kattintson a "tulajdonságok" panelre.
1. Ellenőrizze, hogy a Soft-delete melletti választógomb a "helyreállítás engedélyezése" értékre van-e állítva.
1. Ha a Soft-delete nincs engedélyezve a kulcstartóban, kattintson a választógombra a Soft delete engedélyezéséhez, majd kattintson a Save (Mentés) gombra.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="A tulajdonságok területen a Soft-delete kiemelve értékű, ahogy az az érték, amely lehetővé teszi a törlést.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Hozzáférés biztosítása egy egyszerű szolgáltatáshoz a törölt titkok kiürítéséhez és helyreállításához

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Kattintson a "hozzáférési szabályzat" panelre.
1. A táblázatban keresse meg a rendszerbiztonsági tag azon sorát, amelyhez hozzáférést szeretne biztosítani (vagy adjon hozzá egy új rendszerbiztonsági tag).
1. Kattintson a kulcsok, tanúsítványok és titkok legördülő listára.
1. Görgessen a legördülő menü aljára, és kattintson a "helyreállítás" és a "kiürítés" elemre.
1. A rendszerbiztonsági tag a legtöbb művelet végrehajtásához is szükség van a Get és a List funkciókra.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="A bal oldali navigációs panelen a hozzáférési házirendek ki vannak emelve. A hozzáférési házirendekben megjelenik a titkos pozíciók legördülő lista, és négy elem van kiválasztva: Get, List, Recover és purge.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Helyreállítható kulcstartók listázása, helyreállítása vagy törlése

1. Jelentkezzen be az Azure Portalra.
1. Kattintson a lap tetején található keresési sávra.
1. A legutóbbi szolgáltatások területen kattintson a "Key Vault" elemre. Ne kattintson egy egyedi kulcstartóra.
1. A képernyő felső részén kattintson a "törölt tárolók kezelése" lehetőségre.
1. A képernyő jobb oldalán megnyílik egy helyi ablaktábla.
1. Válassza ki előfizetését.
1. Ha a kulcstartót nem sikerült törölni, akkor a jobb oldalon megjelenik a helyi ablaktáblán.
1. Ha túl sok tár van, akkor a környezeti ablaktábla alján kattintson a "betöltés több" lehetőségre, vagy a CLI vagy a PowerShell használatával érheti el az eredményeket.
1. Ha megtalálta a helyreállítani vagy kiüríteni kívánt tárolót, jelölje be a mellette található jelölőnégyzetet.
1. Ha vissza szeretné állítani a kulcstárolót, válassza a helyi ablaktábla alján található helyreállítás lehetőséget.
1. Ha véglegesen törölni szeretné a kulcstárolót, válassza a kiürítés lehetőséget.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="A Key Vaults szolgáltatásban a törölt tárolók kezelése beállítás ki van emelve.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="A törölt kulcstartók kezelése oldalon az egyetlen megjelenített kulcstartó ki van jelölve és ki van választva, és a helyreállítás gomb ki van emelve.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>A törölt titkos kódok, kulcsok és tanúsítványok listázása, helyreállítása vagy törlése

1. Jelentkezzen be az Azure Portalra.
1. Válassza ki a kulcstartót.
1. Válassza ki a kezelni kívánt titkos típushoz tartozó panelt (kulcsok, titkok vagy tanúsítványok).
1. A képernyő felső részén kattintson a "Törölt elemek kezelése (kulcsok, titkos kódok vagy tanúsítványok) elemre.
1. A képernyő jobb oldalán megjelenik egy helyi ablaktábla.
1. Ha a titok, a kulcs vagy a tanúsítvány nem jelenik meg a listában, akkor nem a nem megfelelően törölt állapotban van.
1. Válassza ki a kezelni kívánt titkot, kulcsot vagy tanúsítványt.
1. Válassza a helyreállítás vagy a Törlés lehetőséget a helyi ablaktábla alján.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="A kulcsok területen a törölt kulcsok kezelése beállítás ki van emelve.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Annak ellenőrzése, hogy a Key-Vault rendelkezik-e a helyreállítható törléssel

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Helyreállítható Törlés engedélyezése a Key-vaultban

    Alapértelmezés szerint minden új kulcstárolóban engedélyezve van a törlés. Ha jelenleg van olyan kulcstartó, amelynél nincs engedélyezve a helyreállítható törlés, a következő paranccsal engedélyezheti a helyreállítható törlést.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Key Vault törlése (helyreállítható, ha a Soft delete engedélyezve van)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Az összes Soft Deleted Key Vault listázása

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Helyreállítható kulcs visszaállítása – tár

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* A helyreállított kulcstartó törlése **(Figyelmeztetés! Ez a művelet véglegesen törli a KULCSTARTÓt)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Törlés engedélyezése – a Key-Vault védelme

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Tanúsítványok (CLI)

* Hozzáférés biztosítása a tanúsítványok törléséhez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Tanúsítvány törlése

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Törölt tanúsítványok listázása

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt tanúsítvány helyreállítása

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Helyreállítható törölt tanúsítvány törlése **(Figyelmeztetés! a művelet véglegesen törli a tanúsítványt)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Kulcsok (CLI)

* Hozzáférés biztosítása a kulcsok törléséhez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Törlés billentyű

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Törölt kulcsok listázása

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt kulcs helyreállítása

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Helyreállítható kulcs törlése **(Figyelmeztetés! a művelet véglegesen törli a kulcsot)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Titkok (CLI)

* Hozzáférés biztosítása a titkok törléséhez és helyreállításához

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Titkos kód törlése

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Törölt titkok listázása

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Törölt titkos kód helyreállítása

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* A törölt titkos kód kiürítése **(Figyelmeztetés! a művelet véglegesen törli a TITKot)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Annak ellenőrzése, hogy a Key-Vault rendelkezik-e a helyreállítható törléssel

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Key Vault törlése

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Az összes Soft Deleted Key Vault listázása

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Helyreállítható kulcs visszaállítása – tár

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Helyreállítható kulcs törlése – tár **(figyelmeztetés) Ez a művelet véglegesen törli a KULCSTARTÓt)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Törlés engedélyezése – a Key-Vault védelme

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Tanúsítványok (PowerShell)

* Engedélyek megadása a tanúsítványok helyreállításához és törléséhez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Tanúsítvány törlése

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* A Key Vault összes törölt tanúsítványának listázása

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Tanúsítvány helyreállítása törölt állapotban

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Törölt tanúsítvány törlése **(Figyelmeztetés! a művelet véglegesen törli a tanúsítványt)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Kulcsok (PowerShell)

* Engedélyek megadása a kulcsok helyreállításához és kiürítéséhez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Kulcs törlése

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* A Key Vault összes törölt tanúsítványának listázása

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Helyreállítható kulcs helyreállítása

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Helyreállítható kulcs törlése **(Figyelmeztetés! a művelet véglegesen törli a kulcsot)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Titkok (PowerShell)

* Engedélyek megadása a titkok helyreállításához és kiürítéséhez

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword nevű titkos kód törlése

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* A Key Vault összes törölt titkának listázása

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Titkos kód helyreállítása törölt állapotban

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Titkos kód törlése törölt állapotban **(Figyelmeztetés! a művelet véglegesen törli a kulcsot)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Következő lépések

- [PowerShell-parancsmagok Azure Key Vault](/powershell/module/az.keyvault)
- [Azure CLI-parancsok Key Vault](/cli/azure/keyvault)
- [Azure Key Vault biztonsági mentés](backup.md)
- [Key Vault naplózás engedélyezése](howto-logging.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](secure-your-key-vault.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- [Ajánlott eljárások a Key Vault használatához](security-overview.md)