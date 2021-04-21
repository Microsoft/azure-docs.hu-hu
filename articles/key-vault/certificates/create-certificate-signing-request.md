---
title: CSR létrehozása és egyesítése a Azure Key Vault
description: Útmutató CSR létrehozásához és egyesítéséhez a Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752137"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>CSR létrehozása és egyesítése a Key Vault

Azure Key Vault bármely hitelesítésszolgáltató (CA) által kiadott digitális tanúsítványok tárolását támogatja. Támogatja a tanúsítvány-aláírási kérelem (CSR) létrehozását privát/nyilvános kulcspárokkal. A CSR-t bármilyen hitelesítésszolgáltató (belső vállalati hitelesítésszolgáltató vagy külső nyilvános hitelesítésszolgáltató) aláírhatja. A CSR egy olyan üzenet, amely egy ca-nak küldve kér digitális tanúsítványt.

A tanúsítványokkal kapcsolatos további általános információkért lásd: Azure Key Vault [tanúsítványok.](./about-certificates.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Tanúsítványok hozzáadása a Key Vault hitelesítéspartner által kiadott tanúsítványokhoz

Key Vault az alábbi hitelesítésszolgáltatóval, hogy leegyszerűsítse a tanúsítványok létrehozását.

|Szolgáltató|Tanúsítvány típusa|Konfiguráció beállítása  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault OV- vagy EV SSL-tanúsítványokat kínál a DigiCert használatával| [Integrációs útmutató](./how-to-integrate-certificate-authority.md)
|Globalsign|Key Vault OV- vagy EV SSL-tanúsítványokat kínál a GlobalSign használatával| [Integrációs útmutató](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Nem partneri hitelesítés Key Vault által kiadott tanúsítványok hozzáadása a tanúsítványban

Kövesse az alábbi lépéseket olyan hitelesítésszolgáltatótól származó tanúsítvány hozzáadásához, amely nem áll Key Vault. (A GoDaddy például nem megbízható Key Vault hitelesítésszolgáltató.)

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Ugrás arra a kulcstartóra, amelybe a tanúsítványt hozzá szeretné adni.
1. A Tulajdonságok lapon válassza a Tanúsítványok **lehetőséget.**
1. Válassza a **Generate/Import (Generálás/Importálás)** lapot.
1. A Tanúsítvány **létrehozása képernyőn** válassza a következő értékeket:
    - **Tanúsítvány-létrehozási módszer:** Létrehozás.
    - **Tanúsítvány neve:** ContosoManualCSRCertificate.
    - **Hitelesítésszolgáltató típusa:** Nem integrált hitelesítésszolgáltató által kibocsátott tanúsítvány.
    - **Tárgy:** `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Ha relatív megkülönböztető nevet (RDN) használ, amely vesszővel (,) rendelkezik az értékben, akkor a speciális karaktert tartalmazó értéket dupla idézőjelek közé burkoltuk. 
     >
     > Példa a Tárgy **bejegyzésre:**`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Ebben a példában az RDN `OU` egy vesszővel megadott értéket tartalmaz. Az eredményül kapott kimenet `OU` a **Következő: Docs, Contoso.**
1. Jelölje ki a többi értéket, majd válassza a **Létrehozás** lehetőséget a tanúsítvány tanúsítványok listájához **való hozzáadásához.**

    ![A tanúsítvány tulajdonságainak képernyőképe](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. A Tanúsítványok **listában** válassza ki az új tanúsítványt. A tanúsítvány aktuális állapota le **van tiltva,** mert a hitelesítésszolgáltató még nem bocsátotta ki.
1. A **Tanúsítványművelet lapon** válassza a **CSR letöltése lehetőséget.**

   ![Képernyőkép a CSR letöltése gomb kiemelésével.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. A HITELESÍTÉSszolgáltató írja alá a CSR-t (.csr).
1. A kérelem aláírása után  válassza az  Aláírt kérelem egyesítése lehetőséget a Tanúsítványművelet lapon az aláírt tanúsítvány hozzáadásához a Key Vault.

A tanúsítványkérelem most már sikeresen összefésülve lett.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy tanúsítvány-házirendet. Mivel az ebben a forgatókönyvben kiválasztott hitelesítésszolgáltató nincs partneri kapcsolattal, az **IssuerName** beállítása **Ismeretlen,** Key Vault nem regisztrálja vagy újítja meg a tanúsítványt.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Ha olyan relatív megkülönböztető nevet (RDN) használ, amely vesszővel (,) rendelkezik az értékben, használjon egyszeres idézőjelet a teljes értékhez vagy értékhalmazhoz, és burkoltuk a speciális karaktert tartalmazó értéket dupla idézőjelek közé. 
     >
     >Példa a **SubjectName bejegyzésre:** `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . Ebben a példában az `OU` érték a **következő: Docs, Contoso.** Ez a formátum minden olyan érték esetén működik, amely vesszőt tartalmaz.
     > 
     > Ebben a példában az RDN `OU` egy vesszővel megadott értéket tartalmaz. Az eredményül kapott kimenet `OU` a **Docs, Contoso.**

1. Hozza létre a CSR-t.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Írja alá a CSR-t a hitelesítésszolgáltatóval. A `$csr.CertificateSigningRequest` a tanúsítvány alapkódolt CSR-fájlja. Ezt a blobot a kiállító tanúsítványigénylési webhelyére is kihozhatja. Ez a lépés hitelesítésszolgáltatóról hitelesítésszolgáltatóra változik. Keresse meg a CA útmutatóját a lépés végrehajtásához. Olyan eszközöket is használhat, mint a certreq vagy az openssl a CSR aláírásának lekéréséhez és a tanúsítvány generálásának befejezéséhez.

1. Egyesítheti az aláírt kérést a Key Vault. A tanúsítványkérelem aláírása után egyesítheti azt a tanúsítványban létrehozott kezdeti titkos/nyilvános Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

A tanúsítványkérelem most már sikeresen összefésülve lett.

---

## <a name="add-more-information-to-the-csr"></a>További információ hozzáadása a CSR-hez

Ha további információkat szeretne hozzáadni a CSR létrehozásakor, adja meg azt a **SubjectName (Tulajdonosnév) mezőben.** Érdemes lehet például a következő információkat hozzáadni:
- Ország
- Város/helység
- Állam/megye
- Szervezet
- Szervezeti egység

Példa

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Ha további információkkal együtt kér tartományérvényesítési (DV) tanúsítványt, a CA elutasíthatja a kérelmet, ha nem tudja ellenőrizni a kérelemben található összes információt. A további információk megfelelőbbek lehetnek, ha szervezeti érvényesítési (OV) tanúsítványt kér.

## <a name="faqs"></a>Gyakori kérdések

- Hogyan vagy kezelni a CSR-t?

     Lásd: [Tanúsítvány létrehozásának figyelése és kezelése.](./create-certificate-scenarios.md)

- Mi a következő hibatípus: "A végfelhasználói tanúsítvány nyilvános kulcsa a megadott X.509-tanúsítványtartalomban nem egyezik meg a megadott titkos kulcs nyilvános **részében. Ellenőrizze, hogy a tanúsítvány érvényes-e"**?

     Ez a hiba akkor fordul elő, ha nem egyesíti az aláírt CSR-t a kezdeményezett CSR-kéréssel. Minden létrehozott új CSR rendelkezik egy titkos kulccsal, amelynek egyeznie kell az aláírt kérelem egyesítésekor.

- A CSR egyesítésekor a teljes láncot egyesíti?

     Igen, a teljes láncot egyesíti, feltéve, hogy a felhasználó egy .p7b fájlt hozott vissza az egyesítéshez.

- Mi történik, ha a kiadott tanúsítvány letiltott állapotú a Azure Portal?

     A **Tanúsítványművelet lapon** tekintse át a tanúsítványhoz vonatkozó hibaüzenetet.

- Mi a következő hibatípus: "A megadott tulajdonosnév **nem érvényes X500 név"?**

     Ez a hiba akkor fordulhat **elő, ha a SubjectName speciális** karaktereket tartalmaz. Tekintse meg a megjegyzéseket a Azure Portal PowerShell-utasítások között.

---

## <a name="next-steps"></a>Következő lépések

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault REST API referencia](/rest/api/keyvault)
- [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate)
- [Tárolók – Hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy)