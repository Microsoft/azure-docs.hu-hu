---
title: Tanúsítványok exportálása Azure Key Vault
description: Megtudhatja, hogyan exportálhatja a tanúsítványokat a Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 0efe0164d8e1a4e5bc3b9d6d7313855740afd316
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767906"
---
# <a name="export-certificates-from-azure-key-vault"></a>Tanúsítványok exportálása Azure Key Vault

Megtudhatja, hogyan exportálhatja a tanúsítványokat a Azure Key Vault. A tanúsítványokat exportálhatja az Azure CLI, a Azure PowerShell vagy a Azure Portal. 

## <a name="about-azure-key-vault-certificates"></a>Információk az Azure Key Vault-tanúsítványokról

Azure Key Vault lehetővé teszi a digitális tanúsítványok egyszerű üzembe helyezését, kezelését és üzembe helyezését a hálózaton. Emellett biztonságos kommunikációt tesz lehetővé az alkalmazások számára. További [Azure Key Vault a tanúsítványokról.](./about-certificates.md)

### <a name="composition-of-a-certificate"></a>A tanúsítványok összetétele

A Key Vault létrehozásakor létrejön egy azonos nevű  címezhető kulcs és titkos kulcs.  A Key Vault kulcs lehetővé teszi a kulcsműveleteket. A Key Vault titkos ként engedélyezi a tanúsítvány értékének titkos ként való lekérését. A Key Vault tanúsítvány nyilvános x509-tanúsítvány metaadatokat is tartalmaz. További [információ: A](./about-certificates.md#composition-of-a-certificate) tanúsítvány összetétele.

### <a name="exportable-and-non-exportable-keys"></a>Exportálható és nem exportálható kulcsok

A Key Vault létrehozása után a titkos kulccsal lekérheti azt a címezhető titkos kulcsból. A tanúsítvány lekérése PFX vagy PEM formátumban.

- **Exportálható:** A tanúsítvány létrehozásához használt házirend azt jelzi, hogy a kulcs exportálható.
- **Nem exportálható:** A tanúsítvány létrehozásához használt házirend azt jelzi, hogy a kulcs nem exportálható. Ebben az esetben a titkos kulcs nem része az értéknek, amikor titkos kulcsként lekéri.

Támogatott kulcstípusok: RSA, RSA-HSM, EC, EC-HSM, október (itt listázva) [](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)Az exportálható csak RSA, EC esetén engedélyezett. A HSM-kulcsok nem exportálhatók.

További [információ: Azure Key Vault tanúsítványok](./about-certificates.md#exportable-or-non-exportable-key) használata.

## <a name="export-stored-certificates"></a>Tárolt tanúsítványok exportálása

Az Azure CLI, az Azure PowerShell vagy az Azure Portal használatával exportálhat tárolt tanúsítványokat az Azure Key Vaultba.

> [!NOTE]
> Csak tanúsítványjelszót igényel, amikor importálja a tanúsítványt a kulcstartóba. A Key Vault nem menti a társított jelszót. Amikor exportálja a tanúsítványt, a jelszó üres.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-ban a következő  paranccsal töltheti le egy nyilvános Key Vault tanúsítványból.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

További [információért tekintse](/cli/azure/keyvault/certificate#az_keyvault_certificate_download) meg a példákat és a paraméterdefiníciókat.

A tanúsítványként való letöltés a nyilvános rész lekért részét jelenti. Ha a titkos kulcsot és a nyilvános metaadatokat is szeretné, akkor titkos ként letöltheti.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

További információ: [paraméterdefiníciók.](/cli/azure/keyvault/secret#az_keyvault_secret_download)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ezzel a paranccsal a Azure PowerShell a **TestCert01** nevű tanúsítványt a **ContosoKV01 nevű kulcstartóból.** A tanúsítvány PFX-fájlként való letöltéséhez futtassa a következő parancsot. Ezek a parancsok hozzáférnek a **SecretId-hez,** majd PFX-fájlként mentik a tartalmat.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Ez a parancs exportálja a tanúsítványok teljes láncát titkos kulccsal (azaz ugyanúgy, mint az importáltat). A tanúsítvány jelszavas védelem alatt áll.
A **Get-AzKeyVaultCertificate** paranccsal és paraméterekkel kapcsolatos további információkért lásd: [Get-AzKeyVaultCertificate – 2. példa.](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate)

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal tanúsítvány létrehozása/importálása után a Tanúsítvány  panelen értesítést kap arról, hogy a tanúsítvány sikeresen létrejött. Válassza ki a tanúsítványt és az aktuális verziót a letöltési lehetőség kiválasztásához.

A tanúsítvány letöltéséhez válassza a **Letöltés CER formátumban** vagy **a Letöltés PFX/PEM formátumban lehetőséget.**

![Tanúsítvány letöltése](../media/certificates/quick-create-portal/current-version-shown.png)

**Tanúsítvány Azure App Service exportálása**

Azure App Service tanúsítványokkal kényelmesen vásárolhat SSL-tanúsítványokat. Ezeket a portálon keresztül rendelheti hozzá az Azure Appshez. Importálásuk után az App Service tanúsítványok a titkos kulcsok **alatt találhatók.**

További információkért lásd a tanúsítványok [exportálásának Azure App Service lépéseit.](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

---

## <a name="read-more"></a>További információk
* [Különböző tanúsítványfájltípusok és -definíciók](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
