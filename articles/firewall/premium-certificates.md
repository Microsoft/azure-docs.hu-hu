---
title: Prémium előzetes verziójú tanúsítványok Azure Firewall
description: A TLS-ellenőrzésnek a Azure Firewall Premium Preview-on való megfelelő konfigurálásához konfigurálnia és telepítenie kell a köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 31948d5e98ea3024c838bf0fa4b05609a5662ec5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485520"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Prémium előzetes verziójú tanúsítványok Azure Firewall 

> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview TLS-vizsgálat megfelelő konfigurálásához meg kell adnia egy érvényes közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, és az Azure Key vaultban kell befizetnie.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Azure Firewall Premium Preview által használt tanúsítványok

Egy tipikus központi telepítésben három típusú tanúsítvány használatos:

- **Köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány)**

   A hitelesítésszolgáltatók (CA-k) olyan szervezetek, amelyek megbízhatók a digitális tanúsítványok aláírásához. A HITELESÍTÉSSZOLGÁLTATÓ ellenőrzi a vállalat vagy a tanúsítványt kérő személy identitását és törvényességét. Ha az ellenőrzés sikeres, a HITELESÍTÉSSZOLGÁLTATÓ aláírt tanúsítványt bocsát ki. Amikor a kiszolgáló megadja a tanúsítványt az ügyfélnek (például a webböngészőnek) az SSL/TLS-kézfogás során, az ügyfél megkísérli ellenőrizni az aláírást az *ismert jó* aláírók listájával. A webböngészők általában olyan hitelesítésszolgáltatók listájával rendelkeznek, amelyek implicit módon bíznak a gazdagépek azonosításában. Ha a szolgáltató nem szerepel a listában, akárcsak a saját tanúsítványait aláíró webhelyek esetében, a böngésző figyelmezteti a felhasználót, hogy a tanúsítványt nem egy elismert hatóság írta alá, és kéri a felhasználót, hogy folytassa a nem ellenőrzött hellyel való kommunikációt.

- **Kiszolgálótanúsítvány (webhely-tanúsítvány)**

   Adott tartománynévhez társított tanúsítvány. Ha egy webhely rendelkezik érvényes tanúsítvánnyal, az azt jelenti, hogy a hitelesítésszolgáltató lépéseket tett annak ellenőrzéséhez, hogy a webcíme ténylegesen a szervezethez tartozik-e. Amikor begépel egy URL-címet, vagy egy biztonságos webhelyre mutató hivatkozást követ, a böngésző a következő jellemzőkkel ellenőrzi a tanúsítványt:
   - A webhely címe megegyezik a tanúsítványhoz tartozó címtől.
   - A tanúsítványt egy hitelesítésszolgáltató írja alá, hogy a böngésző *megbízható* szolgáltatóként ismeri fel.
   
   Alkalmanként előfordulhat, hogy a felhasználók nem megbízható tanúsítvánnyal rendelkező kiszolgálóhoz csatlakoznak. Azure Firewall el fogja dobni a kapcsolatokat úgy, mintha a kiszolgáló megszakította a kapcsolatokat.

- **Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (főtanúsítvány)**

   A hitelesítésszolgáltató több tanúsítványt is kiadhat egy fastruktúra formájában. A főtanúsítvány a fa legfelső szintű tanúsítványa.

Azure Firewall Premium Preview képes a kimenő HTTP/S forgalom elfogására és a kiszolgálói tanúsítvány automatikus előállítására `www.website.com` . Ez a tanúsítvány az Ön által megadott köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával jön létre. A végfelhasználói böngészőnek és az ügyfélalkalmazásnak megbízhatónak kell lennie a szervezet legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának vagy köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának az eljárás működéséhez. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Tanúsítvány feldolgozása":::

## <a name="intermediate-ca-certificate-requirements"></a>A köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra vonatkozó követelmények

Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megfelel a következő követelményeknek:

- Key Vault titokként való üzembe helyezéskor a jelszó nélküli PFX (Pkcs12/pfx-profil) tanúsítványt és titkos kulcsot kell használnia.

- Egyetlen tanúsítványnak kell lennie, és nem szabad a tanúsítványok teljes láncát tartalmaznia.  

- A kérelemnek egy évig előre kell lennie.  

- Legalább 4096 bájt méretű RSA titkos kulcsnak kell lennie.  

- A `KeyUsage` `KeyCertSign` jelölőnek a jelzővel (RFC 5280; 4.2.1.3 Key-használattal) kritikusnak kell lennie.

- A `BasicContraints` bővítménynek kritikus jelöléssel (RFC 5280; 4.2.1.9 alapvető megkötésekkel) kell rendelkeznie.  

- A `CA` jelzőt True értékre kell állítani.

- Az elérési út hosszának eggyel nagyobbnak vagy azzal egyenlőnek kell lennie.

## <a name="azure-key-vault"></a>Azure Key Vault

A [Azure Key Vault](../key-vault/general/overview.md) egy platform által felügyelt titkos tároló, amely a titkok, a kulcsok és a TLS/SSL-tanúsítványok védelmére használható. A Azure Firewall Premium támogatja a tűzfal-házirendhez csatolt kiszolgálói tanúsítványok Key Vault-integrációját.
 
A Key Vault konfigurálása:

- A Key vaultba egy meglévő tanúsítványt kell importálnia. 
- Azt is megteheti, hogy egy Key Vault-titkot is használ, amely jelszó nélküli, Base-64 kódolású PFX-fájlként van tárolva.  A PFX-fájlok egy titkos kulcsot és egy nyilvános kulcsot tartalmazó digitális tanúsítvány.
- A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány importálását ajánlott használni, mert lehetővé teszi, hogy a tanúsítvány lejárati dátuma alapján konfiguráljon riasztást.
- Miután importált egy tanúsítványt vagy titkos kulcsot, meg kell határoznia a Key Vault hozzáférési szabályzatait, hogy lehetővé váljon a tanúsítvány/titok hozzáférésének engedélyezése az identitás számára.
- Az Azure-beli számítási feladatnak megbízhatónak kell lennie a megadott HITELESÍTÉSSZOLGÁLTATÓI tanúsítványon. Győződjön meg arról, hogy megfelelően vannak-e telepítve.

Létrehozhat vagy felhasználhat egy meglévő, felhasználó által hozzárendelt felügyelt identitást, amelyet a Azure Firewall a tanúsítványok lekéréséhez a Key Vault az Ön nevében. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Tanúsítvány konfigurálása a szabályzatban

Ha HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt szeretne konfigurálni a tűzfal prémium házirendjében, válassza ki a szabályzatot, majd válassza a **TLS-vizsgálat (előzetes verzió)** lehetőséget. Válassza az **engedélyezve** lehetőséget a **TLS-ellenőrzési** oldalon. Ezután válassza ki a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt Azure Key Vaultban, ahogy az a következő ábrán látható:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure Firewall Premium – áttekintés diagram":::
 
> [!IMPORTANT]
> Ha szeretné megtekinteni és konfigurálni a tanúsítványokat a Azure Portalból, fel kell vennie az Azure-beli felhasználói fiókját az Key Vault hozzáférési szabályzatba. Adja meg a felhasználói **fiókját** , és **sorolja** fel a **titkos engedélyeket**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Hozzáférési szabályzat Azure Key Vault":::


## <a name="create-your-own-self-signed-ca-certificate"></a>Saját önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

A TLS-ellenőrzés teszteléséhez és ellenőrzéséhez használja a következő parancsfájlokat a saját önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ és a köztes HITELESÍTÉSSZOLGÁLTATÓ létrehozásához.

> [!IMPORTANT]
> Éles környezetben a vállalati PKI-t kell használnia egy köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához. A vállalati PKI kihasználja a meglévő infrastruktúrát, és kezeli a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI eloszlást az összes végponti gépen.

A szkript két verziója létezik:
- egy bash-szkript `cert.sh` 
- egy PowerShell-parancsfájl `cert.ps1` 

 Emellett mindkét parancsfájl a `openssl.cnf` konfigurációs fájlt használja. A parancsfájlok használatához másolja a ( `openssl.cnf` `cert.sh` vagy) tartalmát a `cert.ps1` helyi számítógépre.

A parancsfájlok a következő fájlokat eredményezik:
- rootCA. CRT/rootCA. Key – legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa és titkos kulcsa.
- interCA. CRT/interCA. Key – közbenső szintű HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa és titkos kulcsa
- interCA. pfx – köztes HITELESÍTÉSSZOLGÁLTATÓI PKCS12/pfx-profil-csomag, amelyet a tűzfal használni fog

> [!IMPORTANT]
> a rootCA. Key tárolót biztonságos offline helyen kell tárolni. A parancsfájlok 1024 napos érvényességű tanúsítványt állítanak elő.

A tanúsítványok létrehozása után telepítse azokat a következő helyszínekre:
- rootCA. CRT – üzembe helyezés Endpoint Machines gépeken (csak nyilvános tanúsítvány).
- interCA. pfx – importálás tanúsítványként egy Key Vault, és hozzárendelés a tűzfal házirendjéhez.

### <a name="opensslcnf"></a>**OpenSSL. cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash-szkript – cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell – cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány érvényes, de a TLS-vizsgálat alatt nem fér hozzá a teljes tartománynevek vagy URL-címekhez, ellenőrizze a következő elemeket:

- Győződjön meg arról, hogy a webkiszolgáló-tanúsítvány érvényes.  

- Győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítve van az ügyfél operációs rendszerén.  

- Győződjön meg arról, hogy a böngésző vagy a HTTPS-ügyfél tartalmaz egy érvényes főtanúsítványt. A Firefox és más böngészők speciális minősítési házirendekkel rendelkezhetnek.  

- Győződjön meg arról, hogy az alkalmazási szabályban szereplő URL-célcím tartalmazza a helyes elérési utat és a célként megadott HTML-lapon beágyazott összes hivatkozást. Használhat helyettesítő karaktereket is a teljes szükséges URL-cím elérési útjának egyszerű biztosításához.  


## <a name="next-steps"></a>Következő lépések

- [További információ a Azure Firewall Premium funkcióival kapcsolatban](premium-features.md)
