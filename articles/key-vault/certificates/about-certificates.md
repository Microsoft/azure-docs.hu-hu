---
title: Az Azure Key Vault tanúsítványok – Azure Key Vault
description: A REST Azure Key Vault és tanúsítványok áttekintése.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: fb69068ddac311a8020a76eec9b18fab3256fea6
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752533"
---
# <a name="about-azure-key-vault-certificates"></a>Információk az Azure Key Vault-tanúsítványokról

Key Vault támogatja az x509-tanúsítványok kezelését és a következő viselkedéseket:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa tanúsítványt hozzon létre egy Key Vault létrehozási folyamattal vagy egy meglévő tanúsítvány importálásával. Tartalmazza az önaírt és a hitelesítésszolgáltató által létrehozott tanúsítványokat is.
-   Lehetővé teszi Key Vault tanúsítványtulajdonos számára, hogy biztonságos tárolást és X509-tanúsítványkezelést valósítson meg a titkos kulcsokkal való interakció nélkül.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa olyan házirendet hozzon létre, amely Key Vault a tanúsítvány életciklusának kezelésére.  
-   Lehetővé teszi a tanúsítványtulajdonosok számára, hogy kapcsolattartási adatokat adjanak meg a lejárati és a tanúsítvány megújítási életciklusával kapcsolatos értesítésekhez.  
-   Támogatja az automatikus megújítást a kiválasztott kiállítókkal – Key Vault X509-tanúsítványszolgáltatók/hitelesítésszolgáltatók.

>[!Note]
>A nem partneri szolgáltatók/szolgáltatók szintén engedélyezettek, de nem támogatják az automatikus megújítási funkciót.

## <a name="composition-of-a-certificate"></a>Tanúsítvány összetétele

A Key Vault létrehozásakor egy címezhető kulcs és egy titkos kulcs is létrejön ugyanazokkal a névvel. A Key Vault kulcs lehetővé teszi a kulcsműveleteket, Key Vault titkos kulcs lehetővé teszi a tanúsítvány értékének titkos kulcsként való lekérését. A Key Vault tanúsítvány nyilvános x509-tanúsítvány metaadatait is tartalmazza.  

A tanúsítványok azonosítója és verziója hasonló a kulcsok és titkos kulcsok azonosítóihoz. A tanúsítvány tanúsítványának verziójával létrehozott címezhető kulcs és titkos Key Vault a tanúsítvány válaszában Key Vault érhető el.
 
![A tanúsítványok összetett objektumok](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exportálható vagy nem exportálható kulcs

Amikor létrejön Key Vault tanúsítvány, a titkos kulccsal lekérhető a címezhető titkos kulcsból PFX vagy PEM formátumban. A tanúsítvány létrehozásához használt házirendnek jeleznie kell, hogy a kulcs exportálható. Ha a szabályzat nem exportálhatóként jelzi, akkor a titkos kulcs nem része az értéknek, amikor titkos kulcsként lekéri.  

A címezhető kulcs relevánsabbá válik a nem exportálható KV-tanúsítványok esetében. A címezhető KV-kulcs műveletei a KV-tanúsítvány létrehozásához használt KV-tanúsítvány házirend *keyusage* mezőjéből vannak leképezve.  

A tanúsítványok által támogatott kulcspár típusa

 - Támogatott kulcstípusok: RSA, RSA-HSM, EC, EC-HSM, október (itt listázva) [](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)Az exportálható csak RSA, EC esetén engedélyezett. A HSM-kulcsok nem exportálhatók.

|Kulcs típusa|Névjegy|Biztonság|
|--|--|--|
|**RSA**| "Szoftveres védelem" RSA-kulcs|FIPS 140-2 1. szint|
|**RSA-HSM**| "HSM által védett" RSA-kulcs (csak prémium termékváltozat)|FIPS 140-2 Level 2 HSM|
|**EC**| "Szoftveres védelem" Három ponttal védett görbe kulcs|FIPS 140-2 1. szint|
|**EC-HSM**| "HSM által védett" három ponttal védett görbekulcs (csak prémium termékváltozat)|FIPS 140-2 Level 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>Tanúsítványattribútumok és címkék

A tanúsítvány metaadatain, a címezhető kulcsokon és a címezhető titkos kulcsokon kívül az Key Vault tanúsítvány attribútumokat és címkéket is tartalmaz.  

### <a name="attributes"></a>Attribútumok

A tanúsítványattribútumok a KV-tanúsítvány létrehozásakor létrehozott címezhető kulcs és titkos kulcs attribútumaiba vannak tükrözve.  

A Key Vault tanúsítvány a következő attribútumokkal rendelkezik:  

-   *enabled*: boolean, optional, default is **true**. Meg lehet adni annak jelzésére, hogy a tanúsítványadatok lekértek titkos kulcsként vagy kulcsként működőképesek-e. Az *nbf* és az *exp* értékkel együtt is használatos, ha egy művelet *az nbf* és *az exp* között történik, és csak akkor engedélyezett, ha az engedélyezve van true (igaz) értékkel. Az *nbf és* *az exp ablakon* kívüli műveletek automatikusan nem engedélyezettek.  

A válasz további csak olvasható attribútumokat is tartalmaz:

-   *created*: IntDate: azt jelzi, hogy mikor jött létre a tanúsítvány ezen verziója.  
-   *updated*: IntDate: azt jelzi, hogy mikor frissült a tanúsítvány ezen verziója.  
-   *exp*: IntDate: az x509-tanúsítvány lejárati dátumának értékét tartalmazza.  
-   *nbf*: IntDate: az x509-tanúsítvány dátumának értékét tartalmazza.  

> [!Note] 
> Ha egy Key Vault-tanúsítvány lejár, az ahhoz tartozó címezhető kulcs és titkos kulcs nem fog működni.  

### <a name="tags"></a>Címkék

 Az ügyfél által megadott kulcs-érték párok szótára hasonló a kulcsokban és titkos kulcsokban található címkékhez.  

 > [!Note]
> A címkék akkor olvashatók, ha  a  hívó rendelkezik a listával, vagy engedélyt kap erre az objektumtípusra (kulcsok, titkos kulcsok vagy tanúsítványok).

## <a name="certificate-policy"></a>Tanúsítvány-házirend

A tanúsítvány-házirendek információt tartalmaznak arról, hogyan hozható létre és kezelhető egy Key Vault életciklusa. Amikor titkos kulccsal importál egy tanúsítványt a kulcstartóba, az x509-tanúsítvány beolvasásával létrejön egy alapértelmezett házirend.  

Amikor Key Vault új tanúsítvány jön létre, meg kell adni egy szabályzatot. A házirend határozza meg, hogyan hozhatja létre ezt Key Vault tanúsítványverziót, vagy a tanúsítvány következő Key Vault verzióját. A szabályzat a létrehozása után nem szükséges az egymást követő létrehozási műveletekkel a jövőbeli verziókhoz. A szabályzatnak csak egy példánya van a tanúsítvány összes Key Vault számára.  

A tanúsítvány-házirendek magas szinten a következő információkat tartalmazják (a definícióik itt [találhatók):](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)  

-   X509-tanúsítványtulajdonságok: A tulajdonos nevét, a tulajdonos alternatív neveit és az x509-tanúsítványkérelem létrehozásához használt egyéb tulajdonságokat tartalmazza.  
-   Kulcstulajdonságok: kulcstípust, kulcshosszt, exportálható és ReuseKeyOnRenewal mezőket tartalmaz. Ezek a mezők arra utasítják a Key Vaultot, hogy hogyan hozzon létre egy kulcsot. 
     - Támogatott kulcstípusok: RSA, RSA-HSM, EC, EC-HSM, oct (itt [található)](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) 
-   Titkos titok tulajdonságai: olyan titkos tulajdonságokkal rendelkezik, mint például a címezhető titkos adatok tartalomtípusa a titkos érték létrehozásához a tanúsítvány titkos ként való lekérhető lekérelezése érdekében.  
-   Élettartam-műveletek: a KV-tanúsítvány élettartam-műveletét tartalmazza. Minden élettartam-művelet a következő adatokat tartalmazza:  

     - Eseményindító: a lejárat előtti napokon vagy az élettartam százalékos időtartamán keresztül van megadva  

     - Művelet: művelettípus megadása – *e-mailContacts* vagy *autoRenew*  

-   Kiállító: Az x509-tanúsítványok kiállításához használt tanúsítványkiállító paraméterei.  
-   Szabályzatattribútumok: a szabályzathoz társított attribútumokat tartalmaz  

### <a name="x509-to-key-vault-usage-mapping"></a>X509–Key Vault-leképezés

Az alábbi táblázat az x509 kulcshasználati szabályzatnak a tanúsítvány létrehozásakor létrehozott kulcs hatályos kulcsműveleteire való leképezését Key Vault mutatja be.

|**X509 kulcshasználat jelzők**|**Key Vault műveletek**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|Adattűnés|titkosítás, visszafejtés| N/A |
|DecipherOnly (Csak titkosítás)|Visszafejteni| N/A  |
|DigitalSignature (Digitális aláírás)|aláírás, ellenőrzés| Key Vault alapértelmezett beállítás a tanúsítvány létrehozásakor használati specifikáció nélkül | 
|EncipherOnly (Csak titkosítás)|encrypt| N/A |
|KeyCertSign|aláírás, ellenőrzés|N/A|
|KeyEncipherment (Kulcs titkosítása)|wrapKey, unwrapKey| Key Vault alapértelmezett beállítás a tanúsítvány létrehozásakor használati specifikáció nélkül | 
|Nem megtagadás|aláírás, ellenőrzés| N/A |
|crlsign (crlsign)|aláírás, ellenőrzés| N/A |

## <a name="certificate-issuer"></a>Tanúsítványkiállító

A Key Vault tanúsítványobjektum egy olyan konfigurációt használ, amely az x509-tanúsítványok megrendelésére a kiválasztott tanúsítványkiállító szolgáltatóval kommunikál.  

-   Key Vault következő tanúsítványkiállító-szolgáltatókkal partneri kapcsolatot a TLS-/SSL-tanúsítványokhoz

|**Szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|A nyilvános felhőben és a felhőben található összes Key Vault-szolgáltatás Azure Government|
|Globalsign|A nyilvános felhőben és a felhőben található összes Key Vault-szolgáltatás Azure Government|

Ahhoz, hogy egy tanúsítványkiállító létre Key Vault, az 1. és a 2. előfeltételnek megfelelő lépéseket kell végrehajtani.  

1. Hitelesítésszolgáltatói (CA)-szolgáltatók – be- és beiratás  

    -   A vállalati rendszergazdának kell a vállalatot bevetnie (például: Contoso) legalább egy hitelesítésszolgáltatóval.  

2. A rendszergazda létrehozza a kérelmező hitelesítő adatait, Key Vault TLS-/SSL Key Vault tanúsítványok regisztrálására (és megújítására)  

    -   A szolgáltató kiállítói objektumának kulcstartóban való létrehozásához használt konfigurációt biztosítja  

A Kiállító objektumok a Tanúsítványok portálról történő létrehozásával kapcsolatos további információkért tekintse meg a [tanúsítványokról Key Vault blogot.](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

Key Vault lehetővé teszi több kiállító objektum létrehozását különböző kiállító-szolgáltatói konfigurációval. A kiállító objektum létrehozása után a nevére egy vagy több tanúsítvány-házirend hivatkozhat. A kiállító objektumra való hivatkozás arra utasítja a Key Vault, hogy használja a kiállító objektumban megadott konfigurációt, amikor az x509-tanúsítványt a hitelesítésszolgáltatótól kéri le a tanúsítvány létrehozása és megújítása során.  

A kiállító objektumok a tárolóban vannak létrehozva, és csak kv tanúsítványokkal használhatók ugyanabban a tárolóban.  

## <a name="certificate-contacts"></a>Tanúsítvány-kapcsolattartók

A tanúsítvány-kapcsolattartók kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartameseményei által kiváltott értesítések küldése érdekében. A névjegyek adatait a kulcstartóban található összes tanúsítvány megosztja. A rendszer értesítést küld a kulcstartóban található összes tanúsítványhoz egy adott esemény összes megadott kapcsolattartója számára. A tanúsítvány-kapcsolattartó beállításának mikéntjről itt [található információ](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>Tanúsítvány Access Control

 A tanúsítványok hozzáférés-vezérlését a Key Vault kezeli, és az adott tanúsítványokat tartalmazó Key Vault biztosítja. A tanúsítványokra vonatkozó hozzáférés-vezérlési házirend eltér az ugyanabban a tanúsítványban található kulcsok és titkos kulcsok hozzáférés-vezérlési Key Vault. A felhasználók létrehozhatnak egy vagy több tárolót a tanúsítványok kezeléséhez, hogy fenntartsák a forgatókönyvet a tanúsítványok megfelelő szegmentálása és kezelése érdekében.  A tanúsítvány-hozzáférés-vezérléssel kapcsolatos további információkért lásd [itt](certificate-access-control.md)

## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
