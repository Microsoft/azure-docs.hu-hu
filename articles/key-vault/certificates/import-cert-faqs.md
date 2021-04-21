---
title: Gyakori kérdések – Azure Key Vault importálása
description: Választ kaphat a tanúsítványok importálására Azure Key Vault gyakori kérdésekre.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 97dfc2db837f728b8cb4ece9a064f99006c9996b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767816"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Gyakori kérdések Azure Key Vault tanúsítványok importálásáról

Ez a cikk a tanúsítványok importálására vonatkozó gyakori Azure Key Vault ad választ.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hogyan importálható tanúsítvány a Azure Key Vault?

Tanúsítványimportolási művelet esetén a Azure Key Vault a PEM és a PFX tanúsítványfájlformátumot. Bár vannak olyan PEM-fájlok, amelyek csak a nyilvános részből állnak, Key Vault csak egy titkos kulccsal Key Vault PEM- vagy PFX-fájlt fogad el. További információ: [Tanúsítvány importálása a](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)Key Vault.

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Miután importáltam egy jelszóval védett tanúsítványt a Key Vault, majd letöltöttem, miért nem látom a hozzá tartozó jelszót?
     
Miután importált és védett egy tanúsítványt a Key Vault, a hozzá tartozó jelszó nem lesz mentve. A jelszóra csak egyszer van szükség az importálási művelet során. Ez a kialakításból áll, de a tanúsítványt mindig lekérheti titkos ként, és konvertálhatja Base64-ről PFX-re a jelszó hozzáadásával a [Azure PowerShell.](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hogyan oldható meg egy "Hibás paraméter" hiba? Melyek a támogatott tanúsítványformátumok a Key Vault?

Tanúsítvány importálkor meg kell győződni arról, hogy a kulcs szerepel a fájlban. Ha a titkos kulcsot külön, eltérő formátumban tárolja, akkor a kulcsot a tanúsítvánnyal kell kombinálni. Egyes hitelesítésszolgáltató (HITELESÍTÉSSZOLGÁLTATÓ) más formátumban biztosít tanúsítványokat. Ezért a tanúsítvány importálása előtt győződjön meg arról, hogy PEM- vagy PFX-fájlformátumban van, és hogy a kulcs Vagy aGép–Shamir–Adleman (RSA) vagy az elliptikus görbés titkosítást (ECC) használja. 

További információ: [Tanúsítványkövetelmények](./certificate-scenarios.md#formats-of-import-we-support) és [tanúsítványkulcs-követelmények.](../keys/about-keys.md)

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Importálható tanúsítvány ARM-sablonnal?

Nem, tanúsítványműveleteket nem lehet végrehajtani egy Azure Resource Manager -sablonnal. Az ajánlott áthidaló megoldás a tanúsítványimportozási módszerek használata az Azure API-ban, az Azure CLI-ban vagy a PowerShellben. Ha rendelkezik meglévő tanúsítvánnyal, titkos ként importálhatja.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Amikor tanúsítványt importálok a Azure Portal, "Hiba történt" hibaüzenetet kapok. Hogyan vizsgálhatja meg jobban a vizsgálatot?
     
Egy leíróbb hiba megtekintéséhez importálja a tanúsítványfájlt az [Azure CLI](/cli/azure/keyvault/certificate#az_keyvault_certificate_import) vagy a [PowerShell használatával.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hogyan oldható fel a "Hibatípus: Hozzáférés megtagadva, vagy a felhasználó nem jogosult tanúsítvány importálásához"?
    
Az importálási művelethez engedélyeket kell ad a felhasználónak a tanúsítvány importálásához a hozzáférési házirendek alatt. Ehhez keresse meg a kulcstartót, válassza a Hozzáférési szabályzatok Hozzáférési szabályzat hozzáadása Hozzáférési szabályzat kiválasztása Tanúsítványengedélyek egyszerűje lehetőséget, keresse meg a felhasználót, majd adja meg a felhasználó e-mail-címét.  >    >    >   

A tanúsítvánnyal kapcsolatos hozzáférési házirendekkel kapcsolatos további információkért lásd: Tudnivalók Azure Key Vault [tanúsítványokról.](./about-certificates.md#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hogyan oldható fel a "Hibatípus: Ütközés tanúsítvány létrehozásakor"?
    
Minden tanúsítványnévnek egyedinek kell lennie. Előfordulhat, hogy egy azonos nevű tanúsítvány állapota nem törölhető. Emellett a tanúsítvány [](./about-certificates.md#composition-of-a-certificate)összeállítása alapján az új tanúsítvány létrehozásakor egy címezhető titkos kulcsot hoz létre ugyanazokkal a névvel, így ha a kulcstartóban van egy másik kulcs vagy titkos kulcs ugyanazokkal a névvel, mint amit a tanúsítványhoz szeretne megadni, a tanúsítvány létrehozása sikertelen lesz, és el kell távolítania a kulcsot vagy titkos kulcsot, vagy másik nevet kell használnia a tanúsítványhoz. 

További információ: [Get Deleted Certificate operation ..](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Miért jelenik meg a "Hibatípus: a karakter hossza túl hosszú"?
Ezt a hibát két ok okozhatja:    
* A tanúsítvány tulajdonosának neve legfeljebb 200 karakter lehet.
* A tanúsítvány jelszava legfeljebb 200 karakter hosszúságú lehet.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>"A megadott PEM X.509-tanúsítvány tartalma váratlan formátumú. Ellenőrizze, hogy a tanúsítvány érvényes PEM formátumú-e."
Ellenőrizze, hogy a PEM-fájl tartalma UNIX stílusú sorelválasztókat használ-e `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Importálható lejárt tanúsítvány a Azure Key Vault?
    
Nem, a lejárt PFX-tanúsítványok nem importálhatók Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hogyan konvertálható a tanúsítvány a megfelelő formátumra?

Megkérheti a hitelesítésszolgáltatót, hogy a szükséges formátumban adja meg a tanúsítványt. Vannak olyan külső eszközök is, amelyek segíthetnek a tanúsítvány megfelelő formátumra konvertálásában.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Importálhatóak tanúsítványok a nem partner hitelesítést használhatjak?
Igen, bármelyik hitelesítésszolgáltatóról importálhat tanúsítványokat, de a kulcstartó nem tudja automatikusan megújítani őket. Beállíthatja, hogy az emlékeztetők értesítést kapnak a tanúsítvány lejáratával kapcsolatban.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Ha tanúsítványt importálok egy partner hitelesítésszolgáltatótól, az automatikus új funkció továbbra is működni fog?
Igen. A tanúsítvány feltöltése után mindenképpen adja meg az automatikus hitelesítést a tanúsítvány kiállítási szabályzatában. A beállítások a következő ciklus vagy tanúsítványverzió kiadig érvényben maradnak.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Miért nem látom a App Service importált Key Vault? 
Ha sikeresen importálta a tanúsítványt, a Titkos kulcsok panelen meg kell tudnia **erősíteni.**


## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault tanúsítványok](./about-certificates.md)
