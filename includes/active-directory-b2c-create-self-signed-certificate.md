---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095289"
---
Ha még nem rendelkezik tanúsítvánnyal, használhat önaláírt tanúsítványt is. Az önaláírt tanúsítvány olyan biztonsági tanúsítvány, amelyet nem a hitelesítésszolgáltató (CA) aláír, és nem biztosítja a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvány biztonsági garanciáit. 

# <a name="windows"></a>[Windows](#tab/windows)

Windows rendszeren a PowerShell [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmagjának használatával hozhatja elő a tanúsítványt.

1. Futtassa ezt a PowerShell-parancsot egy önaláírt tanúsítvány létrehozásához. Módosítsa az `-Subject` argumentumot megfelelően az alkalmazáshoz, és Azure ad B2C a bérlő nevét. A dátumot úgy is beállíthatja `-NotAfter` , hogy a tanúsítványhoz eltérő lejáratot határozzon meg.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Nyissa meg a **felhasználói tanúsítványok kezelése**  >  **aktuális felhasználói**  >  **személyes**  >  **tanúsítványok**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Válassza ki a tanúsítványt, majd válassza a **művelet**  >  **minden feladat**  >  **Exportálás** lehetőséget.
1. Válassza az **Igen**  >  **tovább** lehetőséget  >  **, majd exportálja a titkos kulcsot a**  >  **Tovább gombra**.
1. Fogadja el az **exportálási** fájlformátum alapértelmezett értékeit.
1. Adja meg a tanúsítványhoz tartozó jelszót.

A. pfx fájl jelszavának elfogadásához a jelszónak titkosítottnak kell lennie a Windows tanúsítványtároló exportálási segédprogramjának TripleDES-SHA1 kapcsolóval, a AES256-SHA256 szemben. Azure AD B2C

# <a name="macos"></a>[macOS](#tab/macos)

MacOS rendszeren használja a [tanúsítvány-Segédet](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) a kulcstartó-hozzáférésben a tanúsítvány létrehozásához.

1. Kövesse az [önaláírt tanúsítványok a Mac-ben való kulcstartó-hozzáféréshez való létrehozásával](https://support.apple.com/guide/keychain-access/kyca8916/mac)kapcsolatos utasításokat.
1. A kulcstartó-hozzáférési alkalmazás Mac gépen válassza ki a létrehozott tanúsítványt.
1. Válassza a **fájl**  >  **exportálása elemet**.
1. Válassza ki a fájl nevét a tanúsítvány mentéséhez. Például: **önaláírt tanúsítvány. P12**.
1. A **fájl formátuma** mezőben válassza a **személyes információcsere (. P12)** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. Adja meg a **jelszót**, majd **ellenőrizze** a jelszót.
1. Cserélje le a fájlkiterjesztést a következőre: `.pfx` . Például: **Self-Signed-Certificate. pfx**.

---