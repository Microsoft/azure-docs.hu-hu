---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916920"
---
Ha még nem rendelkezik tanúsítvánnyal, az oktatóanyaghoz használhat önaláírt tanúsítványt is. Az önaláírt tanúsítvány olyan biztonsági tanúsítvány, amelyet nem a hitelesítésszolgáltató (CA) írta alá. Nem biztosítanak a hitelesítésszolgáltató által aláírt tanúsítványok összes biztonsági garanciáját. 

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

1. A **felhasználói tanúsítványok kezelése**  >  **aktuális felhasználói**  >  **személyes**  >  **tanúsítványok**  >  *yourappname.yourtenant.onmicrosoft.com* megnyitása
1. Válassza ki a tanúsítványt > **művelet**  >  **minden feladat**  >  **Exportálás** elemet.
1. Válassza az **Igen**  >  **tovább** lehetőséget  >  **, majd exportálja a titkos kulcsot**  >  **tovább**
1. Az **exportálási fájlformátum** alapértelmezett értékének elfogadása
1. Adja meg a tanúsítványhoz tartozó jelszót.

Ahhoz, hogy a Azure AD B2C el tudja fogadni a. pfx-fájl jelszavát, a jelszót titkosítani kell a Windows tanúsítványtároló-exportálás segédprogram TripleDES-SHA1 kapcsolóval, a AES256-SHA256 szemben.

# <a name="macos"></a>[macOS](#tab/macos)

MacOS rendszeren használja a [tanúsítvány-Segédet](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) a kulcstartó-hozzáférésben a tanúsítvány létrehozásához.

1. Kövesse a következő utasításokat [: önaláírt tanúsítványok létrehozása a kulcstartó-hozzáférés Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac)számítógépen.
1. A kulcstartó-hozzáférési alkalmazás Mac gépen válassza ki a létrehozott tanúsítványt.
1. Válassza a fájl > exportálás elemet.
1. Válassza ki a fájl nevét a tanúsítvány mentéséhez. Például: **önaláírt tanúsítvány. P12**. 
1. A **fájl formátuma** mezőben válassza a **személyes információcsere (. P12)** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. Adja meg a **jelszót**, majd **ellenőrizze** a jelszót.
1. Cserélje le a fájlkiterjesztést a következőre: `.pfx` . Például: **Self-Signed-Certificate. pfx**. 

---
