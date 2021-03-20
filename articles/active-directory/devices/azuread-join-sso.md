---
title: Hogyan működik az SSO a helyszíni erőforrásokkal az Azure AD-hez csatlakoztatott eszközökön | Microsoft Docs
description: Ismerje meg, hogyan bővítheti az egyszeri bejelentkezési élményt a hibrid Azure Active Directory csatlakoztatott eszközök konfigurálásával.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da22a4e5e9ab13ec18347e58bea6cfc5f45333de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630700"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>A helyszíni erőforrásokra történő egyszeri bejelentkezés működése Azure AD-be léptetett eszközökön

Valószínűleg nem meglepő, hogy egy Azure Active Directory (Azure AD) csatlakoztatott eszköz egyszeri bejelentkezéses (SSO) élményt biztosít a bérlő felhőalapú alkalmazásai számára. Ha a környezet helyszíni Active Directory (AD) szolgáltatással rendelkezik, az Azure AD-hez csatlakoztatott eszközökön is bejelentkezhet az egyszeri bejelentkezésre a helyszíni AD-t használó erőforrásokhoz és alkalmazásokhoz. 

Ez a cikk a működésének módját ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A helyszíni egyszeri bejelentkezéshez a helyszíni AD DS tartományvezérlőkkel való helyszíni kommunikáció szükséges. Ha az Azure AD-hez csatlakoztatott eszközök nem csatlakoznak a szervezet hálózatához, VPN-vagy egyéb hálózati infrastruktúrára van szükség. 

## <a name="how-it-works"></a>Működés 

Az Azure AD-hez csatlakoztatott eszközzel a felhasználók már rendelkeznek egyszeri bejelentkezéssel a környezetében lévő felhőalapú alkalmazásokhoz. Ha a környezet rendelkezik Azure AD-val és helyszíni AD-vel, érdemes kiterjesztenie az SSO-élmény hatókörét a helyszíni üzletági (LOB) alkalmazások, a fájlmegosztás és a nyomtatók számára.

Az Azure AD-hez csatlakoztatott eszközök nem rendelkeznek a helyszíni AD-környezettel kapcsolatos ismeretekkel, mivel azok nem csatlakoznak hozzá. A helyszíni AD-vel kapcsolatos további információkat azonban Azure AD Connect használatával is megadhat.

Ha hibrid környezettel rendelkezik, és az Azure AD-t és a helyszíni AD-t is, akkor valószínű, hogy már rendelkezik Azure AD Connect központilag, hogy szinkronizálja a helyszíni identitás adatait a felhőbe. A szinkronizálási folyamat részeként Azure AD Connect szinkronizálja a helyszíni felhasználói és tartományi információkat az Azure AD-be. Amikor egy felhasználó egy hibrid környezetben jelentkezik be egy Azure AD-hez csatlakoztatott eszközre:

1. Az Azure AD a felhasználó helyszíni tartományának adatait visszaküldi az eszközre az [elsődleges frissítési jogkivonattal](concept-primary-refresh-token.md) együtt.
1. A helyi biztonsági szervezet (LSA) szolgáltatás lehetővé teszi a Kerberos és az NTLM hitelesítés használatát az eszközön.

>[!NOTE]
> A vállalati Windows Hello használatához további konfiguráció szükséges, amely lehetővé teszi a helyszíni egyszeri bejelentkezést egy Azure AD-hez csatlakoztatott eszközről. További információ: az [Azure ad-hez csatlakoztatott eszközök konfigurálása helyszíni Single-Signhoz a Windows Hello for Business használatával](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

A Kerberost vagy NTLM-t kérő erőforráshoz való hozzáférési kísérlet során a felhasználó helyszíni környezetében az eszköz:

1. Elküldi a helyszíni tartományi adatokat és a felhasználói hitelesítő adatokat a helyi tartományvezérlőnek a hitelesített felhasználó beszerzéséhez.
1. Kerberos [-jegy (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) vagy NTLM-token fogadása a helyszíni erőforrás vagy alkalmazás által támogatott protokoll alapján. Ha a tartomány Kerberos-TGT vagy NTLM-jogkivonatának beolvasására tett kísérlet meghiúsul (a kapcsolódó DCLocator időtúllépése késleltetést okozhat), a Hitelesítőadat-kezelő bejegyzéseinek megkeresése vagy a felhasználó kaphat egy hitelesítési felugró ablakot, amely a cél erőforráshoz tartozó hitelesítő adatokat kér le.

Minden, a **Windows rendszerhez integrált hitelesítéshez** konfigurált alkalmazás ZÖKKENŐMENTESEN egyszeri bejelentkezést kap, amikor egy felhasználó megpróbál hozzáférni azokhoz.

## <a name="what-you-get"></a>Amihez jut

Az SSO-val egy Azure AD-hez csatlakoztatott eszközön a következőket teheti: 

- UNC elérési út elérése egy AD-tagkiszolgálón
- Hozzáférés a Windows rendszerhez integrált biztonsági szolgáltatáshoz konfigurált AD tag webkiszolgálóhoz 

Ha Windowsos eszközről szeretné felügyelni a helyszíni AD-t, telepítse a [Windows 10-es Távoli kiszolgálófelügyelet eszközei](https://www.microsoft.com/download/details.aspx?id=45520).

Az alábbi eszközöket használhatja:

- A Active Directory felhasználók és számítógépek (ADUC) beépülő modul az összes AD objektum felügyeletéhez. Azonban meg kell adnia azt a tartományt, amelyhez manuálisan szeretne csatlakozni.
- Az AD-hez csatlakoztatott DHCP-kiszolgáló felügyeletére szolgáló DHCP beépülő modul. Előfordulhat azonban, hogy meg kell adnia a DHCP-kiszolgáló nevét vagy címeit.
 
## <a name="what-you-should-know"></a>Alapismeretek

Előfordulhat, hogy módosítania kell a [tartományalapú szűrést](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) Azure ad Connect a szükséges tartományokkal kapcsolatos információk szinkronizálásának biztosítása érdekében.

A Active Directory számítógép-hitelesítéstől függő alkalmazások és erőforrások nem működnek, mert az Azure AD-hez csatlakoztatott eszközök nem rendelkeznek számítógép-objektummal az AD-ben. 

Egy Azure AD-hez csatlakoztatott eszközön nem oszthat meg fájlokat más felhasználókkal.

## <a name="next-steps"></a>Következő lépések

További információ: [Mi az Eszközkezelés a Azure Active Directory?](overview.md) 
