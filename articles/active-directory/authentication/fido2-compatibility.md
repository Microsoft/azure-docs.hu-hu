---
title: A FIDO2 jelszavas hitelesítésének böngésző általi támogatása | Azure Active Directory
description: A böngészők és az operációsrendszer-kombinációk támogatják a FIDO2 jelszó-alapú hitelesítés használatát Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a8f96fabdff3543222077f5113b0bd602997b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417429"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>A FIDO2 jelszavas hitelesítésének böngésző általi támogatása

Azure Active Directory lehetővé teszi, hogy a [FIDO2 biztonsági kulcsait](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) jelszóval nem rendelkező eszközként lehessen használni. A Microsoft-fiókok FIDO2-hitelesítésének rendelkezésre állása [2018-ben jelent meg](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). A közleményben leírtaknak megfelelően a FIDO2 CTAP-specifikáció bizonyos opcionális funkcióit és bővítményeit úgy kell megvalósítani, hogy támogassák a biztonságos hitelesítést a Microsoft-és Azure Active Directory-fiókokkal. Az alábbi ábra azt mutatja, hogy mely böngészők és operációsrendszer-kombinációk támogatják a jelszó-alapú hitelesítés használatát a Azure Active Directory FIDO2-hitelesítési kulcsaival.

## <a name="supported-browsers"></a>Támogatott böngészők

Ez a táblázat a Azure Active Directory (Azure AD) és a Microsoft-fiókok (MSA) hitelesítésének támogatását mutatja be. A Microsoft-fiókokat a felhasználók hozzák létre olyan szolgáltatásokhoz, mint például az Xbox, a Skype vagy a Outlook.com. A támogatott eszközök típusai közé tartozik az **USB**, a kis hatótávolságú **kommunikáció (****NFC**) és a Bluetooth alacsony energiafelhasználás.

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | GEDÉLYEZÉSE | USB | NFC | GEDÉLYEZÉSE | USB | NFC | GEDÉLYEZÉSE |
| **Windows**  | ![A Chrome támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Chrome támogatja az NFC használatát a Windows HRE-fiókokhoz.][y] | ![A Chrome támogatja a Windows for HRE-fiókokat.][y] | ![A Edge támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Edge támogatja az NFC használatát a Windows HRE-fiókokhoz.][y] | ![A Edge támogatja a Windows for HRE-fiókokat.][y] | ![A Firefox támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Firefox támogatja az NFC-t a Windows HRE-fiókokhoz.][y] | ![A Firefox támogatja a Windows rendszerhez készült HRE-fiókokat.][y] |
| **macOS**  | ![A Chrome támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][y] | ![A Chrome nem támogatja a macOS rendszerű HRE-fiókokhoz tartozó NFC használatát.][n] | ![A Chrome nem támogatja a HRE-fiókokhoz készült macOS-t.][n] | ![A Edge támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][y] | ![Az Edge nem támogatja az NFC használatát a macOS rendszerű HRE-fiókokhoz.][n] | ![Az Edge nem támogatja a macOS rendszerű HRE-fiókokat.][n] | ![A Firefox nem támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja az NFC használatát a macOS rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja a macOS rendszerű HRE-fiókokat.][n] |
| **Linux**  | ![A Chrome támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][y] | ![A Chrome nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![A Chrome nem támogatja a HRE-fiókok Linux rendszeren való használatát.][n] | ![Az Edge nem támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][n] | ![Az Edge nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![Az Edge nem támogatja a Linux rendszerű HRE-fiókok használatát.][n] | ![A Firefox nem támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja a Linux rendszerű HRE-fiókok használatát.][n] |

## <a name="operating-system-versions-tested"></a>Tesztelt operációsrendszer-verziók

A fenti táblázatban szereplő információk az operációs rendszer következő verzióira lettek tesztelve.

| Operációs rendszer | Legújabb tesztelt verzió |
| --- | --- |
| Windows | Windows 10 20H2 1904 |
| macOS | OS X 11 Big Sur |
| Linux | Fedora 32 munkaállomás |

## <a name="next-steps"></a>Következő lépések
[Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
