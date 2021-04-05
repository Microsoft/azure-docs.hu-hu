---
title: Az aláírás-kivonatoló algoritmus módosítása Microsoft 365 függő entitás megbízhatóságához – Azure
description: Ez az oldal útmutatást nyújt az SHA-algoritmusnak a Microsoft 365sal való összevonási megbízhatósági kapcsolathoz való módosításához.
keywords: SHA1, SHA256, M365, összevonás, aadconnect, ADFS, AD FS, Change SHA, összevonási megbízhatóság, függő entitás megbízhatósága
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660921"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Az aláírás-kivonatoló algoritmus módosítása Microsoft 365 függő entitás megbízhatóságához
## <a name="overview"></a>Áttekintés
Active Directory összevonási szolgáltatások (AD FS) (AD FS) aláírja a tokeneit Microsoft Azure Active Directory, így biztosítva, hogy nem módosíthatók a használatával. Ez az aláírás SHA1-vagy SHA256-alapú lehet. A Azure Active Directory mostantól támogatja a SHA256 algoritmussal aláírt tokeneket, és azt javasoljuk, hogy a token-aláíró algoritmust a legmagasabb szintű biztonság SHA256 állítsa be. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a jogkivonat-aláíró algoritmust a biztonságosabb SHA256 szintre állítsa.

>[!NOTE]
>A Microsoft azt javasolja, hogy a SHA256 az aláírási tokenekhez használt algoritmusként használják, mivel az sokkal biztonságosabb, mint az SHA1, de az SHA1 továbbra is támogatott lehetőség marad.

## <a name="change-the-token-signing-algorithm"></a>Jogkivonat-aláíró algoritmus módosítása
Miután beállította az aláírási algoritmust az alábbi két folyamat egyikével, AD FS aláírja Microsoft 365 függő entitás megbízhatóságának jogkivonatait a SHA256. Nem kell további konfigurációs módosításokat végeznie, és ez a változás nem érinti a Microsoft 365 vagy más Azure AD-alkalmazások elérésére való képességet.

### <a name="ad-fs-management-console"></a>AD FS felügyeleti konzol
1. Nyissa meg az AD FS felügyeleti konzolt az elsődleges AD FS kiszolgálón.
2. Bontsa ki a AD FS csomópontot, és kattintson a **függő entitások megbízhatóságai** elemre.
3. Kattintson a jobb gombbal a Microsoft 365/Azure függő entitás megbízhatóságára, és válassza a **Tulajdonságok** lehetőséget.
4. Válassza a **speciális** lapot, és válassza ki a biztonságos KIVONATOLÓ algoritmus sha256.
5. Kattintson az **OK** gombra.

![SHA256-aláírási algoritmus – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>PowerShell-parancsmagok AD FS
1. Bármelyik AD FS-kiszolgálón nyissa meg a PowerShellt rendszergazdai jogosultságok alatt.
2. Állítsa be a biztonságos kivonatoló algoritmust a **set-AdfsRelyingPartyTrust** parancsmag használatával.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>További információk
* [Microsoft 365 megbízhatóság javítása Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

