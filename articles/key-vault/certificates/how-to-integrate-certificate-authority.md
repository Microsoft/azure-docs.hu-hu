---
title: A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval
description: A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749311"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>A Key Vault integrálása a DigiCert hitelesítésszolgáltatóval

Azure Key Vault lehetővé teszi a hálózat digitális tanúsítványai egyszerű üzembe helyezését, kezelését és üzembe helyezését, valamint az alkalmazások biztonságos kommunikációját. A digitális tanúsítvány egy elektronikus hitelesítő adat, amely egy elektronikus tranzakció során megállapítja a személyazonosság igazolását. 

Az Azure Key Vault felhasználói a DigiCert-tanúsítványokat közvetlenül a saját Key Vault. Key Vault a DigiCert és a DigiCer Key Vault t hitelesítésszolgáltató közötti megbízható partnerkapcsolaton keresztül biztosítaná a DigiCert által kibocsátott tanúsítványok teljes tanúsítvány-életciklusának kezelését.

A tanúsítványokkal kapcsolatos általános információkért lásd: [tanúsítványok Azure Key Vault.](./about-certificates.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következő erőforrásokkal kell rendelkezik.
* Egy kulcstartó. Használhat meglévő kulcstartót, vagy létrehozhat egy újat az alábbi rövid útmutatókban található lépések valamelyikével:
   - [Kulcstartó létrehozása az Azure CLI használatával](../general/quick-create-cli.md)
   - [Kulcstartó létrehozása Azure PowerShell](../general/quick-create-powershell.md)
   - [Hozzon létre egy kulcstartót a Azure Portal.](../general/quick-create-portal.md)
*   Aktiválnia kell a DigiCert CertCentral-fiókot. [Regisztráljon](https://www.digicert.com/account/signup/) a CertCentral-fiókra.
*   Rendszergazdai szintű engedélyek a fiókokban.


### <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy a következő információk kéznél vannak a DigiCert CertCentral-fiókjában:
-   Tanúsítványcentrális fiók azonosítója
-   Szervezet azonosítója
-   API-kulcs

## <a name="adding-certificate-authority-in-key-vault"></a>Hitelesítésszolgáltató hozzáadása a Key Vault 
Miután begyűjti a fenti adatokat a DigiCert CertCentral-fiókból, hozzáadhatja a DigiCertet a hitelesítésszolgáltatói listához a kulcstartóban.

### <a name="azure-portal"></a>Azure Portal

1.  DigiCert-hitelesítésszolgáltató hozzáadásához keresse meg a DigiCertet hozzáadni kívánt kulcstartót. 
2.  A Tulajdonságok Key Vault oldalon válassza a Tanúsítványok **lehetőséget.**
3.  Válassza **a Hitelesítésszolgáltató fület.** ![ hitelesítésszolgáltató kiválasztása](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Válassza **a Beállítás hozzáadása** lehetőséget.
 ![hitelesítésszolgáltató hozzáadása](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  A **Hitelesítésszolgáltató létrehozása képernyőn** válassza a következő értékeket:
    -   **Név:** Adjon meg egy azonosítható kiállítónevet. Példa DigicertCA-példára
    -   **Szolgáltató:** A menüben válassza a DigiCert lehetőséget.
    -   **Fiókazonosító:** Adja meg DigiCert-tanúsítványcentrális fiókazonosítóját
    -   **Fiók jelszava:** Adja meg a DigiCert CertCentral-fiókban létrehozott API-kulcsot
    -   **Szervezeti azonosító:** Adja meg a DigiCert CertCentral-fiókból gyűjtött orgID azonosítót 
    -   Kattintson a **Létrehozás** lehetőségre.
   
6.  Látni fogja, hogy a DigicertCA hozzá lett adva a hitelesítésszolgáltatói listához.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell azure-erőforrások parancsokkal vagy szkriptekkel való létrehozására és kezelésére szolgál. Az Azure Azure Cloud Shell egy interaktív felületi környezetet, amely a böngészőben Azure Portal használható.

Ha a PowerShell helyi telepítését és használatát választja, az oktatóanyaghoz Azure PowerShell modul 1.0.0-s vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereshez írja be a következőt: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

1.  Erőforráscsoport **létrehozása**

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Új **Key Vault**

A kulcstartónak egyedi nevet kell használnia. Itt a "Contoso-Vaultname" az útmutatóban Key Vault neve.

- **Tároló neve** Contoso-Vaultname.
- **Erőforráscsoport neve**: ContosoResourceGroup.
- **Hely** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Változók definiálása a DigiCert CertCentral-fiókból gyűjtött információkhoz.

- Fiókazonosító **változó** meghatározása
- Szervezeti **azonosító változójának** meghatározása
- **API-kulcs változójának** definiálás

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Állítsa be **a Kiállítót.** Ezzel hozzáadja a Digicertet hitelesítésszolgáltatóként a kulcstartóhoz. A paraméterekkel kapcsolatos további információkért olvassa [el itt](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Házirend beállítása a tanúsítványhoz és** tanúsítvány kiállítása a DigiCertből közvetlenül a Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

A Digicert ca sikeresen kibocsátott egy tanúsítványt a megadott Key Vault az integráción keresztül.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a kiadott tanúsítvány "disabled" (letiltva) állapotú a  Azure Portal- (Tanúsítványművelet) nézetben tekintse át a tanúsítvány DigiCert-hibaüzenetét.

 ![Tanúsítványművelet](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

"Kérjük, hajtson végre egy egyesítést a tanúsítványkérelem befejezéséhez." hibaüzenet.
A kérés befejezéséhez egyesítenie kell a CA által aláírt CSR-t. További információt [itt](./create-certificate-signing-request.md) talál

További információért tekintse meg [a tanúsítványműveleteket a Key Vault REST API referenciában.](/rest/api/keyvault) További információ az engedélyek létrehozásáról: [Tárolók – Létrehozás](/rest/api/keyvault/vaults/createorupdate) vagy frissítés és tárolók [– Hozzáférési szabályzat frissítése.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- Létrehozhatok digicert helyettesítő tanúsítványt a KeyVault használatával? 
   Igen. Ez attól függ, hogyan konfigurálta a digicert fiókot.
- Hogyan hozhatok létre **OV-SSL- vagy EV-SSL-tanúsítványt** a DigiCert használatával? 
   A Key Vault támogatja az OV- és EV SSL-tanúsítványok létrehozását. Tanúsítvány létrehozásakor kattintson a Speciális házirend-konfiguráció elemre, majd adja meg a Tanúsítványtípust. Támogatott értékek: OV-SSL, EV-SSL
   
   Ezt a tanúsítványtípust létrehozhatja a Key Vaultban, ha a Digicert-fiókja engedélyezi. Ilyen típusú tanúsítvány esetén az ellenőrzést a DigiCert végzi el, és a támogatási csapat a legjobban segíthet a megoldásban, ha az ellenőrzés meghiúsul. A tanúsítvány létrehozásakor további információkat adhat hozzá, ha a subjectName (tulajdonosnév) mezőben definiálja őket.

Példa
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Késik a digicert tanúsítvány létrehozása az integráció és a tanúsítvány közvetlenül a digicert segítségével való lekérés során?
   Nem. Tanúsítvány létrehozásakor az ellenőrzési folyamatra van szükség, amely időt is vehet, és az ellenőrzés a DigiCert által nyomon követhető folyamattól függ.


## <a name="next-steps"></a>Következő lépések

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)