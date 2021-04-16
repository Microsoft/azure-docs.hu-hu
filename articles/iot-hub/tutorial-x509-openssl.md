---
title: Oktatóanyag – X.509 teszttanúsítványok létrehozása az OpenSSL használatával Azure IoT Hub| Microsoft Docs
description: Oktatóanyag – Hitelesítésszolgáltatói és eszköztanúsítványok létrehozása az Azure IoT Hubhoz OpenSSL használatával
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
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378295"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Oktatóanyag: Teszttanúsítványok létrehozása az OpenSSL használatával

Bár X.509-tanúsítványokat megbízható hitelesítésszolgáltatótól vásárolhat, saját teszttanúsítvány-hierarchiát kell létrehoznia, vagy önaírt tanúsítványokat kell használnia az IoT Hub-eszközhitelesítés teszteléséhez. Az alábbi példa [OpenSSL](https://www.openssl.org/) és [OpenSSL-kézikönyv](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) segítségével hoz létre hitelesítésszolgáltatót (CA), alárendelt CA-t és eszköztanúsítványt. A példa ezután aláírja az alárendelt CA-t és az eszköz tanúsítványát egy tanúsítványhierarchiába. Ez csak példaként szolgál.

## <a name="step-1---create-the-root-ca-directory-structure"></a>1. lépés – A legfelső szintű hitelesítésszolgáltató címtárstruktúrája létrehozása

Hozzon létre egy címtárstruktúrát a hitelesítésszolgáltató számára.

* A **tanúsítványkönyvtár új** tanúsítványokat tárol.
* A **rendszer a db** könyvtárat használja a tanúsítvány-adatbázishoz.
* A **privát** címtár tárolja a CA titkos kulcsát.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>2. lépés – Legfelső szintű hitelesítésszolgáltató konfigurációs fájljának létrehozása

Hitelesítésszolgáltató létrehozása előtt hozzon létre egy konfigurációs fájlt, és mentse a `rootca.conf` rootca könyvtárba.

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

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>3. lépés – Legfelső szintű hitelesítésszolgáltató létrehozása

Először hozza létre a kulcsot és a tanúsítvány-aláírási kérelmet (CSR) a rootca könyvtárban.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Ezután hozzon létre egy önaírt hitelesítésszolgáltatói tanúsítványt. Az önalá aláírás tesztelési célokra alkalmas. Adja ca_ext konfigurációs fájlkiterjesztéseket a parancssorban. Ezek azt jelzik, hogy a tanúsítvány egy legfelső szintű hitelesítésszolgáltatóhoz szükséges, és tanúsítványok és visszavont tanúsítványok listái (CRL-ek) aláírására használható. Írja alá a tanúsítványt, és véglegesítse az adatbázisban.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>4. lépés – Az alárendelt CA könyvtárszerkezetének létrehozása

Hozzon létre egy könyvtárstruktúrát az alárendelt CA számára.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>5. lépés – Alárendelt CA konfigurációs fájl létrehozása

Hozzon létre egy konfigurációs fájlt, és mentse subca.conf fájlként a `subca` könyvtárba.

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

## <a name="step-6---create-a-subordinate-ca"></a>6. lépés – Alárendelt CA létrehozása

Hozzon létre egy új sorozatszámot az alárendelt `rootca/db/serial` CA-tanúsítvány fájljában.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Létre kell hoznia egy új sorozatszámot minden alárendelt CA-tanúsítványhoz és minden ön által létrehozott eszköz tanúsítványhoz. A különböző tanúsítványok sorozatszáma nem lehet azonos.

Ez a példa bemutatja, hogyan hozhat létre alárendelt vagy regisztrációs hitelesítésszolgáltatót. Mivel a legfelső szintű hitelesítésszolgáltatóval aláírhatja a tanúsítványokat, nem feltétlenül szükséges alárendelt CA-t létrehozni. Az alárendelt CA azonban a valós tanúsítványhierarchiákat utánozza, amelyekben a legfelső szintű hitelesítésszolgáltató offline állapotban van, az alárendelt CA pedig ügyféltanúsítványokat ad ki.

A konfigurációs fájl használatával hozzon létre egy kulcsot és egy tanúsítvány-aláírási kérelmet (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Küldje el a CSR-t a legfelső szintű hitelesítésszolgáltatónak, és használja a legfelső szintű hitelesítésszolgáltatót az alárendelt CA-tanúsítvány kiállításához és aláírásához. Adja sub_ca_ext a parancssorban a bővítmények kapcsolóját. A bővítmények azt jelzik, hogy a tanúsítvány olyan hitelesítésszolgáltatóhoz való, amely képes tanúsítványokat és visszavont tanúsítványok listáit (CRL-eket) aláírni. Amikor a rendszer kéri, írja alá a tanúsítványt, és véglegesítse azt az adatbázisban.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>7. lépés – A birtoklási igazolás szemléltető lépése

Most már rendelkezik egy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal és egy alárendelt CA-tanúsítvánnyal is. Az eszköztanúsítványok aláírására bármelyiket használhatja. A választott fájlt fel kell tölteni a IoT Hub. A következő lépések feltételezik, hogy az alárendelt CA-tanúsítványt használja. Az alárendelt CA-tanúsítvány feltöltése és regisztrálása a IoT Hub:

1. A Azure Portal lépjen az IoTHubra, és válassza **a Beállítások és tanúsítványok > lehetőséget.**

1. Az **új alárendelt CA-tanúsítvány** hozzáadásához válassza a Hozzáadás lehetőséget.

1. Adjon meg egy megjelenítendő nevet a **Tanúsítvány neve mezőben,** és válassza ki a korábban létrehozott PEM-tanúsítványfájlt.

1. Kattintson a **Mentés** gombra. A tanúsítvány nem ellenőrzött állapotúként jelenik meg a **tanúsítványlistában.** Az ellenőrzési folyamat igazolja, hogy Ön a tanúsítvány ön tulajdona.

   
1. Válassza ki a tanúsítványt a Tanúsítvány **részletei párbeszédpanel megtekintéséhez.**

1. Válassza **az Ellenőrző kód létrehozása lehetőséget.** További információ: [Hitelesítésszolgáltatói tanúsítvány birtoklásának bizonyítása.](tutorial-x509-prove-possession.md)

1. Másolja az ellenőrzőkódot a vágólapra. Az ellenőrző kódot a tanúsítvány tulajdonosának kell beállítania. Ha például az ellenőrző kód AZ ÖNA656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, adja hozzá a tanúsítványhoz a 9. lépésben látható módon.

1. Hozzon létre egy titkos kulcsot.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Hozzon létre egy tanúsítvány-aláírási kérelmet (CSR) a titkos kulcsból. Adja hozzá az ellenőrző kódot a tanúsítvány tárgyaként.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
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

10. Hozzon létre egy tanúsítványt a legfelső szintű hitelesítésszolgáltató konfigurációs fájljának és a birtoklási igazoláshoz használt CSR-nek a használatával.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Válassza ki az új tanúsítványt a **Tanúsítvány részletei nézetben.** A PEM-fájl megkeresése a certs mappában található.

12. A tanúsítvány feltöltése után válassza az Ellenőrzés **lehetőséget.** A hitelesítésszolgáltatói tanúsítvány állapotának Ellenőrzött **állapotra kell változnia.**

## <a name="step-8---create-a-device-in-your-iot-hub"></a>8. lépés – Eszköz létrehozása a IoT Hub

Lépjen a IoT Hub a Azure Portal, és hozzon létre egy új IoT-eszközidentitást a következő értékekkel:

1. Adja meg **az eszköztanúsítványok** tulajdonosnevének megfelelő eszközazonosítót.

1. Válassza ki az **X.509 hitelesítésszolgáltató aláírt hitelesítési** típusát.

1. Kattintson a **Mentés** gombra.

## <a name="step-9---create-a-client-device-certificate"></a>9. lépés – Ügyféleszköz-tanúsítvány létrehozása

Ügyfél-tanúsítvány létrehozásához először létre kell hoznia egy titkos kulcsot. A következő parancs bemutatja, hogyan hozhat létre titkos kulcsot az OpenSSL használatával. Hozza létre a kulcsot az alca könyvtárban.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Hozzon létre egy tanúsítvány-aláírási kérelmet (CSR) a kulcshoz. Nem kell megadnia a kérdéshez szükséges jelszót vagy a vállalat nevét ( nem kötelező). A köznév mezőben azonban meg kell adnia az eszközazonosítót.

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

Ellenőrizze, hogy a CSR-t a vártnak megfelelő-e.

```bash
openssl req -text -in device.csr -noout
```

Küldje el a CSR-t az alárendelt CA-nak a tanúsítványhierarchiába való bejelentkezéshez. A `client_ext` kapcsolóban adja `-extensions` meg a következőt: . Figyelje meg, hogy a kiállított tanúsítványban a azt jelzi, hogy ez a tanúsítvány `Basic Constraints` nem hitelesítésszolgáltatóhoz való. Ha több tanúsítványt ír alá, az openssl paranccsal frissítse a sorozatszámot az egyes tanúsítványok létrehozása `rand -hex 16 > db/serial` előtt.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Következő lépések

A [Tanúsítványhitelesítés tesztelése alatt](tutorial-x509-test-certificate.md) állapítsa meg, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT Hub.
