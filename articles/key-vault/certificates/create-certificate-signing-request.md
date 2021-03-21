---
title: CSR létrehozása és egyesítése Azure Key Vault
description: Ismerje meg, hogyan hozhat létre és egyesítheti a CSR-t a Azure Key Vaultban.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789027"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>CSR létrehozása és egyesítése Key Vault

Azure Key Vault támogatja a bármely hitelesítésszolgáltató (CA) által kiadott digitális tanúsítványok tárolását. Támogatja a tanúsítvány-aláírási kérelem (CSR) létrehozását privát/nyilvános kulcspár esetén. A CSR-t bármely HITELESÍTÉSSZOLGÁLTATÓ (belső vállalati HITELESÍTÉSSZOLGÁLTATÓ vagy külső nyilvános HITELESÍTÉSSZOLGÁLTATÓ) aláírhatja. A CSR egy olyan üzenet, amelyet a HITELESÍTÉSSZOLGÁLTATÓnak küld a digitális tanúsítvány igénylése érdekében.

A tanúsítványokkal kapcsolatos további általános információkért lásd: [Azure Key Vault tanúsítványok](./about-certificates.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Tanúsítványok hozzáadása a partneri hitelesítésszolgáltatók által kiadott Key Vault

A tanúsítványok létrehozásának egyszerűbbé tétele érdekében Key Vault partnereket a következő hitelesítésszolgáltatók használatával.

|Szolgáltató|Tanúsítvány típusa|Konfiguráció beállítása  
|--------------|----------------------|------------------|  
|DigiCert|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a DigiCert| [Integrációs útmutató](./how-to-integrate-certificate-authority.md)
|GlobalSign|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a GlobalSign| [Integrációs útmutató](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>A nem partneri hitelesítésszolgáltatók által kiadott Key Vault tanúsítványok hozzáadása

A következő lépésekkel adhat hozzá olyan tanúsítványokat a hitelesítésszolgáltatókról, amelyek nem a Key Vaulthoz vannak társítva. (Például a GoDaddy nem megbízható Key Vault CA.)

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Nyissa meg azt a kulcstartót, amelyhez hozzá szeretné adni a tanúsítványt.
1. A Tulajdonságok lapon válassza a **tanúsítványok** lehetőséget.
1. Válassza a **létrehozó/importálás** lapot.
1. A **tanúsítvány létrehozása** képernyőn válassza ki a következő értékeket:
    - **Tanúsítvány létrehozásának metódusa**: létrehozás.
    - **Tanúsítvány neve**: ContosoManualCSRCertificate.
    - **Hitelesítésszolgáltató (CA)**: nem integrált hitelesítésszolgáltató által kiállított tanúsítvány.
    - **Tárgy**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Ha relatív megkülönböztető nevet (RDN) használ, és az értékben vessző (,) szerepel, a speciális karaktert idézőjelek közé rendezheti. 
     >
     > Példa a **tárgyra**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Ebben a példában a RDN `OU` olyan értéket tartalmaz, amelynek a neve a vessző. Az eredményül kapott kimenet a `OU` **docs, contoso**.
1. Válassza ki a többi értéket a kívánt módon, majd válassza a **Létrehozás** lehetőséget a tanúsítvány hozzáadásához a **tanúsítványok** listához.

    ![Képernyőfelvétel a tanúsítvány tulajdonságairól](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. A **tanúsítványok** listán válassza ki az új tanúsítványt. A tanúsítvány jelenlegi állapota **le van tiltva** , mert még nem állította ki a hitelesítésszolgáltató.
1. A **tanúsítvány művelete** lapon válassza a **CSR letöltése** lehetőséget.

   ![Képernyőfelvétel: a CSR letöltése gomb.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. A HITELESÍTÉSSZOLGÁLTATÓ aláírja a CSR-t (. CSR).
1. A kérelem aláírása után válassza a **tanúsítvány művelet** lapján az **aláírt tanúsítvány egyesítése** lehetőséget, hogy hozzáadja az aláírt tanúsítványt Key Vaulthoz.

A tanúsítványkérelem sikeresen egyesítve lett.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy tanúsítvány-házirendet. Mivel az ebben a forgatókönyvben kiválasztott HITELESÍTÉSSZOLGÁLTATÓ nem partner, a **IssuerName** **ismeretlenre** van állítva, és a Key Vault nem regisztrálja vagy újítja meg a tanúsítványt.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Ha olyan relatív megkülönböztető nevet (RDN) használ, amely az értékben vessző (,) szerepel, a teljes értékhez vagy a beállított értékhez használjon aposztrófot, és a speciális karaktert tartalmazó értéket adja meg idézőjelek között. 
     >
     >Példa a **SubjectName** bejegyzésre: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . Ebben a példában az érték a következőként van `OU` beolvasva: **docs, contoso**. Ez a formátum minden olyan érték esetében működik, amely egy vesszőt tartalmaz.
     > 
     > Ebben a példában a RDN `OU` olyan értéket tartalmaz, amelynek a neve a vessző. Az eredményül kapott kimenet a `OU` **docs, contoso**.

1. Hozza létre a CSR-t.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. A HITELESÍTÉSSZOLGÁLTATÓ aláírja a CSR-t. A a `$csr.CertificateSigningRequest` tanúsítvány alap kódolású CSR-je. Ezt a blobot a kiállító tanúsítványkérelem webhelyére lehet kibocsátani. Ez a lépés a CA-tól a CA-ig változik. Keresse meg a HITELESÍTÉSSZOLGÁLTATÓ útmutatását a lépés végrehajtásához. Olyan eszközöket is használhat, mint például a certreq vagy az OpenSSL, hogy bejelentkezzen a CSR-be, és fejezze be a tanúsítvány generálásának folyamatát.

1. Egyesítse az aláírt kérelmet Key Vaultban. A tanúsítványkérelem aláírása után egyesítheti azt a Azure Key Vault-ben létrehozott kezdeti titkos/nyilvános kulcspár használatával.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

A tanúsítványkérelem sikeresen egyesítve lett.

---

## <a name="add-more-information-to-the-csr"></a>További információk hozzáadása a CSR-hez

Ha további információt szeretne hozzáadni a CSR létrehozásához, adja meg azt a **SubjectName**-ben. Előfordulhat, hogy például a következő adatokat szeretné felvenni:
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
> Ha további információkat tartalmazó tartomány-ellenőrzési (DV) tanúsítványt kér, akkor a HITELESÍTÉSSZOLGÁLTATÓ elutasítja a kérést, ha nem tudja érvényesíteni a kérelemben szereplő összes információt. A további információk megfelelőbbek lehetnek, ha a szervezet ellenőrzési (OV) tanúsítványát kéri.

## <a name="faqs"></a>Gyakori kérdések

- Hogyan a CSR figyelése vagy kezelése?

     Lásd: [a tanúsítványok létrehozásának figyelése és kezelése](./create-certificate-scenarios.md).

- Mi a teendő, ha az **adott X. 509 tanúsítványban található végfelhasználói tanúsítvány nyilvános kulcsa nem egyezik a megadott titkos kulcs nyilvános részével. Ellenőrizze, hogy érvényes-e a tanúsítvány**?

     Ez a hiba akkor fordul elő, ha nem egyesíti az aláírt CSR-t ugyanazzal a CSR-kéréssel, amelyet kezdeményezett. Minden újonnan létrehozott új CSR rendelkezik egy titkos kulccsal, amelynek egyeznie kell az aláírt kérelem egyesítésével.

- Ha a CSR egyesítve van, a rendszer egyesíti a teljes láncot?

     Igen, a rendszer egyesíti a teljes láncot, ha a felhasználó egy. p7b fájlt állított vissza az egyesítéshez.

- Mi a helyzet, ha a kiállított tanúsítvány letiltott állapotban van a Azure Portalban?

     A tanúsítvány **művelete** lapon tekintse át a tanúsítványhoz tartozó hibaüzenetet.

- Mit tegyek **, ha a hiba típusa "a megadott tulajdonos neve nem érvényes X500 név"**?

     Ez a hiba akkor fordulhat elő, ha a **SubjectName** speciális karaktereket tartalmaz. Lásd: Megjegyzések a Azure Portal és a PowerShell utasításokban.

---

## <a name="next-steps"></a>Következő lépések

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault REST API-hivatkozás](/rest/api/keyvault)
- [Tárolók – létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate)
- [Tárolók – hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy)