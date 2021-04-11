---
title: Oktatóanyag – HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok tulajdonjogának igazolása az Azure IoT Hubban | Microsoft Docs
description: Oktatóanyag – igazolja, hogy rendelkezik egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal az Azure IoT Hub
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
ms.openlocfilehash: 0eb91754c3c70a7b477d456158454f707a874207
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630717"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Oktatóanyag: HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány birtoklásának igazolása

Miután feltöltötte a legfelső szintű hitelesítésszolgáltató (CA) tanúsítványát vagy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az IoT hubhoz, igazolnia kell, hogy a tanúsítvány tulajdonosa:

1. A Azure Portal navigáljon a IoTHub, és válassza a **beállítások > tanúsítványok** lehetőséget.

2. Új HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

3. Adja meg a megjelenítendő nevet a **tanúsítvány neve** mezőben, majd válassza ki a hozzáadni kívánt PEM-tanúsítványt.

4. Kattintson a **Mentés** gombra. A tanúsítvány a tanúsítványok listájában nem **ellenőrzött** állapottal jelenik meg. Ez az ellenőrzési folyamat igazolni fogja, hogy rendelkezik a tanúsítvány birtokában.

5. Válassza ki a tanúsítványt a **tanúsítvány részletei** párbeszédpanel megtekintéséhez.

6. Válassza az **ellenőrző kód előállítása** lehetőséget a párbeszédpanelen.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Tanúsítvány részletei párbeszédpanel}":::

7. Másolja az ellenőrzőkódot a vágólapra. A tanúsítvány tulajdonosának kell megadnia az ellenőrző kódot. Ha például az ellenőrző kód 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, adja hozzá a tanúsítványt tulajdonosként a következő lépésben látható módon.

8. Az ellenőrző tanúsítvány három módon hozható elő:

    * Ha a Microsoft által biztosított PowerShell-parancsfájlt használja, futtassa a parancsot a `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` nevű tanúsítvány létrehozásához `VerifyCert4.cer` . További információ: [a Microsoft által biztosított parancsfájlok használata](tutorial-x509-scripts.md).

    * Ha a Microsoft által megadott bash-parancsfájlt használja, futtassa a parancsot a `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` nevű tanúsítvány létrehozásához `verification-code.cert.pem` . További információ: [a Microsoft által biztosított parancsfájlok használata](tutorial-x509-scripts.md).

    * Ha OpenSSL-t használ a tanúsítványok létrehozásához, először egy titkos kulcsot és egy tanúsítvány-aláírási kérelmet (CSR) kell előállítania:

      ```bash
      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Ezután hozzon létre egy tanúsítványt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ konfigurációs fájljával (lásd alább) vagy az alárendelt HITELESÍTÉSSZOLGÁLTATÓ konfigurációs fájlját és a CSR-t.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    További információ: az [OpenSSL használata tesztelési tanúsítványok létrehozásához](tutorial-x509-openssl.md).

10. Válassza ki az új tanúsítványt a **tanúsítvány részletei** nézetben.

11. A tanúsítvány feltöltése után válassza az **ellenőrzés** lehetőséget. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány állapotának **ellenőrzött** értékre kell váltania.
