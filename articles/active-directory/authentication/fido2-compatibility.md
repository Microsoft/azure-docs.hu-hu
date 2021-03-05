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
ms.openlocfilehash: 7e5eb77a27a7c74ad9738a0e1784066a6a859e43
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175368"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>A FIDO2 jelszavas hitelesítésének böngésző általi támogatása

Azure Active Directory lehetővé teszi, hogy a [FIDO2 biztonsági kulcsait](./concept-authentication-passwordless.md#fido2-security-keys) jelszóval nem rendelkező eszközként lehessen használni. A Microsoft-fiókok FIDO2-hitelesítésének rendelkezésre állása [2018-ben jelent meg](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). A közleményben leírtaknak megfelelően a FIDO2 CTAP-specifikáció bizonyos opcionális funkcióit és bővítményeit úgy kell megvalósítani, hogy támogassák a biztonságos hitelesítést a Microsofttal és Azure Active Directory fiókokkal. Az alábbi ábra azt mutatja, hogy mely böngészők és operációsrendszer-kombinációk támogatják a jelszó-alapú hitelesítés használatát a Azure Active Directory FIDO2-hitelesítési kulcsaival.

## <a name="supported-browsers"></a>Támogatott böngészők

Ez a táblázat a Azure Active Directory (Azure AD) és a Microsoft-fiókok (MSA) hitelesítésének támogatását mutatja be. A Microsoft-fiókokat a felhasználók hozzák létre olyan szolgáltatásokhoz, mint például az Xbox, a Skype vagy a Outlook.com. A támogatott eszközök típusai közé tartozik az **USB**, a kis hatótávolságú **kommunikáció (****NFC**) és a Bluetooth alacsony energiafelhasználás.

| Operációs rendszer | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | GEDÉLYEZÉSE | USB | NFC | GEDÉLYEZÉSE | USB | NFC | GEDÉLYEZÉSE |
| **Windows**  | ![A Chrome támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Chrome támogatja az NFC használatát a Windows HRE-fiókokhoz.][y] | ![A Chrome támogatja a Windows for HRE-fiókokat.][y] | ![A Edge támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Edge támogatja az NFC használatát a Windows HRE-fiókokhoz.][y] | ![A Edge támogatja a Windows for HRE-fiókokat.][y] | ![A Firefox támogatja az USB-t a Windows HRE-fiókokhoz.][y] | ![A Firefox támogatja az NFC-t a Windows HRE-fiókokhoz.][y] | ![A Firefox támogatja a Windows rendszerhez készült HRE-fiókokat.][y] |
| **macOS**  | ![A Chrome támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][y] | ![A Chrome nem támogatja a macOS rendszerű HRE-fiókokhoz tartozó NFC használatát.][n] | ![A Chrome nem támogatja a HRE-fiókokhoz készült macOS-t.][n] | ![A Edge támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][y] | ![Az Edge nem támogatja az NFC használatát a macOS rendszerű HRE-fiókokhoz.][n] | ![Az Edge nem támogatja a macOS rendszerű HRE-fiókokat.][n] | ![A Firefox nem támogatja az USB-t a macOS rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja az NFC használatát a macOS rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja a macOS rendszerű HRE-fiókokat.][n] |
| **Linux**  | ![A Chrome támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][y] | ![A Chrome nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![A Chrome nem támogatja a HRE-fiókok Linux rendszeren való használatát.][n] | ![Az Edge nem támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][n] | ![Az Edge nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![Az Edge nem támogatja a Linux rendszerű HRE-fiókok használatát.][n] | ![A Firefox nem támogatja az USB-t Linux rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja az NFC használatát a Linux rendszeren a HRE-fiókokhoz.][n] | ![A Firefox nem támogatja a Linux rendszerű HRE-fiókok használatát.][n] |



## <a name="unsupported-browsers"></a>Nem támogatott böngészők

A következő operációsrendszer-és böngésző-kombinációk nem támogatottak, de a rendszer megvizsgálja a jövőbeli támogatást és tesztelést. Ha más operációs rendszerre és böngészőre vonatkozó támogatást szeretne látni, küldjön visszajelzést az oldal alján található termék-visszajelzési eszköz használatával.

| Operációs rendszer | Böngésző |
| ---- | ---- |
| iOS | Safari, bátor |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>A böngésző legalacsonyabb verziója

A böngésző minimális verziójára vonatkozó követelmények a következők: 

| Böngésző | Minimális verzió |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 1903-es verzió<sup>1</sup> |
| Firefox | Chrome |
| ChromeOS | 66 |

<sup>1</sup> Az új Chromium-alapú Microsoft Edge-támogatás Fido2 összes verziója. A Microsoft Edge örökölt támogatása a 1903-es verzióban lett hozzáadva.

## <a name="next-steps"></a>Következő lépések
[Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése (előzetes verzió)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
