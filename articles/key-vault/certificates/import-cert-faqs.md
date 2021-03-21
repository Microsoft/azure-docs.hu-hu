---
title: Gyakori kérdések – Azure Key Vault tanúsítvány importálása
description: Választ kaphat a Azure Key Vault tanúsítványok importálásával kapcsolatos gyakori kérdésekre.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 78ec0af0acbb74436af16abba75d64de061d5268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202165"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Azure Key Vault-tanúsítványok importálása – gyakori kérdések

Ez a cikk a Azure Key Vault tanúsítványok importálásával kapcsolatos gyakori kérdésekre ad választ.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hogyan importálhatók tanúsítvány Azure Key Vault?

A tanúsítvány-importálási művelethez Azure Key Vault két tanúsítványfájl-formátumot fogad el: PEM és PFX. Bár a PEM-fájlok csak a nyilvános részből állnak, Key Vault igényel, és csak egy PEM-vagy PFX-fájlt fogad el titkos kulccsal. További információ: [tanúsítvány importálása Key Vaultba](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Miután importáltam egy jelszóval védett tanúsítványt Key Vault, majd letölti, miért nem látom a hozzá társított jelszót?
    
A tanúsítvány importálását és védelmét követően Key Vault a hozzá tartozó jelszót nem menti a rendszer. A jelszót csak egyszer kell megadni az importálási művelet során. Ez a kialakítás alapján történik, de mindig titkos kulcsot kaphat a tanúsítványhoz, és a Base64-ről PFX-re alakíthatja a jelszót [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)használatával.

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hogyan oldható fel a "Hibás paraméter" hiba? Melyek a Key Vaultba való importálás támogatott tanúsítványainak formátuma?

Tanúsítvány importálásakor gondoskodnia kell arról, hogy a kulcs szerepeljen a fájlban. Ha más formátumban külön tárolt titkos kulccsal rendelkezik, a kulcsot össze kell kapcsolni a tanúsítvánnyal. Egyes hitelesítésszolgáltatók (CA-k) más formátumú tanúsítványokat biztosítanak. Ezért a tanúsítvány importálása előtt győződjön meg arról, hogy a PEM-vagy PFX-fájlformátumban van, és hogy a kulcs Rivest – a Adleman (RSA) vagy az elliptikus-görbe titkosítási (ECC) titkosítást használja. 

További információ: a [tanúsítványokra vonatkozó követelmények](./certificate-scenarios.md#formats-of-import-we-support) és a [tanúsítvány kulcsa](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Importálható egy tanúsítvány egy ARM-sablon használatával?

Nem, Azure Resource Manager-(ARM-) sablon használatával nem lehet végrehajtani a tanúsítvány-műveleteket. Ajánlott megkerülő megoldásként használja az Azure API, az Azure CLI vagy a PowerShell tanúsítvány-importálási módszereit. Ha rendelkezik meglévő tanúsítvánnyal, akkor azt titkos fájlként importálhatja.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Ha a Azure Portal keresztül importálok egy tanúsítványt, "hiba történt" hibaüzenetet kapok. Hogyan lehet további vizsgálatot végezni?
    
Ha részletesebben szeretne megtekinteni egy leíró hibát, importálja a tanúsítványt [Az Azure CLI vagy a](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)használatával.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hogyan oldható fel a "hiba típusa: hozzáférés megtagadva, vagy a felhasználó nem jogosult a tanúsítvány importálására"?
    
Az importálási művelethez a felhasználónak engedélyeket kell adnia a tanúsítvány importálásához a hozzáférési házirendekben. Ehhez nyissa meg a Key vaultot, és válassza a **hozzáférési szabályzatok**  >  **hozzáférési házirend hozzáadása** a  >  tanúsítvány-engedélyek rendszerbiztonsági tag **kiválasztása elemet**,  >  keresse meg a felhasználót, majd adja hozzá a felhasználó e-mail-címét. 

További információ a tanúsítványokkal kapcsolatos hozzáférési házirendekről: [Tudnivalók a Azure Key Vault tanúsítványokról](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hogyan oldható fel a "hiba típusa: ütközés a tanúsítvány létrehozásakor"?
    
Minden tanúsítvány nevének egyedinek kell lennie. Előfordulhat, hogy egy ugyanilyen nevű tanúsítvány nem törölhető állapotban van. Továbbá a [tanúsítvány összetétele](./about-certificates.md#composition-of-a-certificate)szerint amikor új tanúsítvány jön létre, létrehoz egy azonos nevű címezhető titkos kulcsot, ha a kulcstartóban van egy másik kulcs vagy titok, amelynek a neve megegyezik a tanúsítványhoz megadott névvel, a tanúsítvány létrehozása sikertelen lesz, és el kell távolítania a kulcsot vagy a titkos kulcsot, vagy más nevet kell használnia a tanúsítványhoz. 

További információ: [Get Deleted Certificate Operation](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Miért kapok "hiba típusa: karakter hossza túl hosszú"?
Ezt a hibát két ok okozhatja:    
* A tanúsítvány tulajdonosának neve legfeljebb 200 karakter hosszú lehet.
* A tanúsítvány jelszava 200 karakterre van korlátozva.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Hiba: "a megadott PEM X. 509 tanúsítvány tartalma nem várt formátumú. Ellenőrizze, hogy a tanúsítvány érvényes PEM formátumú-e. "
Ellenőrizze, hogy a PEM-fájl tartalma UNIX-stílusú vonal-elválasztó karaktereket használ-e. `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Importálhatók a lejárt tanúsítványok Azure Key Vault?
    
Nem, a lejárt PFX-tanúsítványok nem importálhatók Key Vaultba.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hogyan válthatom be a tanúsítványt a megfelelő formátumba?

Megkérheti a HITELESÍTÉSSZOLGÁLTATÓT, hogy a szükséges formátumban adja meg a tanúsítványt. Vannak olyan külső eszközök is, amelyek segítségével a tanúsítvány megfelelő formátumba konvertálható.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Importálhatók a nem partneri hitelesítésszolgáltatóktól származó tanúsítványok?
Igen, bármely HITELESÍTÉSSZOLGÁLTATÓTÓL importálhat tanúsítványokat, de a kulcstartó nem fogja tudni automatikusan megújítani őket. Az emlékeztetőket beállíthatja úgy, hogy értesítést kapjon a tanúsítvány lejáratáról.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Ha importálok egy tanúsítványt egy partner HITELESÍTÉSSZOLGÁLTATÓTÓL, az automatikus megújítási funkció továbbra is működik?
Igen. A tanúsítvány feltöltése után ne felejtse el megadnia az autorotációt a tanúsítvány kiállítási házirendjében. A beállítások érvényben maradnak a következő ciklus vagy a tanúsítvány verziójának kiadása előtt.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Miért nem látom a Key Vaultba importált App Service-tanúsítványt? 
Ha sikeresen importálta a tanúsítványt, a **titkok** ablaktáblán ellenőrizheti, hogy meg tudja-e erősíteni.


## <a name="next-steps"></a>Következő lépések

- [Tanúsítványok Azure Key Vault](./about-certificates.md)
