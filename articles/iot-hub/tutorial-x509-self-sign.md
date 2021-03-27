---
title: Oktatóanyag – önaláírt tanúsítványokat hozhat létre az Azure IoT Hubhoz az OpenSSL használatával | Microsoft Docs
description: Oktatóanyag – az OpenSSL használata önaláírt X. 509 tanúsítványok létrehozásához az Azure IoT Hub
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
ms.openlocfilehash: 82ef2e39d5d04914e1086e0b25ccbc8e5c7c762e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630675"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Oktatóanyag: az OpenSSL használata önaláírt tanúsítványok létrehozásához

Az eszközt két önaláírt eszköz tanúsítványával hitelesítheti a IoT Hub. Ezt néha ujjlenyomat-hitelesítésnek is nevezik, mivel a tanúsítványok a IoT hubhoz küldött ujjlenyomatai megfelelnek (kivonatoló értékeket) tartalmaznak. A következő lépésekből megtudhatja, hogyan hozhat létre két önaláírt tanúsítványt.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>1. lépés – kulcs létrehozása az első tanúsítványhoz

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>2. lépés – CSR létrehozása az első tanúsítványhoz

Ha a rendszer kéri, győződjön meg róla, hogy megadja az eszköz AZONOSÍTÓját.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>3. lépés – a CSR-fájl megtekintése

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>4. lépés – az önaláírt tanúsítvány 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>5. lépés – kulcs létrehozása a 2. tanúsítványhoz

Ha a rendszer kéri, az 1. tanúsítványhoz használt eszköz AZONOSÍTÓját kell megadnia.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>6. lépés – az önaláírt tanúsítvány 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>7. lépés – az 1. tanúsítvány ujjlenyomatának beolvasása

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>8. lépés – a 2. tanúsítvány ujjlenyomatának beolvasása

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>9. lépés – új IoT-eszköz létrehozása

Navigáljon a IoT Hub a Azure Portalban, és hozzon létre egy új IoT-identitást a következő jellemzőkkel:

* Adja meg a két tanúsítvány tulajdonosának nevével megegyező **eszköz azonosítóját** .
* Válassza ki az **X. 509 önaláírt** hitelesítési típust.
* Illessze be az elsődleges és másodlagos tanúsítványokból másolt hexadecimális karakterlánc ujjlenyomatai megfelelnek. Győződjön meg arról, hogy a hexadecimális sztringek nem rendelkeznek kettőspont elválasztó karakterrel.

## <a name="next-steps"></a>Következő lépések

A [tanúsítvány hitelesítésének tesztelése](tutorial-x509-test-certificate.md) lehetőségre kattintva megállapíthatja, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT hub.
