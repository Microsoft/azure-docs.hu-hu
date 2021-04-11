---
title: Oktatóanyag – az OpenSSL használata X. 509 tesztelési tanúsítványok létrehozásához az Azure IoT Hubhoz | Microsoft Docs
description: Oktatóanyag – HITELESÍTÉSSZOLGÁLTATÓI és eszköz-tanúsítványok létrehozása az OpenSSL használatával az Azure IoT hub-hoz
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 0d083d856138d7895a6e03f4d290ef3c4ddebd05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630720"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Oktatóanyag: az OpenSSL használata tesztelési tanúsítványok létrehozásához

Bár az X. 509 tanúsítványokat megbízható hitelesítésszolgáltatótól is megvásárolhatja, a saját teszt-hierarchia létrehozása vagy az önaláírt tanúsítványok használata megfelelő az IoT hub-eszközök hitelesítésének teszteléséhez. Az alábbi példa az [OpenSSL](https://www.openssl.org/) -t és az [OpenSSL-szakácskönyvet](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) használja a hitelesítésszolgáltató (CA), egy alárendelt hitelesítésszolgáltató és egy eszköz tanúsítványának létrehozásához. A példa ezután aláírja az alárendelt HITELESÍTÉSSZOLGÁLTATÓT és az eszköz tanúsítványát egy tanúsítvány-hierarchiába. Ez csak példaként jelenik meg.

## <a name="step-1---create-the-root-ca-directory-structure"></a>1. lépés – a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ címtár-struktúrájának létrehozása

Hozzon létre egy címtár-struktúrát a hitelesítésszolgáltató számára.

* A **tanúsítványok** könyvtára új tanúsítványokat tárol.
* A rendszer a tanúsítvány-adatbázishoz használja az **db** könyvtárat.
* A **privát** könyvtár TÁROLJA a hitelesítésszolgáltató titkos kulcsát.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>2. lépés – a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ konfigurációs fájljának létrehozása

A HITELESÍTÉSSZOLGÁLTATÓ létrehozása előtt hozzon létre egy konfigurációs fájlt, és mentse azt `rootca.conf` a rootca könyvtárba.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>3. lépés – legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ létrehozása

Először a kulcs és a tanúsítvány-aláírási kérelem (CSR) előállítása a rootca könyvtárban.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Ezután hozzon létre egy önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Az önaláírás megfelelő tesztelési célokra. A parancssorban határozza meg a ca_ext konfigurációs fájl bővítményeit. Ezek azt jelzik, hogy a tanúsítvány egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz kapcsolódik, és használható a tanúsítványok és a visszavont tanúsítványok listáinak (CRL-ek) aláírására. Írja alá a tanúsítványt, és véglegesítse azt az adatbázisba.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>4. lépés – az alárendelt HITELESÍTÉSSZOLGÁLTATÓ címtár-struktúrájának létrehozása

Hozzon létre egy címtár-struktúrát az alárendelt HITELESÍTÉSSZOLGÁLTATÓ számára.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>5. lépés – alárendelt HITELESÍTÉSSZOLGÁLTATÓ konfigurációs fájljának létrehozása

Hozzon létre egy konfigurációs fájlt, és mentse subca. conf néven a `subca` könyvtárba.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>6. lépés – alárendelt HITELESÍTÉSSZOLGÁLTATÓ létrehozása

Hozzon létre egy új sorozatszámot a `rootca/db/serial` fájlban az ALÁRENDELT hitelesítésszolgáltatói tanúsítványhoz.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Létre kell hoznia egy új sorozatszámot minden alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz és minden létrehozott eszköz tanúsítványhoz. A különböző tanúsítványok nem rendelkezhetnek ugyanazzal a sorozatszámmal.

Ebből a példából megtudhatja, hogyan hozhat létre alárendelt vagy regisztrációs HITELESÍTÉSSZOLGÁLTATÓT. Mivel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT a tanúsítványok aláírására használhatja, az alárendelt HITELESÍTÉSSZOLGÁLTATÓ létrehozása nem feltétlenül szükséges. Az alárendelt HITELESÍTÉSSZOLGÁLTATÓkkal azonban olyan valós tanúsítvány-hierarchiákat is utánozhat, amelyekben a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ offline állapotban van, és az alárendelt hitelesítésszolgáltatók kiállítják az Ügyféltanúsítványok tanúsítványait.

A konfigurációs fájl használatával kulcs és tanúsítvány-aláírási kérelem (CSR) hozható elő.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Küldje el a CSR-t a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓNAK, és használja a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT az alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kiállításához és aláírásához. A bővítmények kapcsolójának sub_ca_ext megadása a parancssorban. A bővítmények azt jelzik, hogy a tanúsítvány a tanúsítványok és a visszavont tanúsítványok listáinak (CRL-ek) aláírására képes HITELESÍTÉSSZOLGÁLTATÓ. Amikor a rendszer kéri, írja alá a tanúsítványt, és véglegesítse azt az adatbázisba.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>7. lépés – a birtoklás igazolásának bemutatása

Most már rendelkezik egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal és egy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal is. Az eszköz tanúsítványait bármelyik használatával is aláírhatja. A kiválasztott elemet fel kell tölteni a IoT Hubba. A következő lépések azt feltételezik, hogy az alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja. Az alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése és regisztrálása a IoT Hubba:

1. A Azure Portal navigáljon a IoTHub, és válassza a **beállítások > tanúsítványok** lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget az új alárendelt hitelesítésszolgáltatói tanúsítvány hozzáadásához.

1. Adja meg a megjelenítendő nevet a **tanúsítvány neve** mezőben, és válassza ki a korábban létrehozott PEM-tanúsítványfájl.

1. Kattintson a **Mentés** gombra. A tanúsítvány a tanúsítványok listájában nem **ellenőrzött** állapottal jelenik meg. Az ellenőrzési folyamat igazolni fogja, hogy Ön a tanúsítvány tulajdonosa.

   
1. Válassza ki a tanúsítványt a **tanúsítvány részletei** párbeszédpanel megtekintéséhez.

1. Válassza az **ellenőrző kód előállítása** lehetőséget. További információ: CA- [tanúsítvány birtoklásának bizonyítása](tutorial-x509-prove-possession.md).

1. Másolja az ellenőrzőkódot a vágólapra. A tanúsítvány tulajdonosának kell megadnia az ellenőrző kódot. Ha például az ellenőrző kód BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, adja hozzá a tanúsítványt tulajdonosként a következő lépésben látható módon.

1. Titkos kulcs létrehozása.

  ```bash
    $ openssl req -new -key pop.key -out pop.csr

    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

9. Hozzon létre egy tanúsítványt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ konfigurációs fájljának és a CSR-nek a használatával.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

10. Válassza ki az új tanúsítványt a **tanúsítvány részletei** nézetben.

11. A tanúsítvány feltöltése után válassza az **ellenőrzés** lehetőséget. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány állapotának **ellenőrzött** értékre kell váltania.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>8. lépés – eszköz létrehozása a IoT Hubban

Navigáljon a IoT Hub a Azure Portalban, és hozzon létre egy új IoT-azonosítót a következő értékekkel:

1. Adja meg az eszköz tanúsítványának tulajdonos nevével megegyező **azonosítóját** .

1. Válassza ki az **X. 509 hitelesítésszolgáltatói aláírt** hitelesítési típust.

1. Kattintson a **Mentés** gombra.

## <a name="step-9---create-a-client-device-certificate"></a>9. lépés – ügyfél-eszköz tanúsítványának létrehozása

Ügyféltanúsítvány létrehozásához először egy titkos kulcsot kell előállítania. A következő parancs bemutatja, hogyan használható az OpenSSL egy titkos kulcs létrehozásához. Hozza létre a kulcsot a subca könyvtárban.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Hozzon létre egy tanúsítvány-aláírási kérelmet (CSR) a kulcshoz. Nem kell megadnia a jelszó vagy a választható cég nevét. Az eszköz AZONOSÍTÓját azonban a köznapi név mezőben kell megadnia.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Győződjön meg arról, hogy a CSR a vártnak megfelelően van-e.

```bash
openssl req -text -in device.csr -noout
```

Küldje el a CSR-t az alárendelt HITELESÍTÉSSZOLGÁLTATÓNAK a tanúsítvány-hierarchiába való bejelentkezéshez. Itt adhatja meg `client_ext` a `-extensions` kapcsolót. Figyelje meg, hogy a `Basic Constraints` kiállított tanúsítványban a tanúsítvány nem hitelesítésszolgáltató. Ha több tanúsítványt is aláír, ne felejtse el frissíteni a sorozatszámot, mielőtt az OpenSSL parancs használatával létrehozza az egyes tanúsítványokat `rand -hex 16 > db/serial` .

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Következő lépések

A [tanúsítvány hitelesítésének tesztelése](tutorial-x509-test-certificate.md) lehetőségre kattintva megállapíthatja, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT hub.
