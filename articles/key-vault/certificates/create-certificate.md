---
title: Tanúsítvány-létrehozási módszerek
description: Ismerje meg a tanúsítvány létrehozásához vagy importáláshoz Key Vault lehetőségeket a Azure Key Vault. Több módon is létrehozható Key Vault tanúsítvány.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752119"
---
# <a name="certificate-creation-methods"></a>Tanúsítvány-létrehozási módszerek

 Egy Key Vault (KV) tanúsítványt lehet létrehozni, vagy importálni egy kulcstartóba. KV-tanúsítvány létrehozásakor a titkos kulcs a kulcstartón belül jön létre, és soha nem lesz elérhető a tanúsítvány tulajdonosa számára. A következő módokon hozhat létre tanúsítványt a Key Vault:  

-   **Önaírt tanúsítvány létrehozása:** Ezzel létrehoz egy nyilvános-titkos kulcspárt, és társítja azt egy tanúsítvánnyal. A tanúsítványt a saját kulcsa fogja aláírni.  

-    **Hozzon létre manuálisan egy új tanúsítványt:** Ez létrehoz egy nyilvános-titkos kulcspárt és egy X.509-tanúsítvány-aláírási kérelmet. Az aláírási kérelmet a regisztrációszolgáltató vagy a hitelesítésszolgáltató aláírhatja. Az aláírt x509-tanúsítvány egyesíthető a függőben lévő kulcspárokkal a KV-tanúsítvány befejezéséhez a Key Vault. Bár ez a módszer több lépést igényel, nagyobb biztonságot nyújt, mivel a titkos kulcs a -ban jön létre, és csak az Key Vault. Ezt az alábbi diagramon ismertetjük.  

![Tanúsítvány létrehozása saját hitelesítésszolgáltatóval](../media/certificate-authority-1.png)  

Az alábbi leírások az előző diagram zöld betűvel írt lépéseit felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. Key Vault tanúsítvány-aláírási kérelmet (CSR) ad vissza az alkalmazásnak
3. Az alkalmazás a választott CA-nak adja tovább a CSR-t.
4. A kiválasztott hitelesítésszolgáltató X509-tanúsítvánnyal válaszol.
5. Az alkalmazás a CA X509-tanúsítványának összevonásával befejezi az új tanúsítvány létrehozását.

-   **Tanúsítvány létrehozása egy ismert kiállítószolgáltatóval:** Ehhez a módszerhez egyszer kell létrehoznia egy kiállító objektumot. Miután létrehozott egy kiállító objektumot a kulcstartóban, annak nevére hivatkozhat a KV-tanúsítvány szabályzatában. Egy ilyen KV-tanúsítvány létrehozására vonatkozó kérelem létrehoz egy kulcspárt a tárolóban, és kommunikál a kiállító szolgáltatójával a hivatkozott kiállító objektumban található információk alapján egy x509-tanúsítvány lekérése érdekében. Az x509-tanúsítvány a kiállítói szolgáltatásból lesz lekérve, és egyesül a kulcspárokkal a KV-tanúsítvány létrehozásának befejezéséhez.  

![Tanúsítvány létrehozása egy Key Vault hitelesítésszolgáltatóval](../media/certificate-authority-2.png)  

Az alábbi leírások az előző diagram zöld betűvel írt lépéseit felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. Key Vault TLS/SSL-tanúsítványkérelmet küld a hitelesítésszolgáltatónak.
3. Az alkalmazás ciklikus lekérdezéseket végezve várja meg, hogy a kulcstartó elkészítse a tanúsítványt. A tanúsítvány létrehozása akkor ér véget, amikor a kulcstartó megkapja a CA válaszát az X.509-tanúsítvánnyal.
4. A hitelesítésszolgáltató TLS/SSL X.509 Key Vault TLS/SSL-tanúsítványkérelemre válaszol.
5. Az új tanúsítvány létrehozása a ca TLS/SSL X.509 tanúsítványának összevonásával fejeződik be.

## <a name="asynchronous-process"></a>Aszinkron folyamat
A KV-tanúsítvány létrehozása aszinkron folyamat. Ez a művelet létrehoz egy KV-tanúsítványkérelmet, és visszaadja a 202 (Elfogadva) HTTP-állapotkódot. A kérés állapota a művelet által létrehozott függőben lévő objektum lekérdezése alapján követhető nyomon. A függőben lévő objektum teljes URI-ját a LOCATION fejléc tartalmazza.  

Amikor befejeződik a KV-tanúsítvány létrehozására vonatkozó kérés, a függőben lévő objektum állapota "completed" (kész) állapotra változik a folyamatban lévő állapotról, és létrejön a KV-tanúsítvány új verziója. Ez lesz az aktuális verzió.  

## <a name="first-creation"></a>Első létrehozás
 A KV-tanúsítvány első létrehozásakor egy címezhető kulcs és egy titkos kulcs is létrejön a tanúsítvány nevével azonos néven. Ha a név már használatban van, a művelet 409-es HTTP-állapotkóddal meghiúsul (ütközés).
A címezhető kulcs és a titkos kulcs az attribútumokat a KV-tanúsítványattribútumokból szerezze be. Az így létrehozott címezhető kulcs és titkos kulcs felügyelt kulcsként és titkos kulcsként van megjelölve, amelynek élettartamát a Key Vault. A felügyelt kulcsok és titkos kulcsok csak olvashatók. Megjegyzés: Ha egy KV-tanúsítvány lejár vagy le van tiltva, a megfelelő kulcs és titkos kulcs működésképtelenné válik.  

 Ha ez az első KV-tanúsítvány létrehozására vonatkozó művelet, akkor házirendre van szükség.  A szabályzatok egymást követő létrehozási műveletekkel is elláthatóak, amelyek lecserélik a szabályzat-erőforrást. Ha nem ad meg házirendet, akkor a rendszer a szolgáltatás házirenderőforrását használja a KV-tanúsítvány következő verziójának létrehozásához. Vegye figyelembe, hogy amíg a következő verzió létrehozására vonatkozó kérelem folyamatban van, az aktuális KV-tanúsítvány, valamint a hozzá tartozó címezhető kulcs és titkos kulcs változatlan marad.  

## <a name="self-issued-certificate"></a>Önkibocsátott tanúsítvány
 Önkibocsátott tanúsítvány létrehozásához állítsa a kiállító nevét "Ön" névre a tanúsítvány-házirendben, ahogy az a tanúsítvány-házirend következő kódrészletében látható.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Ha a kiállító neve nincs megadva, akkor a kiállító neve "Ismeretlen" lesz. Ha a kiállító "Ismeretlen", a tanúsítvány tulajdonosának manuálisan be kell szereznie egy x509-tanúsítványt a választott kiállítótól, majd egyesítenie kell a nyilvános x509-tanúsítványt a kulcstartó függőben lévő tanúsítványobjektumával a tanúsítvány létrehozásának befejezéséhez.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partner ca szolgáltatók
A tanúsítvány létrehozása manuálisan vagy egy "önálló" kiállító használatával is befejezhető. Key Vault egyes kiállító-szolgáltatókkal is partneri kapcsolattal rendelkezik a tanúsítványok létrehozásának egyszerűsítése érdekében. Az alábbi típusú tanúsítványok rendelhetők a partnerkibocsátók szolgáltatóitól a kulcstartóhoz.  

|Szolgáltató|Tanúsítvány típusa|Konfiguráció beállítása  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault OV- vagy EV SSL-tanúsítványokat kínál a DigiCert használatával| [Integrációs útmutató](./how-to-integrate-certificate-authority.md)
|Globalsign|Key Vault OV- vagy EV SSL-tanúsítványokat kínál a GlobalSign használatával| [Integrációs útmutató](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 A tanúsítványkibocsátó egy olyan entitás, Azure Key Vault (KV) certificateIssuer erőforrásként van ábrázolva. A KV-tanúsítvány forrásának információit adja meg; a kiállító neve, a szolgáltató, a hitelesítő adatok és egyéb felügyeleti adatok.

Vegye figyelembe, hogy amikor egy rendelést a kiállító szolgáltatónál helyeznek el, az a tanúsítvány típusa alapján figyelembe veszi vagy felülírhatja az x509-tanúsítványbővítményeket és a tanúsítvány érvényességi időszakát.  

 Engedélyezés: A tanúsítványokra/létrehozásra vonatkozó engedélyt igényel.

## <a name="see-also"></a>Lásd még:

 - Útmutató tanúsítványok létrehozásához a Key Vault [portál,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) [az Azure CLI](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli)és a [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Tanúsítvány-létrehozás monitorozása és kezelése](create-certificate-scenarios.md)
