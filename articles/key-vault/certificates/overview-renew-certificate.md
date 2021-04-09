---
title: Tudnivalók a Azure Key Vault tanúsítvány megújításáról
description: Ez a cikk a tanúsítványok Azure Key Vaultának megújítását ismerteti.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: ffa130c0598d2405469d272a3ac6852f281ed965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726362"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault-tanúsítványok megújítása

A Azure Key Vault segítségével könnyedén kiépítheti, kezelheti és telepítheti a hálózat digitális tanúsítványait, és engedélyezheti az alkalmazásai számára a biztonságos kommunikációt. További információ a tanúsítványokról: [Tudnivalók a Azure Key Vault tanúsítványokról](./about-certificates.md).

A rövid élettartamú tanúsítványok vagy a tanúsítvány-elforgatás gyakoriságának növelésével megakadályozhatja, hogy jogosulatlan felhasználók hozzáférjenek az alkalmazásaihoz.

Ez a cikk a Azure Key Vault-tanúsítványok megújítását ismerteti.

## <a name="get-notified-about-certificate-expiration"></a>Értesítés a tanúsítvány lejáratáról
Ha értesítést szeretne kapni a tanúsítványok élettartamával kapcsolatos eseményekről, hozzá kell adnia a tanúsítvány kapcsolattartóját. A tanúsítványhoz tartozó névjegyek kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartama eseményei által aktivált értesítések küldéséhez. A kapcsolattartási adatokat a Key Vault összes tanúsítványa megosztja. A Key Vault bármelyik tanúsítványa esetében értesítést küld a rendszer az adott esemény összes megadott ügyfelének.

### <a name="steps-to-set-certificate-notifications"></a>A tanúsítványok értesítéseinek beállításához szükséges lépések:
Először vegyen fel egy tanúsítványt a kulcstartóba. Azure Portal vagy PowerShell-parancsmag használatával adhat hozzá [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) .

Másodszor, konfigurálja, ha értesítést szeretne kapni a tanúsítvány lejáratáról. A tanúsítvány életciklus-attribútumainak konfigurálásához tekintse [meg a tanúsítvány autorotációjának konfigurálása Key Vaultban](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)című témakört.

Ha a tanúsítvány házirendje automatikus megújításra van beállítva, akkor a rendszer értesítést küld a következő eseményekről.

- A tanúsítvány megújítása előtt
- A tanúsítvány megújítása után, ha a tanúsítvány sikeres megújítása megtörtént, vagy ha hiba történt, a tanúsítvány manuális megújítását kell megkövetelni.  

  Ha egy manuálisan megújított tanúsítvány-házirend (csak e-mail-cím), akkor a rendszer értesítést küld, amikor a tanúsítvány megújítására kerül sor.  

Key Vault a tanúsítványok három kategóriába sorolhatók:
-    Integrált hitelesítésszolgáltatóval (CA) létrehozott tanúsítványok, például DigiCert vagy GlobalSign
-    Nem integrált HITELESÍTÉSSZOLGÁLTATÓval létrehozott tanúsítványok
-    Önaláírt tanúsítványok

## <a name="renew-an-integrated-ca-certificate"></a>Integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
Azure Key Vault kezeli a megbízható Microsoft-hitelesítésszolgáltatók DigiCert és GlobalSign által kiállított tanúsítványok végpontok közötti karbantartását. Ismerje meg, hogyan [integrálhat egy megbízható hitelesítésszolgáltatót Key Vault](./how-to-integrate-certificate-authority.md)használatával.

## <a name="renew-a-nonintegrated-ca-certificate"></a>Nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújítása 
A Azure Key Vault használatával bármilyen HITELESÍTÉSSZOLGÁLTATÓTÓL importálhat tanúsítványokat, így számos Azure-erőforrással integrálható, és egyszerűen üzembe helyezhető. Ha aggódik a tanúsítvány lejárati dátumának elvesztésével kapcsolatban, vagy ha még rosszabb, felfedezte, hogy egy tanúsítvány már lejárt, a kulcstartó segíthet a naprakészen tartásban. A nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok esetében a Key Vault lehetővé teszi, hogy közel lejáratú e-mailes értesítéseket hozzon létre. Ilyen értesítések több felhasználó számára is megadhatók.

> [!IMPORTANT]
> A tanúsítvány verziószámú objektum. Ha az aktuális verzió lejár, létre kell hoznia egy új verziót. Elméletileg minden új verzió egy olyan új tanúsítvány, amely egy kulcsból és egy olyan blobból áll, amely egy identitáshoz kötődik. Ha nem partneri HITELESÍTÉSSZOLGÁLTATÓT használ, a Key Vault létrehoz egy kulcs/érték párokat, és visszaadja a tanúsítvány-aláírási kérelmet (CSR).

Nem integrált HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújításához tegye a következőket:

1. Jelentkezzen be a Azure Portalba, majd nyissa meg a megújítani kívánt tanúsítványt.
1. A tanúsítvány ablaktáblán válassza az **új verzió** elemet.
1. Válassza a **tanúsítvány művelet** lehetőséget.
1. Válassza a **CSR letöltése** lehetőséget, hogy letöltse a CSR-fájlt a helyi meghajtóra.
1. Küldje el az CSR-t a választott HITELESÍTÉSSZOLGÁLTATÓnak a kérelem aláírásához.
1. Hozza vissza az aláírt kérelmet, és válassza a **CSR egyesítése** elemet ugyanazon a tanúsítvány műveleti ablaktáblán.

> [!NOTE]
> Fontos, hogy egyesítse az aláírt CSR-t ugyanazzal a CSR-kéréssel, amelyet Ön hozott létre. Ellenkező esetben a kulcs nem egyezik.

Az új CSR létrehozásával kapcsolatos további információkért lásd: [CSR létrehozása és egyesítése Key Vaultban]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Önaláírt tanúsítvány megújítása

A Azure Key Vault az önaláírt tanúsítványok automatikus megújítását is kezeli. Ha többet szeretne megtudni a kiállítási szabályzat módosításáról és a tanúsítvány életciklus-attribútumainak frissítéséről, tekintse meg a [tanúsítvány autorotációjának konfigurálása Key Vaultban](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)című témakört.

## <a name="troubleshoot"></a>Hibaelhárítás
* Ha a kiállított tanúsítvány *letiltott* állapotban van a Azure Portalban, lépjen a **tanúsítvány műveletre** a tanúsítvány hibaüzenetének megtekintéséhez.
* Hiba típusa: "a tanúsítvány beszerzéséhez használt CSR már használatban van. Próbáljon új tanúsítványt készíteni új CSR-sel. "
  Nyissa meg a tanúsítvány speciális szabályzat szakaszát, és ellenőrizze, hogy ki van-e kapcsolva a **"kulcs újrafelhasználásának megújítása** " beállítás.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Hogyan tesztelhető a tanúsítvány autorotációs funkciója?**

Hozzon létre egy **hónap** érvényességével rendelkező tanúsítványt, majd állítsa be az élettartam műveletet az elforgatáshoz **1%**-ban. Ez a beállítás 7,2 óránként elforgatja a tanúsítványt.
  
**A rendszer replikálja a címkéket a tanúsítvány automatikus megújítása után?**

Igen, a címkék az automatikus megújítás után replikálódnak.

## <a name="next-steps"></a>Következő lépések
*    [Key Vault integrálása DigiCert-hitelesítésszolgáltatóval](how-to-integrate-certificate-authority.md)
*    [Oktatóanyag: a tanúsítvány autorotációjának konfigurálása Key Vaultban](tutorial-rotate-certificates.md)
