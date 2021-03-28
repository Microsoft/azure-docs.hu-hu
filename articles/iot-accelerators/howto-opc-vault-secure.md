---
title: Az OPC UA-eszközök kommunikációjának biztonságossá tétele az OPC-tárolóval – Azure | Microsoft Docs
description: OPC UA-alkalmazások regisztrálása és az OPC UA-eszközökhöz tartozó aláírt alkalmazás-tanúsítványok kiállítása az OPC-tárolóval.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646145"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Az OPC Vault Certificate Management szolgáltatás használata

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk azt ismerteti, hogyan regisztrálhat alkalmazásokat, és hogyan lehet aláírt alkalmazás-tanúsítványokat kibocsátani az OPC UA-eszközökhöz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="deploy-the-certificate-management-service"></a>A Tanúsítványkezelő szolgáltatás üzembe helyezése

Először telepítse a szolgáltatást az Azure-felhőbe. Részletekért lásd: [az OPC-tár tanúsítványkezelő szolgáltatásának telepítése](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

Ha még nem tette meg, hozza létre a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát. Részletekért lásd: [az OPC-tároló kiállítói tanúsítványának létrehozása és kezelése](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Biztonságos OPC UA-alkalmazások

### <a name="step-1-register-your-opc-ua-application"></a>1. lépés: az OPC UA-alkalmazás regisztrálása 

> [!IMPORTANT]
> Egy alkalmazás regisztrálásához az író szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat a (z) helyen `https://myResourceGroup-app.azurewebsites.net` , és jelentkezzen be.
2. Lépjen az **új regisztrálása** gombra. Egy alkalmazás regisztrálásához a felhasználónak legalább a hozzárendelt író szerepkörrel kell rendelkeznie.
2. A nevezési űrlap az OPC UA elnevezési konvencióit követi. Az alábbi képernyőképen látható, hogy az OPC ua- [hivatkozási kiszolgáló](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) mintája az OPC ua .NET Standard stackben jelenik meg:

   ![Az UA-hivatkozási kiszolgáló regisztrációjának képernyőképe](media/howto-opc-vault-secure/reference-server-registration.png "UA-hivatkozási kiszolgáló regisztrálása")

5. Válassza a **regisztráció** lehetőséget, ha regisztrálni szeretné az alkalmazást a Certificate Service Application adatbázisban. A munkafolyamat közvetlenül a következő lépéssel irányítja a felhasználót, hogy aláírt tanúsítványt kérjen az alkalmazáshoz.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2. lépés: az alkalmazás biztonságossá tétele HITELESÍTÉSSZOLGÁLTATÓI aláírású alkalmazás tanúsítványával

Az OPC UA-alkalmazás biztonságossá tételéhez tanúsítvány-aláírási kérelem (CSR) alapján kell kibocsátani egy aláírt tanúsítványt. Azt is megteheti, hogy új kulcspárt kér, amely egy új titkos kulcsot tartalmaz PFX vagy PEM formátumban. Az alkalmazás által támogatott módszerekről az OPC UA-eszköz dokumentációjában olvashat bővebben. Általánosságban elmondható, hogy a CSR-módszer használata ajánlott, mert nincs szükség arra, hogy a titkos kulcs átvitele a hálózaton keresztül történjen.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Új tanúsítvány igénylése új kulcspárt

1. Nyissa meg az **alkalmazásokat**.
3. Válassza ki az **új kérelem** elemet a felsorolt alkalmazásokhoz.

   ![A kérelem új tanúsítványának képernyőképe](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Válassza az **új kulcspár és tanúsítvány kérése** lehetőséget, hogy egy titkos kulcsot és egy új aláírt tanúsítványt kérjen az alkalmazás nyilvános kulcsával.

   ![Képernyőfelvétel új kulcspár és tanúsítvány létrehozásáról](media/howto-opc-vault-secure/generate-new-key-pair.png "Új kulcspár előállítása")

4. Töltse ki az űrlapot a tárgy és a tartománynevek között. A titkos kulcshoz válassza a PEM vagy a PFX jelszót. Válassza az **új kulcspár** létrehozása lehetőséget a tanúsítványkérelem létrehozásához.

   ![Képernyőfelvétel: a tanúsítvány kérésének részletei képernyő és az új kulcspár előállítása gomb.](media/howto-opc-vault-secure/approve-reject.png "Tanúsítvány jóváhagyása")

5. A jóváhagyáshoz a jóváhagyó szerepkörrel rendelkező felhasználónak, valamint a Azure Key Vault aláírási engedélyekkel kell rendelkeznie. A tipikus munkafolyamatban a jóváhagyó és a kérelmező szerepkört különböző felhasználókhoz kell rendelni. A kulcspár és az aláírási művelet tényleges létrehozásának elindításához vagy megszakításához válassza a **jóváhagyás** vagy az **elutasítás** lehetőséget. Az új kulcspár létrehozása és tárolása biztonságosan történik Azure Key Vaultban, amíg a tanúsítvány kérelmezője le nem tölti. A HITELESÍTÉSSZOLGÁLTATÓ aláírja az eredményül kapott tanúsítványt a nyilvános kulccsal. Ezek a műveletek eltarthat néhány másodpercig a befejezésig.

   ![Képernyőfelvétel a tanúsítványkérelem részleteiről, a jóváhagyási üzenettel alul](media/howto-opc-vault-secure/view-key-pair.png "Kulcspár megtekintése")

7. Az eredményül kapott titkos kulcs (PFX vagy PEM) és tanúsítvány (DER) innen tölthető le a bináris fájlok letöltése lehetőséggel kiválasztva. A Base64 kódolású verzió is elérhető, például a tanúsítvány parancssori vagy szöveges bejegyzésbe való másolásához és beillesztéséhez. 
8. A titkos kulcs letöltése és biztonságos tárolása után kiválaszthatja a **titkos kulcs törlése** lehetőséget. A nyilvános kulccsal rendelkező tanúsítvány jövőbeli használatra továbbra is elérhető marad.
9. A CA által aláírt tanúsítvány használata miatt a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és a tanúsítvány-visszavonási lista (CRL) is letölthető.

Most az OPC UA-eszköztől függ, hogyan alkalmazza az új kulcspárt. A HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát és a CRL-t általában egy mappába másolja a rendszer `trusted` , míg az alkalmazás tanúsítványának nyilvános és titkos kulcsa a tanúsítványtároló egyik mappájára lesz alkalmazva `own` . Előfordulhat, hogy egyes eszközök már támogatják a kiszolgálók leküldését a tanúsítvány frissítéseire. Tekintse át az OPC UA-eszköz dokumentációját.

#### <a name="request-a-new-certificate-with-a-csr"></a>Új tanúsítvány igénylése CSR-sel 

1. Nyissa meg az **alkalmazásokat**.
3. Válassza ki az **új kérelem** elemet a felsorolt alkalmazásokhoz.

   ![A kérelem új tanúsítványának képernyőképe](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Válassza az **új tanúsítvány kérése aláírási kéréssel** lehetőséget, hogy új aláírt tanúsítványt igényeljen az alkalmazáshoz.

   ![Képernyőfelvétel új tanúsítvány létrehozásáról](media/howto-opc-vault-secure/generate-new-certificate.png "Új tanúsítvány előállítása")

4. Töltse fel a CSR-t egy helyi fájl kiválasztásával, vagy egy Base64 kódolású CSR beillesztésével az űrlapon. Válassza az **új tanúsítvány előállítása** lehetőséget.

   ![A tanúsítványkérelem megtekintésére vonatkozó információk képernyőképe](media/howto-opc-vault-secure/approve-reject-csr.png "CSR jóváhagyása")

5. A jóváhagyáshoz a jóváhagyó szerepkörrel rendelkező felhasználónak, valamint a Azure Key Vault aláírási engedélyekkel kell rendelkeznie. A tényleges aláírási művelet elindításához vagy megszakításához válassza a **jóváhagyás** vagy az **elutasítás** lehetőséget. A HITELESÍTÉSSZOLGÁLTATÓ aláírja az eredményül kapott tanúsítványt a nyilvános kulccsal. Ez a művelet eltarthat néhány másodpercig.

   ![A tanúsítvány megtekintésére vonatkozó kérés részleteit megjelenítő képernyőkép, amely egy jóváhagyási üzenetet tartalmaz alul.](media/howto-opc-vault-secure/view-cert-csr.png "Tanúsítvány megtekintése")

6. Az eredményül kapott tanúsítvány (DER) innen tölthető le bináris fájlként. A Base64 kódolású verzió is elérhető, például a tanúsítvány parancssori vagy szöveges bejegyzésbe való másolásához és beillesztéséhez. 
10. A tanúsítvány letöltése és biztonságos tárolása után válassza a **tanúsítvány törlése** lehetőséget.
11. A CA által aláírt tanúsítvány használata miatt a HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát és a CRL-t is le kell tölteni.

Most az OPC UA-eszköztől függ, hogyan alkalmazza az új tanúsítványt. A HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát és a CRL-t általában egy mappába másolja a rendszer `trusted` , miközben az alkalmazás tanúsítványa a tanúsítványtároló egyik mappájára van alkalmazva `own` . Előfordulhat, hogy egyes eszközök már támogatják a kiszolgálók leküldését a tanúsítvány frissítéseire. Tekintse át az OPC UA-eszköz dokumentációját.

### <a name="step-3-device-secured"></a>3. lépés: biztonságos eszköz

Az OPC UA-eszköz most már készen áll arra, hogy a CA által aláírt tanúsítványok által védett más OPC UA-eszközökkel tudjon kommunikálni további konfiguráció nélkül.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC UA-eszközök biztonságossá tételét, a következőket teheti:

> [!div class="nextstepaction"]
> [Biztonságos tanúsítványkezelő szolgáltatás futtatása](howto-opc-vault-secure-ca.md)
