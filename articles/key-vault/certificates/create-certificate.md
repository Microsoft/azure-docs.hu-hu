---
title: Tanúsítvány-létrehozási módszerek
description: Ismerje meg, hogyan hozhat létre vagy importálhat Key Vault-tanúsítványt a Azure Key Vaultban. Több módon is létrehozhat egy Key Vault-tanúsítványt.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: f06f2de1f373f72aa5e55da17c249ff119a36950
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581832"
---
# <a name="certificate-creation-methods"></a>Tanúsítvány-létrehozási módszerek

 Egy Key Vault (KV) tanúsítvány hozható létre vagy importálható egy kulcstartóba. Egy KV-os tanúsítvány létrehozásakor a titkos kulcs a kulcstartón belül jön létre, és soha nem lesz kitéve a tanúsítvány tulajdonosának. A következő módszerekkel hozhat létre tanúsítványt Key Vaultban:  

-   **Önaláírt tanúsítvány létrehozása:** Ezzel létrehoz egy nyilvános titkos kulcspárt, és hozzárendeli egy tanúsítványhoz. A tanúsítványt a saját kulcsa fogja aláírni.  

-    **Hozzon létre egy új tanúsítványt manuálisan:** Ezzel létrehoz egy nyilvános titkos kulcspárt, és létrehoz egy X. 509 tanúsítvány-aláírási kérelmet. Az aláírási kérelmet a regisztrációs hatóság vagy a hitelesítésszolgáltató aláírhatja. Az aláírt x509-tanúsítvány egyesíthető a függőben lévő kulcspár használatával, hogy elvégezze a KV-os tanúsítványt Key Vaultban. Bár ez a módszer több lépést igényel, nagyobb biztonságot nyújt, mivel a titkos kulcsot a-ben hozza létre, és a Key Vaultre korlátozódik. Ezt az alábbi ábra ismerteti.  

![Tanúsítvány létrehozása saját hitelesítésszolgáltatóval](../media/certificate-authority-1.png)  

Az alábbi leírások az előző ábrán látható zöld betűs lépéseknek felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. Key Vault visszaadja az alkalmazásnak a tanúsítvány-aláírási kérelmet (CSR)
3. Az alkalmazás a választott CA-nak adja tovább a CSR-t.
4. A kiválasztott HITELESÍTÉSSZOLGÁLTATÓ egy X509-tanúsítvánnyal válaszol.
5. Az alkalmazás befejezi az új tanúsítvány létrehozását a HITELESÍTÉSSZOLGÁLTATÓ X509-tanúsítványának egyesítésével.

-   **Hozzon létre egy ismert kiállító szolgáltatót tartalmazó tanúsítványt:** Ehhez a metódushoz egyszeri feladatot kell létrehozni egy kiállító objektum létrehozásához. Miután létrehozta a kiállítói objektumot a Key vaultban, a neve a KV-os tanúsítvány házirendjében hivatkozhat rá. Egy ilyen KV-tanúsítvány létrehozásához szükséges kérelem létrehoz egy kulcspárt a tárolóban, és kommunikál a kiállító szolgáltató szolgáltatással a hivatkozott kiállító objektumban található információk használatával egy x509-tanúsítvány beszerzéséhez. A rendszer beolvassa a x509-tanúsítványt a kiállító szolgáltatásból, és egyesíti a kulcspárt a KV-tanúsítvány létrehozásának befejezéséhez.  

![Tanúsítvány létrehozása Key Vault partnerrel rendelkező hitelesítésszolgáltatóval](../media/certificate-authority-2.png)  

Az alábbi leírások az előző ábrán látható zöld betűs lépéseknek felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. Key Vault TLS/SSL-tanúsítványkérelmet küld a HITELESÍTÉSSZOLGÁLTATÓNAK.
3. Az alkalmazás ciklikus lekérdezéseket végezve várja meg, hogy a kulcstartó elkészítse a tanúsítványt. A tanúsítvány létrehozása akkor ér véget, amikor a kulcstartó megkapja a CA válaszát az X.509-tanúsítvánnyal.
4. A CA válaszol a TLS/SSL X. 509 tanúsítvánnyal rendelkező TLS/SSL-tanúsítványkérelem Key Vault.
5. Az új tanúsítvány létrehozása a HITELESÍTÉSSZOLGÁLTATÓ TLS/SSL X. 509 tanúsítványának egyesítésével fejeződik be.

## <a name="asynchronous-process"></a>Aszinkron folyamat
A KV-tanúsítvány létrehozása egy aszinkron folyamat. A művelet egy KV-os tanúsítványkérelmet hoz létre, és egy 202-es HTTP-állapotkódot ad vissza (elfogadva). A kérelem állapota nyomon követhető a művelet által létrehozott függő objektum lekérdezésével. A függő objektum teljes URI-ja a LOCATION (hely) fejlécben lesz visszaadva.  

Ha egy KV-tanúsítvány létrehozására vonatkozó kérelem befejeződik, a függőben lévő objektum állapota a "befejezve" értékre változik, és a rendszer létrehozza a KV-tanúsítvány új verzióját. Ez lesz a jelenlegi verzió.  

## <a name="first-creation"></a>Első létrehozás
 Ha első alkalommal hoz létre egy KV-os tanúsítványt, a rendszer a tanúsítvány nevével megegyező névvel létrehoz egy címezhető kulcsot és titkos kulcsot is. Ha a név már használatban van, a művelet sikertelen lesz, és a http-állapotkód 409 (ütközés).
A megcímezhető kulcs és titkos kód az attribútumokat a KV tanúsítvány attribútumaiból kapja meg. Az ily módon létrehozott címezhető kulcs és titkos kód felügyelt kulcsoknak és titoknak van megjelölve, amelynek élettartamát Key Vault kezeli. A felügyelt kulcsok és titkos kódok csak olvashatók. Megjegyzés: Ha egy KV-es tanúsítvány lejár vagy le van tiltva, akkor a megfelelő kulcs és titok működésképtelenné válik.  

 Ha ez az első művelet egy KV-os tanúsítvány létrehozásához, akkor szükség van egy házirendre.  Egy házirendet a házirend-erőforrás cseréjére szolgáló, egymást követő létrehozási műveletekkel is meg lehet adni. Ha nem adja meg a szabályzatot, a rendszer a szolgáltatáshoz tartozó házirend-erőforrást használja a KV-os tanúsítvány következő verziójának létrehozásához. Vegye figyelembe, hogy míg a következő verzió létrehozásához szükséges kérelem folyamatban van, a jelenlegi KV-tanúsítvány és a hozzá tartozó címezhető kulcs és titkos kód változatlan marad.  

## <a name="self-issued-certificate"></a>Önmagát kiállított tanúsítvány
 Önkiszolgáló tanúsítvány létrehozásához állítsa a kiállító nevét "saját" értékre a tanúsítvány-házirendben, ahogy az a tanúsítvány-házirendben szereplő következő kódrészletben látható.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Ha nincs megadva a kiállító neve, akkor a kiállító neve "Unknown" (ismeretlen) értékre van állítva. Ha a kiállító "ismeretlen", a tanúsítvány tulajdonosának manuálisan be kell szereznie egy x509-tanúsítványt a választott kibocsátótól, majd egyesíteni kell a nyilvános x509-tanúsítványt a Key Vault-tanúsítvánnyal függő objektummal a tanúsítvány létrehozásának befejezéséhez.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partneri HITELESÍTÉSSZOLGÁLTATÓI szolgáltatók
A tanúsítvány létrehozása manuálisan vagy "saját" kibocsátó használatával végezhető el. Key Vault az egyes kiállító szolgáltatókhoz tartozó partnereket is, hogy leegyszerűsítsék a tanúsítványok létrehozását. A Key Vault a következő típusú tanúsítványokat rendelheti ezekhez a partner kiállító szolgáltatókhoz.  

|Szolgáltató|Tanúsítvány típusa|Konfiguráció beállítása  
|--------------|----------------------|------------------|  
|DigiCert|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a DigiCert| [Integrációs útmutató](./how-to-integrate-certificate-authority.md)
|GlobalSign|A Key Vault OV vagy EV SSL-tanúsítványokat kínál a GlobalSign| [Integrációs útmutató](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 A tanúsítvány kiállítója Azure Key Vault (KV) CertificateIssuer erőforrásként jelölt entitás. A rendszer a KV-tanúsítvány forrására vonatkozó információk megadására szolgál. kiállító neve, szolgáltatója, hitelesítő adatai és egyéb rendszergazdai részletek.

Vegye figyelembe, hogy ha rendelést helyez el a kiállító szolgáltatóval, az a tanúsítvány típusától függően megbecsülheti vagy felülbírálhatja a x509 tanúsítvány-kiterjesztéseit és a tanúsítvány érvényességi időtartamát.  

 Engedélyezés: a tanúsítványok/létrehozási engedély szükséges.

## <a name="see-also"></a>Lásd még:

 - Útmutató tanúsítványok létrehozásához Key Vault a [portál](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal), az [Azure CLI](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli), az [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell) használatával
 - [Tanúsítvány-létrehozás monitorozása és kezelése](create-certificate-scenarios.md)
