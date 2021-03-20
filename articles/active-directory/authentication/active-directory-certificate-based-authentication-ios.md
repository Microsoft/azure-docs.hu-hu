---
title: Tanúsítványalapú hitelesítés iOS rendszeren – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és a tanúsítványalapú hitelesítés konfigurálásának követelményeit az iOS-eszközökkel rendelkező megoldások Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861323"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Tanúsítvány alapú hitelesítés Azure Active Directory iOS rendszeren

A biztonság növelése érdekében az iOS-eszközök tanúsítványalapú hitelesítést (CBA) használhatnak a Azure Active Directory (Azure AD) hitelesítésére az eszközön lévő ügyféltanúsítványt használva a következő alkalmazásokhoz vagy szolgáltatásokhoz való csatlakozáskor:

* Office Mobile-alkalmazások, például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync-(EAS-) ügyfelek

A tanúsítványok használatával nem kell megadnia a Felhasználónév és a jelszó kombinációját bizonyos mail-és Microsoft Office-alkalmazásokban a mobileszközön.

Ez a cikk a CBA iOS-eszközön történő konfigurálásának követelményeit és a támogatott forgatókönyveket ismerteti. Az iOS-hez készült CBA az Azure nyilvános felhők, a Microsoft Government Cloud, a Microsoft Cloud Germany és a Microsoft Azure China 21Vianet között érhető el.

## <a name="microsoft-mobile-applications-support"></a>Microsoft Mobile-alkalmazások támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Pipa jelzi az alkalmazás támogatását][1] |
| Intune Vállalati portál |![Pipa jelzi az alkalmazás támogatását][1] |
| Microsoft Teams |![Pipa jelzi az alkalmazás támogatását][1] |
| Office (mobil) |![Pipa jelzi az alkalmazás támogatását][1] |
| OneNote |![Pipa jelzi az alkalmazás támogatását][1] |
| OneDrive |![Pipa jelzi az alkalmazás támogatását][1] |
| Outlook |![Pipa jelzi az alkalmazás támogatását][1] |
| Power BI |![Pipa jelzi az alkalmazás támogatását][1] |
| Skype Vállalati verzió |![Pipa jelzi az alkalmazás támogatását][1] |
| Word/Excel/PowerPoint |![Pipa jelzi az alkalmazás támogatását][1] |
| Yammer |![Pipa jelzi az alkalmazás támogatását][1] |

## <a name="requirements"></a>Követelmények

A CBA iOS-sel való használatához a következő követelmények és szempontok érvényesek:

* Az eszköz operációsrendszer-verziójának iOS 9-es vagy újabb verziójúnak kell lennie.
* Az iOS-es Office-alkalmazásokhoz Microsoft Authenticator szükséges.
* Az identitás beállítását a macOS-kulcstartóban kell létrehozni, amely tartalmazza az ADFS-kiszolgáló hitelesítési URL-címét. További információ: [Identity preferencia létrehozása a kulcstartó-hozzáférés Mac gépen](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

A következő Active Directory összevonási szolgáltatások (AD FS) (ADFS) követelményei és szempontjai érvényesek:

* Az ADFS-kiszolgálónak engedélyezve kell lennie a tanúsítványalapú hitelesítéshez, és összevont hitelesítést kell használnia.
* A tanúsítványnak a kibővített kulcshasználat (EKU) használatát kell használnia, és tartalmaznia kell a felhasználó egyszerű felhasználónevét a *tulajdonos alternatív nevében (NT egyszerű név)*.

## <a name="configure-adfs"></a>Az ADFS konfigurálása

Ahhoz, hogy az Azure AD visszavonjon egy ügyféltanúsítványt, az ADFS-tokennek a következő jogcímeket kell tartalmaznia. Az Azure AD ezeket a jogcímeket hozzáadja a frissítési jogkivonathoz, ha az ADFS-tokenben (vagy bármely más SAML-jogkivonatban) elérhetők. Ha a frissítési tokent ellenőrizni kell, a rendszer ezt az információt használja a visszavonás ellenőrzéséhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` – az ügyféltanúsítvány sorozatszámának megadása
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -adja meg az ügyféltanúsítvány kiállítójának karakterláncát

Ajánlott eljárásként a szervezet ADFS-hibáinak oldalait is frissítenie kell a következő információkkal:

* A Microsoft Authenticator iOS rendszerre való telepítésének követelménye.
* Útmutató felhasználói tanúsítvány beszerzéséhez.

További információ: [AD FS bejelentkezési oldalának testreszabása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Modern hitelesítés használata az Office-alkalmazásokkal

A modern hitelesítéssel rendelkező Office-alkalmazások az Azure AD-be való küldésük `prompt=login` kérelmében vannak engedélyezve. Alapértelmezés szerint az Azure AD `prompt=login` az ADFS-re irányuló kérésben `wauth=usernamepassworduri` (az U/P hitelesítésének megadását kéri) és (az ADFS megkeresése az `wfresh=0` SSO-állapot mellőzése és új hitelesítés elvégzése). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítsa az alapértelmezett Azure AD-viselkedést.

Az alapértelmezett viselkedés frissítéséhez állítsa le a "*PromptLoginBehavior*" értéket az összevont tartomány beállításaiban a *letiltáshoz*. Ezt a feladatot a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) parancsmaggal hajthatja végre, ahogy az az alábbi példában is látható:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync-ügyfelek támogatása

Az iOS 9-es vagy újabb verzióiban a natív iOS levelezési ügyfélprogram támogatott. Annak megállapításához, hogy ez a funkció támogatott-e az összes többi Exchange ActiveSync-alkalmazás esetében, forduljon az alkalmazás-fejlesztőhöz.

## <a name="next-steps"></a>Következő lépések

A tanúsítványalapú hitelesítés konfigurálásához a környezetben tekintse meg a következő témakört: a [tanúsítványalapú hitelesítés](active-directory-certificate-based-authentication-get-started.md) első lépései.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
