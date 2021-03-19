---
title: Titkosítási tanúsítvány beállítása Windows-fürtökön
description: Megtudhatja, hogyan állíthat be titkosítási tanúsítványt és titkosíthatja a titkokat a Windows-fürtökön.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82583288"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Titkosítási tanúsítvány beállítása és a titkok titkosítása Windows-fürtökön
Ez a cikk bemutatja, hogyan állíthat be titkosítási tanúsítványt, és hogyan titkosíthatja a titkokat a Windows-fürtökön. Linux-fürtök esetében lásd: [titkosítási tanúsítvány beállítása és a titkok titkosítása Linux-fürtökön.][secret-management-linux-specific-link]

A [Azure Key Vault][key-vault-get-started] a tanúsítványok biztonságos tárolási helyeként, valamint az Azure-beli Service Fabric-fürtökön telepített tanúsítványok beszerzésének módjaként használatos. Ha nem telepíti az Azure-t, nincs szükség a Key Vault használatára a titkok kezeléséhez Service Fabric alkalmazásokban. Azonban az alkalmazásokban a titkos kódok *használatával* a felhőalapú platform-agnosztikus lehetővé teszi, hogy az alkalmazások a bárhol üzemeltetett fürtön legyenek telepítve. 

## <a name="obtain-a-data-encipherment-certificate"></a>Titkosítási-tanúsítvány beszerzése
A titkosítási-tanúsítványokat szigorúan a szolgáltatás Settings.xml és [környezeti változói][environment-variables-link] által használt [Paraméterek][parameters-link] titkosítására és visszafejtésére használják a szolgáltatás ServiceManifest.xml. Nem használatos a titkosított szöveg hitelesítéséhez vagy aláírásához. A tanúsítványnak meg kell felelnie a következő követelményeknek:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A tanúsítványt létre kell hozni a személyes információcsere (. pfx) fájlba exportálható kulcscsere-fájlhoz.
* A tanúsítvány használatának tartalmaznia kell az adatok titkosítási (10), és nem tartalmazhat kiszolgálói hitelesítést vagy ügyfél-hitelesítést. 
  
  Ha például egy önaláírt tanúsítványt hoz létre a PowerShell használatával, a jelzőt a következőre `KeyUsage` kell beállítani `DataEncipherment` :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>A tanúsítvány telepítése a fürtben
Ezt a tanúsítványt a fürt minden csomópontján telepíteni kell. Lásd: [fürt létrehozása Azure Resource Manager használatával][service-fabric-cluster-creation-via-arm] a telepítési utasításokhoz. 

## <a name="encrypt-application-secrets"></a>Alkalmazás-titkok titkosítása
A titkos kulcs titkosításához a következő PowerShell-parancs használható. Ez a parancs csak az értéket titkosítja; **nem** írja alá a titkosítási szöveget. A titkos értékek rejtjelezett létrehozásához ugyanazt a titkosítási-tanúsítványt kell használnia, amely a fürtön telepítve van:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Az eredményül kapott Base-64 kódolású karakterlánc a titkos rejtjelezett is tartalmazza, valamint a titkosításhoz használt tanúsítvánnyal kapcsolatos információkat.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [határozhat meg titkosított titkot egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
