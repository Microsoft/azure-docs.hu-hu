---
title: A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval
description: Key Vault integrálása az DigiCert-hitelesítésszolgáltatóval
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: c36353448c140450044f352062c3349939e3f7b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789010"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval

A Azure Key Vault lehetővé teszi a hálózat digitális tanúsítványainak üzembe helyezését, kezelését és telepítését, valamint az alkalmazások biztonságos kommunikációjának engedélyezését. A digitális tanúsítvány egy elektronikus hitelesítő adat, amely igazolja az identitást egy elektronikus tranzakcióban. 

Az Azure Key Vault-felhasználók közvetlenül a Key Vault hozhatnak DigiCert-tanúsítványokat. Key Vault a DigiCert által kiállított tanúsítványok teljes körű felügyeletét a DigiCert-hitelesítésszolgáltatóval Key Vault megbízható partneri kapcsolattal biztosítja.

A tanúsítványokkal kapcsolatos további általános információkért lásd: [Azure Key Vault tanúsítványok](./about-certificates.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató végrehajtásához a következő erőforrásokkal kell rendelkeznie.
* Key Vault. Egy meglévő kulcstartót is használhat, vagy létrehozhat egy újat a következő rövid útmutatók lépéseinek végrehajtásával:
   - [Kulcstartó létrehozása az Azure CLI-vel](../general/quick-create-cli.md)
   - [Key Vault létrehozása Azure PowerShell](../general/quick-create-powershell.md)
   - [Hozzon létre egy Key vaultot a Azure Portal](../general/quick-create-portal.md).
*   Aktiválni kell a DigiCert CertCentral-fiókot. [Regisztráljon](https://www.digicert.com/account/signup/) a CertCentral-fiókjára.
*   Rendszergazdai szintű engedélyek a fiókokban.


### <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy a DigiCert CertCentral-fiókjából a következő információk hasznosak:
-   CertCentral-fiók azonosítója
-   Szervezet azonosítója
-   API-kulcs

## <a name="adding-certificate-authority-in-key-vault"></a>Hitelesítésszolgáltató hozzáadása a Key Vault 
A fenti információknak a DigiCert CertCentral-fiókból való összegyűjtése után mostantól hozzáadhat DigiCert a hitelesítésszolgáltatók listájához a kulcstartóban.

### <a name="azure-portal"></a>Azure Portal

1.  DigiCert-hitelesítésszolgáltató hozzáadásához navigáljon a DigiCert hozzáadni kívánt kulcstartóhoz. 
2.  A Key Vault tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
3.  Válassza **a hitelesítésszolgáltatók** fület. ![ hitelesítésszolgáltatók kiválasztása](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Válassza a **Hozzáadás** lehetőséget.
 ![hitelesítésszolgáltatók hozzáadása](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  A hitelesítésszolgáltató **létrehozása** képernyőn válassza a következő értékeket:
    -   **Név**: azonosítható kiállítói név hozzáadása. Példa DigicertCA
    -   **Szolgáltató**: válassza a DigiCert lehetőséget a menüből.
    -   **Fiókazonosító**: adja meg a DigiCert CertCentral-FIÓKjának azonosítóját
    -   **Fiók jelszava**: adja meg a DigiCert CertCentral-fiókjában létrehozott API-kulcsot
    -   **Szervezet azonosítója**: adja meg a DigiCert CertCentral-fiókból összegyűjtött OrgID 
    -   Kattintson a **Létrehozás** lehetőségre.
   
6.  Látni fogja, hogy a DigicertCA már hozzá lett adva a hitelesítésszolgáltatók listájában.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell az Azure-erőforrások parancsok vagy parancsfájlok használatával történő létrehozására és kezelésére szolgál. Az Azure-ban Azure Cloud Shell egy interaktív rendszerhéj-környezet, amelyet a saját Azure Portal a böngészőben is használhat.

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. `$PSVersionTable.PSVersion`A verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

1.  **Erőforráscsoport** létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. **Key Vault** létrehozása

A kulcstartó egyedi nevét kell használnia. Itt a "contoso-Vaultname" a Key Vault neve az útmutató során.

- Tár **neve** Contoso – Vaultname.
- **Erőforráscsoport neve**: ContosoResourceGroup.
- **Tartózkodási hely** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Változók definiálása a DigiCert CertCentral-fiókból összegyűjtött információkhoz.

- **Fiókazonosító** -változó definiálása
- **Szervezeti azonosító** változójának definiálása
- **API-kulcs** változójának definiálása

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Adja meg a **kiállítót**. Ezzel hozzáadja a Digicert-t a kulcstartóhoz. Ha többet szeretne megtudni a paraméterekről, [olvassa el itt](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Házirend beállítása a tanúsítványhoz és a tanúsítvány kiállítása** a DigiCert közvetlenül a Key Vaulton belül.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

A Digicert CA sikeresen kiállította a tanúsítványt a megadott Key Vaulton ezen integráción keresztül.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a kiállított tanúsítvány "letiltott" állapotban van a Azure Portalban, folytassa a **tanúsítvány-művelet** megtekintésével, hogy áttekintse az adott tanúsítványhoz tartozó DigiCert-hibaüzenetet.

 ![Tanúsítvány művelete](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Hibaüzenet: "Kérjük, végezze el az egyesítést a tanúsítványkérelem befejezéséhez."
A kérelem végrehajtásához egyesíteni kell a HITELESÍTÉSSZOLGÁLTATÓ által aláírt vállalati ügyfélszolgálatot. [További információ](./create-certificate-signing-request.md)

További információkért tekintse meg a [tanúsítványok műveleteit a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- Létrehozhatok Digicert helyettesítő tanúsítványt a kulcstartón keresztül? 
   Igen. Ez attól függ, hogyan konfigurálta a Digicert-fiókját.
- Hogyan hozhatok létre **OV-SSL-vagy EV-SSL-** tanúsítványt a DigiCert-mel? 
   A Key Vault támogatja az OV és az EV SSL-tanúsítványok létrehozását. Tanúsítvány létrehozásakor kattintson a speciális házirend-konfiguráció elemre, majd adja meg a tanúsítvány típusát. Támogatott értékek: OV-SSL, EV-SSL
   
   Ha a Digicert-fiókja lehetővé teszi, hogy ilyen típusú tanúsítványt hozzon létre a Key vaultban. Ilyen típusú tanúsítvány esetén az érvényesítést a DigiCert hajtja végre, és a támogatási csapatuk a lehető legjobban segítene Önnek a megoldásban, ha az érvényesítés sikertelen. A tanúsítványok létrehozásakor további információkat adhat meg a subjectName-ben.

Példa
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Van-e késés a Digicert-tanúsítványnak az integrációval való létrehozása és a tanúsítvány Digicert keresztüli közvetlen beszerzése révén?
   Nem. A tanúsítvány létrehozásakor a rendszer ellenőrzi, hogy az ellenőrzés folyamata hosszabb időt is igénybe vehet, és az ellenőrzés a folyamat DigiCert függ.


## <a name="next-steps"></a>Következő lépések

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)