---
title: További Azure Key Vault tanúsítvány megújításáról
description: Ez a cikk a tanúsítványok megújítását Azure Key Vault ismerteti.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 082b0fd4d3324502516dcd2b45b9ad16a919c773
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749221"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Újítsa meg Azure Key Vault tanúsítványait

A Azure Key Vault segítségével könnyedén kiépíthet, kezelhet és telepíthet digitális tanúsítványokat a hálózatához, és biztonságos kommunikációt tesz lehetővé alkalmazásai számára. A tanúsítványokkal kapcsolatos további információkért lásd: Tudnivalók Azure Key Vault [tanúsítványokról.](./about-certificates.md)

A rövid életű tanúsítványok vagy a tanúsítványrotáció gyakoriságának növelésével megakadályozhatja, hogy jogosulatlan felhasználók hozzáférjenek az alkalmazásokhoz.

Ez a cikk a tanúsítvány tanúsítványai megújítását Azure Key Vault ismerteti.

## <a name="get-notified-about-certificate-expiration"></a>Értesítés a tanúsítvány lejáratával kapcsolatban
Ha értesítést szeretne kapni a tanúsítvány életciklusának eseményeiről, hozzá kell adni a tanúsítvány-kapcsolattartót. A tanúsítvány-kapcsolattartók kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartameseményei által kiváltott értesítések küldése érdekében. A névjegyek adatait a kulcstartóban található összes tanúsítvány megosztja. A rendszer értesítést küld a kulcstartóban található összes tanúsítványhoz egy adott esemény összes megadott kapcsolattartója számára.

### <a name="steps-to-set-certificate-notifications"></a>A tanúsítványértesítések beállításának lépései:
Először adjon hozzá egy tanúsítvány-kapcsolattartót a kulcstartóhoz. A parancsmagot a Azure Portal PowerShell-parancsmag használatával [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) használhatja.

Másodszor konfigurálja a következőt: ha értesítést szeretne kap a tanúsítvány lejárati dátumával kapcsolatban. A tanúsítvány életciklus-attribútumainak konfigurálásával lásd: Tanúsítvány automatikus hitelesítésének konfigurálása a [Key Vault.](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)

Ha egy tanúsítvány szabályzata automatikus megújításra van beállítva, a rendszer értesítést küld a következő eseményekről.

- A tanúsítvány megújítása előtt
- A tanúsítvány megújítása után, ha a tanúsítvány megújítása sikeres volt, vagy hiba történt, a tanúsítvány manuális megújítását igényli.  

  Ha egy manuálisan megújítani beállított tanúsítvány-házirendet (csak e-mailben), a rendszer értesítést küld, amikor a tanúsítvány megújítására van szükség.  

A Key Vault tanúsítványok három kategóriába sorolhatók:
-    Integrált hitelesítésszolgáltatóval (CA) létrehozott tanúsítványok, például DigiCert vagy GlobalSign
-    Nemintegált hitelesítésszolgáltatóval létrehozott tanúsítványok
-    Önaírt tanúsítványok

## <a name="renew-an-integrated-ca-certificate"></a>Integrált hitelesítésszolgáltatói tanúsítvány megújítása 
Azure Key Vault a megbízható Microsoft-hitelesítésszolgáltató , a DigiCert és a GlobalSign által kiállított tanúsítványok végpontok közötti karbantartását kezeli. Megtudhatja, hogyan [integrálhatja a megbízható hitelesítésszolgáltatót a Key Vault.](./how-to-integrate-certificate-authority.md)

## <a name="renew-a-nonintegrated-ca-certificate"></a>Nemintegrated CA-tanúsítvány megújítása 
A Azure Key Vault bármely hitelesítésszolgáltatótól importálhat tanúsítványokat. Ez az egyik előnye, hogy számos Azure-erőforrással integrálható, és megkönnyíti az üzembe helyezést. Ha aggódik a tanúsítvány lejárati dátumának elvesztése miatt, vagy ami még rosszabb, azt is tudja, hogy egy tanúsítvány már lejárt, a kulcstartó segíthet naprakészen tartani. A nemintegrated CA-tanúsítványok esetében a kulcstartó lehetővé teszi a közel lejárati idő alatt értesítő e-mailek beállítását. Ezek az értesítések több felhasználóhoz is beállíthatók.

> [!IMPORTANT]
> A tanúsítvány verziószámmal rendelkezik. Ha az aktuális verzió lejár, új verziót kell létrehoznia. Elméleti szinten minden új verzió egy új tanúsítvány, amely egy kulcsból és egy blobból áll, amely ezt a kulcsot egy identitáshoz egyesíti. Ha nem partneri hitelesítésszolgáltatót használ, a kulcstartó létrehoz egy kulcs/érték párt, és egy tanúsítvány-aláírási kérelmet (CSR) ad vissza.

Nemintegrált hitelesítésszolgáltatói tanúsítvány megújítása a következő:

1. Jelentkezzen be a Azure Portal, majd nyissa meg a megújítani kívánt tanúsítványt.
1. A tanúsítványpanelen válassza az Új **verzió lehetőséget.**
1. Válassza **a Tanúsítványművelet lehetőséget.**
1. Válassza **a CSR letöltése lehetőséget** egy CSR-fájl helyi meghajtóra való letöltéséhez.
1. Küldje el a CSR-t a választott ca-nak a kérelem aláíráshoz.
1. Hozza vissza az aláírt kérést, és válassza **a CSR egyesítése** lehetőséget ugyanazon a tanúsítványműveleti panelen.

> [!NOTE]
> Fontos, hogy az aláírt CSR-t a létrehozott CSR-kéréssel egyesítse. Ellenkező esetben a kulcs nem fog egyezni.

További információ az új CSR létrehozásáról: CSR létrehozása és egyesítése [a Key Vault.]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)

## <a name="renew-a-self-signed-certificate"></a>Önaírt tanúsítvány megújítása

Azure Key Vault az önaírt tanúsítványok automatikus újraújtanúsítványait is kezeli. A kiállítási szabályzat módosításával és a tanúsítvány életciklus-attribútumainak frissítésével kapcsolatos további információkért lásd: Tanúsítvány automatikus [rotálásának](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)konfigurálása a Key Vault.

## <a name="troubleshoot"></a>Hibaelhárítás
* Ha a kiadott  tanúsítvány le van tiltva az  Azure Portal, a Tanúsítványművelet alatt megtekintheti a tanúsítvány hibaüzenetét.
* Hibatípus: "A tanúsítvány lekérni használt CSR már használva van. Hozzon létre egy új tanúsítványt egy új CSR-val."
  A tanúsítvány "Speciális szabályzat" szakaszában ellenőrizze, hogy ki van-e kapcsolva az "újrafelhasználási kulcs **megújításkor"** beállítás.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Hogyan tesztelheti a tanúsítvány automatikus automatizáló funkcióját?**

Hozzon létre egy önaírt tanúsítványt **1** hónapos érvényességgel, majd állítsa be a rotáció élettartam-műveletét **1%-ra.** Meg kell tudnia tekinteni a következő néhány napban létrehozott tanúsítványverzió-előzményeket.
  
**A rendszer replikálja a címkéket a tanúsítvány automatikus újraújulása után?**

Igen, a címkék az automatikus újraújulás után replikálódnak.

## <a name="next-steps"></a>Következő lépések
*    [Integráció Key Vault DigiCert hitelesítésszolgáltatóval](how-to-integrate-certificate-authority.md)
*    [Oktatóanyag: Tanúsítvány automatikus elrotálásának konfigurálása a Key Vault](tutorial-rotate-certificates.md)
