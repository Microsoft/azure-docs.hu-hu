---
title: Tanúsítványok exportálása Azure Key Vaultból
description: Ismerje meg, hogyan exportálhat tanúsítványokat a Azure Key Vaultból.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: e10812290fa06e94419a1b4f27845f9b04ebb049
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508843"
---
# <a name="export-certificates-from-azure-key-vault"></a>Tanúsítványok exportálása Azure Key Vaultból

Ismerje meg, hogyan exportálhat tanúsítványokat a Azure Key Vaultból. A tanúsítványokat az Azure CLI, Azure PowerShell vagy a Azure Portal használatával exportálhatja. 

## <a name="about-azure-key-vault-certificates"></a>Információk az Azure Key Vault-tanúsítványokról

A Azure Key Vault lehetővé teszi digitális tanúsítványok egyszerű kiépítését, kezelését és üzembe helyezését a hálózaton. Emellett biztonságos kommunikációt tesz lehetővé az alkalmazások számára. További információért tekintse meg [Azure Key Vault tanúsítványokat](./about-certificates.md) .

### <a name="composition-of-a-certificate"></a>A tanúsítványok összetétele

Key Vault-tanúsítvány létrehozásakor egy megcímezhető kulcs és *titkos* *kód* jön létre, amelynek neve azonos. A Key Vault kulcs lehetővé teszi a legfontosabb műveleteket. A Key Vault titkos kód lehetővé teszi a tanúsítvány értékének beolvasását titkos kulcsként. A Key Vault tanúsítvány A nyilvános x509 tanúsítvány-metaadatokat is tartalmaz. További információért látogasson el a [tanúsítvány összeállítására](./about-certificates.md#composition-of-a-certificate) .

### <a name="exportable-and-non-exportable-keys"></a>Exportálható és nem exportálható kulcsok

Key Vault-tanúsítvány létrehozása után lekérheti a titkos kulccsal rendelkező címezhető titokból. A tanúsítvány lekérése PFX-vagy PEM-formátumban.

- **Exportálható**: a tanúsítvány létrehozásához használt szabályzat azt jelzi, hogy a kulcs exportálható.
- **Nem exportálható**: a tanúsítvány létrehozásához használt szabályzat azt jelzi, hogy a kulcs nem exportálható. Ebben az esetben a titkos kulcs nem része az értéknek, amikor a rendszer titokként kéri le.

Támogatott főtípusok: az RSA, az RSA-HSM, az EC, az EC-HSM, a TOT ( [itt](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)felsorolva) csak az RSA, az EC esetében engedélyezett. A HSM-kulcsok nem exportálhatók.

További információ: [tudnivalók Azure Key Vault tanúsítványokról](./about-certificates.md#exportable-or-non-exportable-key) .

## <a name="export-stored-certificates"></a>Tárolt tanúsítványok exportálása

Az Azure CLI, az Azure PowerShell vagy az Azure Portal használatával exportálhat tárolt tanúsítványokat az Azure Key Vaultba.

> [!NOTE]
> A tanúsítványnak a kulcstartóba való importálásakor csak tanúsítvány jelszava szükséges. A Key Vault nem menti a társított jelszót. A tanúsítvány exportálásakor a jelszó üres.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI következő parancsával letöltheti Key Vault tanúsítvány **nyilvános részét** .

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

További információkért tekintse meg a [példák és a paraméterek definícióit](/cli/azure/keyvault/certificate#az-keyvault-certificate-download) .

A tanúsítvány letöltése a nyilvános rész beszerzését jelenti. Ha a titkos kulcsot és a nyilvános metaadatokat is szeretné használni, töltse le titkos fájlként.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

További információ: [Paraméterek definíciói](/cli/azure/keyvault/secret#az-keyvault-secret-download).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja ezt a parancsot a Azure PowerShell a **TestCert01** nevű tanúsítvány lekéréséhez a **ContosoKV01** nevű kulcstartóból. A tanúsítvány PFX-fájlként való letöltéséhez futtassa a következő parancsot. Ezek a parancsok hozzáférnek a **SecretId**, majd a tartalmat pfx-fájlként mentik.

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

Ez a parancs a tanúsítványok teljes láncát exportálja a titkos kulccsal. A tanúsítvány jelszavas védelemmel van ellátva.
További információ a **Get-AzKeyVaultCertificate** parancsról és paraméterekről: [Get-AzKeyVaultCertificate – example 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate).

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha **a tanúsítvány** paneljén létrehoz/importál egy tanúsítványt a Azure Portalon, értesítést kap arról, hogy a tanúsítvány sikeresen létrejött. Válassza ki a tanúsítványt és az aktuális verziót, hogy megjelenjen a letöltési lehetőség.

A tanúsítvány letöltéséhez válassza a **Letöltés CER formátumban** vagy **a letöltés pfx/PEM formátumban** lehetőséget.

![Tanúsítvány letöltése](../media/certificates/quick-create-portal/current-version-shown.png)

**Azure App Service tanúsítványok exportálása**

Azure App Service a tanúsítványok kényelmes megoldást jelentenek az SSL-tanúsítványok megvásárlására. Az Azure-alkalmazásokhoz hozzárendelheti őket a portálon belülről. Az importálás után a App Service tanúsítványok a **titkok** területen találhatók.

További információ: [Azure app Service tanúsítványok exportálásának](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)lépései.

---

## <a name="read-more"></a>További információk
* [Különböző tanúsítványfájl-típusok és-definíciók](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
