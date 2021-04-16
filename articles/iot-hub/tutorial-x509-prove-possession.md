---
title: Oktatóanyag – A hitelesítésszolgáltatói tanúsítványok tulajdonjogának bizonyítása a Azure IoT Hub | Microsoft Docs
description: Oktatóanyag – Annak bizonyítása, hogy rendelkezik hitelesítésszolgáltatói tanúsítvánnyal a Azure IoT Hub
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378227"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Oktatóanyag: Hitelesítésszolgáltatói tanúsítvány birtoklásának igazolása

Miután feltöltötte a legfelső szintű hitelesítésszolgáltatói (CA) tanúsítványt vagy az alárendelt CA-tanúsítványt az IoT Hubra, igazolnia kell, hogy Ön a tanúsítvány a következő:

1. A Azure Portal lépjen az IoTHubra, és válassza **a Beállítások és tanúsítványok > lehetőséget.**

2. Új **hitelesítésszolgáltatói** tanúsítvány hozzáadásához válassza a Hozzáadás lehetőséget.

3. Adjon meg egy megjelenítendő nevet a **Tanúsítvány neve mezőben,** majd válassza ki a hozzáadni kívánt PEM-tanúsítványt.

4. Kattintson a **Mentés** gombra. A tanúsítvány nem ellenőrzött állapotúként jelenik meg a **tanúsítványlistában.** Ez az ellenőrzési folyamat igazolja, hogy Ön rendelkezik a tanúsítvánnyal.

5. Válassza ki a tanúsítványt a Tanúsítvány **részletei párbeszédpanel megtekintéséhez.**

6. A **párbeszédpanelen válassza** az Ellenőrző kód létrehozása lehetőséget.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Tanúsítvány részletei párbeszédpanel}":::

7. Másolja az ellenőrzőkódot a vágólapra. Az ellenőrző kódot a tanúsítvány tulajdonosának kell beállítania. Ha például az ellenőrző kód 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, adja hozzá a tanúsítványhoz a következő lépésben látható módon.

8. Ellenőrzési tanúsítványt háromféleképpen hozhat létre:

    * Ha a Microsoft által biztosított PowerShell-szkriptet használja, futtassa a parancsot `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` egy nevű tanúsítvány `VerifyCert4.cer` létrehozásához. További információ: [Using Microsoft-supplied Scripts (A Microsoft által biztosított parancsfájlok használata).](tutorial-x509-scripts.md)

    * Ha a Microsoft által biztosított Bash-szkriptet használja, a futtatásával `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` hozzon létre egy nevű tanúsítványt. `verification-code.cert.pem` További információ: [Using Microsoft-supplied Scripts (A Microsoft által biztosított parancsfájlok használata).](tutorial-x509-scripts.md)

    * Ha OpenSSL-t használ a tanúsítványok létrehozásához, először létre kell hoznia egy titkos kulcsot, majd egy tanúsítvány-aláírási kérelmet (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

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

      Ezután hozzon létre egy tanúsítványt a legfelső szintű hitelesítésszolgáltató konfigurációs fájljának (lásd alább) vagy az alárendelt CA konfigurációs fájljának és a CSR-nek a használatával.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    További információ: [Using OpenSSL to Create Test Certificates (Az OpenSSL használata teszttanúsítványok létrehozásához).](tutorial-x509-openssl.md)

10. Válassza ki az új tanúsítványt a **Tanúsítvány részletei nézetben.**

11. A tanúsítvány feltöltése után válassza a **Verify (Ellenőrzés) lehetőséget.** A hitelesítésszolgáltatói tanúsítvány állapotának Ellenőrzött **állapotra kell változnia.**
