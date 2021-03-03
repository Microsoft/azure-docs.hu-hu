---
title: X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok ellenőrzése az Azure IoT Hub Device Provisioning Service
description: Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok bizonyítása az Azure IoT Hub Device Provisioning Service (DPS) használatával
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0326eef86b42fa8b06c336bbf2b1c6f2f9df0bcf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730354"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok bizonyítása az eszköz kiépítési szolgáltatásával

Az ellenőrzött X. 509 hitelesítésszolgáltató (CA) tanúsítvány egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány, amely fel lett töltve és regisztrálva lett a kiépítési szolgáltatásban, és a szolgáltatással való megfelelőség igazolásával járt. 

A birtokukban lévő igazolás a következő lépéseket foglalja magában:
1. Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz tartozó kiépítési szolgáltatás által generált egyedi ellenőrző kód beszerzése. Ezt az Azure Portalon teheti meg.
2. Hozzon létre egy X. 509 ellenőrző tanúsítványt az ellenőrző kóddal a tulajdonosként, és írja alá a tanúsítványt az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz tartozó titkos kulccsal.
3. Töltse fel az aláírt ellenőrző tanúsítványt a szolgáltatásba. A szolgáltatás érvényesíti az ellenőrző tanúsítványt a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nyilvános részével, amely igazolja, hogy Ön rendelkezik a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány titkos kulcsával.

A hitelesített tanúsítványok fontos szerepet játszanak a beléptetési csoportok használatakor. A tanúsítvány tulajdonjogának ellenőrzése további biztonsági réteget biztosít annak biztosításához, hogy a tanúsítvány feltöltője a tanúsítvány titkos kulcsának birtokában legyen. Az ellenőrzés megakadályozza, hogy egy rosszindulatú szereplő megszippantsa a forgalmat egy köztes tanúsítvány kinyerésével, és ezzel a tanúsítvánnyal hozzon létre egy regisztrációs csoportot a saját kiépítési szolgáltatásában, és hatékonyan eltérítse az eszközöket. Ha egy tanúsítványlánc tulajdonosát vagy köztes tanúsítványát igazolja, a rendszer igazolja, hogy rendelkezik a regisztrációs csoport részeként regisztrálni kívánt eszközökhöz tartozó levél-tanúsítványok létrehozásához szükséges engedéllyel. Emiatt a beléptetési csoportban konfigurált legfelső szintű vagy köztes tanúsítványnak hitelesített tanúsítványnak kell lennie, vagy az eszköz által a szolgáltatással végzett hitelesítés során megjelenő ellenőrzött tanúsítványnak kell lennie. Az X. 509 tanúsítvány igazolásával kapcsolatos további információkért lásd: [x. 509 tanúsítványok](concepts-x509-attestation.md) és [az eszközök hozzáférésének szabályozása a kiépítési szolgáltatáshoz x. 509 tanúsítvánnyal](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Egy X. 509 tanúsítvány nyilvános részének regisztrálása és egy ellenőrző kód beszerzése

Ha HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt szeretne regisztrálni a kiépítési szolgáltatással, és beolvas egy ellenőrző kódot, amelyet a rendszer a birtokában használhat, kövesse az alábbi lépéseket. 

1. A Azure Portal navigáljon a kiépítési szolgáltatáshoz, és nyissa meg a **tanúsítványok** párbeszédpanelt a bal oldali menüben. 
2. Új tanúsítvány hozzáadásához kattintson a **Hozzáadás** gombra.
3. Adja meg a tanúsítvány felhasználóbarát megjelenítendő nevét. Tallózással keresse meg az X. 509 tanúsítvány nyilvános részét jelképező. cer vagy. PEM fájlt. Kattintson a **Feltöltés** gombra.
4. Ha értesítést kap arról, hogy a tanúsítvány feltöltése sikeresen megtörtént, kattintson a **Save (Mentés**) gombra.

    ![Tanúsítvány feltöltése](./media/how-to-verify-certificates/add-new-cert.png)  

   A tanúsítvány a **Certificate Explorer** listájában fog megjelenni. Vegye figyelembe, hogy a tanúsítvány **állapota** nem *ellenőrzött*.

5. Kattintson az előző lépésben hozzáadott tanúsítványra.

6. A **tanúsítvány részletei** területen kattintson az **ellenőrző kód előállítása** elemre.

7. A kiépítési szolgáltatás létrehoz egy **ellenőrző kódot** , amelyet a tanúsítvány tulajdonjogának ellenőrzésére használhat. Másolja a kódot a vágólapra. 

   ![Tanúsítvány ellenőrzése](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Az ellenőrző kód digitális aláírása ellenőrző tanúsítvány létrehozásához

Most alá kell írnia az *ellenőrző kódot* az X. 509 hitelesítésszolgáltatói tanúsítványhoz társított titkos kulccsal, amely aláírást generál. Ez a [birtoklás igazolása](https://tools.ietf.org/html/rfc5280#section-3.1) , és az eredmény egy aláírt ellenőrző tanúsítvány.

A Microsoft olyan eszközöket és mintákat biztosít, amelyek segítségével aláírt ellenőrző tanúsítványt hozhat létre: 

- Az **Azure IoT hub C SDK** PowerShell-(Windows-) és bash-(Linux-) szkripteket biztosít, amelyek segítségével hitelesítésszolgáltatói és levél-tanúsítványokat hozhat létre a fejlesztéshez, valamint igazolhatja a birtokukban lévő ellenőrző kódot. A rendszerhez tartozó [fájlokat](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) letöltheti egy munkamappába, és a [hitelesítésszolgáltató tanúsítványait kezelő információ kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) című témakör útmutatását követve elvégezheti a hitelesítésszolgáltatói tanúsítványon való megfelelőség igazolását. 
- Az **Azure IoT hub C# SDK** tartalmazza a [csoport tanúsítvány-ellenőrzési mintáját](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), amelynek használatával igazolhatja a birtokukban lévő tanúsítványt.
 
> [!IMPORTANT]
> A korábban hivatkozott PowerShell-és bash-parancsfájlok Emellett lehetővé teszik a tanúsítványok hitelesítésére és az eszközök kiosztására szolgáló főtanúsítványok, köztes tanúsítványok és levél-tanúsítványok létrehozására is. Ezeket a tanúsítványokat csak fejlesztési célokra lehet használni. Nem használhatók éles környezetben. 

A dokumentációban és az SDK-ban megadott PowerShell-és bash-parancsfájlok [OpenSSL](https://www.openssl.org/)-t használnak. Az OpenSSL vagy más, harmadik féltől származó eszközöket is használhat, hogy segítséget nyújtson a birtokukban tartásához. Az SDK-k által biztosított szerszámozást használó példaért tekintse meg [az X. 509 tanúsítványlánc létrehozása](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)című témakört. 


## <a name="upload-the-signed-verification-certificate"></a>Az aláírt ellenőrző tanúsítvány feltöltése

1. Töltse fel az eredményül kapott aláírást ellenőrző tanúsítványként a kiépítési szolgáltatásba a portálon. A **tanúsítvány részletei** a Azure Portalban az **ellenőrző tanúsítvány. PEM vagy. cer fájl** mező melletti _fájlkezelő_ ikon használatával töltse fel az aláírt ellenőrző tanúsítványt a rendszerből.

2. Miután a tanúsítvány feltöltése sikeresen megtörtént, kattintson az **ellenőrzés** gombra. A tanúsítvány állapota a **tanúsítvány-kezelő** listájában **_ellenőrzött_** **állapotra** változik. Kattintson a **frissítés** gombra, ha az nem frissül automatikusan.

   ![Tanúsítvány-ellenőrzés feltöltése](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Következő lépések

- Ha szeretne többet megtudni arról, hogyan használható a portál egy regisztrációs csoport létrehozásához, tekintse meg [az eszközök regisztrálásának kezelése a Azure Portal](how-to-manage-enrollments.md)használatával című témakört.
- Ha szeretné megtudni, hogyan használhatja a szolgáltatás SDK-kat egy regisztrációs csoport létrehozásához, tekintse meg az [eszközök regisztrálásának kezelése a Service SDK-val](./quick-enroll-device-x509-java.md)című témakört.