---
title: Oktatóanyag – Az OpenSSL használata önaírt tanúsítványok létrehozásához Azure IoT Hub | Microsoft Docs
description: Oktatóanyag – Az OpenSSL használatával önaírt X.509-tanúsítványokat hozhat létre a Azure IoT Hub
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378193"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Oktatóanyag: Az OpenSSL használata önaírt tanúsítványok létrehozásához

Az eszközt hitelesítheti a IoT Hub két önaírt eszköztanúsítvánnyal. Ezt ujjlenyomat-hitelesítésnek is nevezik, mert a tanúsítványok az IoT Hubnak elküldt ujjlenyomatokat (kivonatértékeket) tartalmaznak. A következő lépésekből két önaírt tanúsítványt hozhat létre.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>1. lépés – Kulcs létrehozása az első tanúsítványhoz

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>2. lépés – CSR létrehozása az első tanúsítványhoz

Amikor a rendszer kéri, adja meg az eszközazonosítót.

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

## <a name="step-3---check-the-csr"></a>3. lépés – A CSR ellenőrzése

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>4. lépés – 1. öna aláírási tanúsítvány

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>5. lépés – Kulcs létrehozása a 2. tanúsítványhoz

Amikor a rendszer kéri, adja meg az 1. tanúsítványhoz használt eszközazonosítót.

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

## <a name="step-6---self-sign-certificate-2"></a>6. lépés – Öna sign certificate 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>7. lépés – Az 1. tanúsítvány ujjlenyomatának lekérése

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>8. lépés – A 2. tanúsítvány ujjlenyomatának lekérése

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>9. lépés – Új IoT-eszköz létrehozása

Lépjen a IoT Hub a Azure Portal, és hozzon létre egy új IoT-eszközidentitást a következő jellemzőkkel:

* Adja meg **a két** tanúsítvány tulajdonosnevének megfelelő eszközazonosítót.
* Válassza az **X.509 önaírt hitelesítés** típusát.
* Illessze be az eszköz elsődleges és másodlagos tanúsítványaiból kimásott hexxiális sztring-ujjlenyomatokat. Győződjön meg arról, hogy a hexxánkénti sztringek nem kettőspontelválasztóval vannak elválasztva.

## <a name="next-steps"></a>Következő lépések

A [Tanúsítványhitelesítés tesztelése alatt](tutorial-x509-test-certificate.md) állapítsa meg, hogy a tanúsítvány hitelesítheti-e az eszközt a IoT Hub.
