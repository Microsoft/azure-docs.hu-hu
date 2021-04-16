---
title: OATH-jogkivonatok hitelesítési módszere – Azure Active Directory
description: Megtudhatja, hogyan javíthatja és biztosíthatja a bejelentkezési események Azure Active Directory OATH-jogkivonatok használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d0dd081e3e1a681ba55e3457b79a548d6b2bb7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530378"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Hitelesítési módszerek a Azure Active Directory - OATH-jogkivonatok 

Az OATH TOTP (időalapú egyszeri jelszó) egy nyílt szabvány, amely meghatározza az egyszeri jelszókódok (OTP) generálásának a beállítását. Az OATH TOTP szoftverrel vagy hardverrel is megvalósítható a kódok létrehozásához. Az Azure AD nem támogatja az OATH HOTP-t, amely egy másik kódgenerációs szabvány.

## <a name="oath-software-tokens"></a>OATH szoftvertokenek

A szoftver OATH tokenek általában olyan alkalmazások, mint Microsoft Authenticator alkalmazás és más hitelesítő alkalmazások. Az Azure AD generálja az alkalmazásba bevitt és az egyes egyszeri egyszeri kulcsok létrehozásához használt titkos kulcsot.

Az Authenticator alkalmazás automatikusan generál kódokat, amikor leküldéses értesítésekre van beállítva, így a felhasználó akkor is rendelkezik biztonsági másolattal, ha az eszköz nem rendelkezik kapcsolattal. Olyan külső alkalmazások is használhatók, amelyek az OATH TOTP használatával hoznak létre kódokat.

Egyes OATH TOTP hardvertokenek programozhatók, ami azt jelenti, hogy nem nak előre programozott titkos kulccsal vagy maggal. Ezek a programozható hardvertokenek a szoftvertoken beállítási folyamatának titkos kulcsával vagy maggal állíthatók be. Az ügyfelek megvásárolhatja ezeket a jogkivonatokat a választott szállítótól, és a titkos kulcsot vagy a magot a szállító beállítási folyamatában használhatja.

## <a name="oath-hardware-tokens-preview"></a>OATH hardveres jogkivonatok (előzetes verzió)

Az Azure AD támogatja az OATH-TOTP SHA-1 tokenek használatát, amelyek 30 vagy 60 másodpercenként frissítik a kódokat. Az ügyfelek a választott szállítótól vásárolhatják meg ezeket a jogkivonatokat.

Az OATH TOTP hardvertokenek általában a jogkivonatban előre beprogramozott titkos kulccsal vagy maggal vannak beprogramozva. Ezeket a kulcsokat az Azure AD-be kell beírni az alábbi lépésekben leírtak szerint. A titkos kulcsok legfeljebb 128 karakterből állhatnak, ami nem minden jogkivonattal kompatibilis. A titkos kulcs csak az *a–z* vagy az *A–Z* karaktereket és *a 2–7* számjegyeket tartalmazhatja, és *Base32 kódolásúnak kell lennie.*

A szoftvertoken beállítási folyamatában az Azure AD-val is beállíthatók a programozható OATH TOTP hardvertokenek.

Az OATH hardvertokenek a nyilvános előzetes verzió részeként támogatottak. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![OATH-jogkivonatok feltöltése az MFA OATH tokenek panelre](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A jogkivonatok beszerzése után azokat fel kell tölteni egy vesszővel elválasztott értékeket (CSV) fájlformátumba, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt, az alábbi példában látható módon:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Ügyeljen arra, hogy a CSV-fájlban szerepeljen a fejlécsor. Ha egy EGYSZERŰN egyetlen idézőjelet is ad meg, egy másik egyszeres idézőjelet kell rá tenni. Ha például az UPN a my' , a fájl feltöltésekor módosítsa a következőre: user@domain.com user@domain.com my'' .

Miután megfelelően formázta a CSV-fájlt, a globális rendszergazda bejelentkezhet a Azure Portal-be, navigálhat az **Azure Active Directory > Security > MFA > OATH tokenek** lapra, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a feldolgozás eltarthat néhány percig. Válassza **a Frissítés gombot** az aktuális állapot lekért állapotához. Ha a fájlban hibák vannak, letölthet egy CSV-fájlt, amely felsorolja a megoldható hibákat. A letöltött CSV-fájl mezőinek nevei eltérnek a feltöltött verziótól.  

A hibák elhárítása után a rendszergazda úgy aktiválhatja az egyes kulcsokat, hogy kiválasztja az **Aktiválás** et a jogkivonathoz, és beírásával a jogkivonaton megjelenő egyszeri jelszó van beírásával. Legfeljebb 200 OATH jogkivonatot aktiválhat 5 percenként. 

A felhasználók legfeljebb öt OATH hardvertoken vagy hitelesítő alkalmazás, például a Microsoft Authenticator alkalmazás kombinációját konfigurálják bármikor használatra. A hardveres OATH jogkivonatok nem rendelhetők hozzá vendégfelhasználókhoz az erőforrás-bérlőben.

## <a name="next-steps"></a>Következő lépések

További információ a hitelesítési módszerek konfigurálásról a [Microsoft Graph REST API.](/graph/api/resources/authenticationmethods-overview)
