---
title: Androidos tanúsítványalapú hitelesítés – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és a tanúsítványalapú hitelesítés konfigurálásának követelményeit Android-eszközökkel rendelkező megoldásokban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861373"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Tanúsítványalapú hitelesítés Azure Active Directory az Androidon

Az Android-eszközök tanúsítványalapú hitelesítést (CBA) használhatnak a Azure Active Directory hitelesítéséhez az eszközön lévő ügyféltanúsítványt használva a következőhöz való csatlakozáskor:

* Office Mobile-alkalmazások, például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync-(EAS-) ügyfelek

A szolgáltatás konfigurálásával nem kell megadnia a Felhasználónév és a jelszó kombinációját bizonyos levelezési és Microsoft Office alkalmazásokban a mobileszközön.

Ez a témakör az Office 365 Enterprise, a Business, az Education, az USA Government, a China és a Germany csomagokban lévő bérlők felhasználói számára készült Android-eszközökre vonatkozó követelményeket és támogatott forgatókönyveket ismerteti.

Ez a funkció előzetes verzióban érhető el az Office 365 US government Defense és Federal tervekben.

## <a name="microsoft-mobile-applications-support"></a>Microsoft Mobile-alkalmazások támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Pipa jelzi az alkalmazás támogatását][1] |
| Intune Vállalati portál |![Pipa jelzi az alkalmazás támogatását][1] |
| Microsoft Teams |![Pipa jelzi az alkalmazás támogatását][1] |
| OneNote |![Pipa jelzi az alkalmazás támogatását][1] |
| OneDrive |![Pipa jelzi az alkalmazás támogatását][1] |
| Outlook |![Pipa jelzi az alkalmazás támogatását][1] |
| Power BI |![Pipa jelzi az alkalmazás támogatását][1] |
| Skype Vállalati verzió |![Pipa jelzi az alkalmazás támogatását][1] |
| Word/Excel/PowerPoint |![Pipa jelzi az alkalmazás támogatását][1] |
| Yammer |![Pipa jelzi az alkalmazás támogatását][1] |

### <a name="implementation-requirements"></a>Megvalósítási követelmények

Az eszköz operációsrendszer-verziójának Android 5,0 (nyalóka) vagy újabb verziójúnak kell lennie.

Konfigurálni kell egy összevonási kiszolgálót.

Az ügyféltanúsítványok visszavonásához az ADFS-tokennek a következő jogcímeket kell tartalmaznia Azure Active Directory:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Az ügyféltanúsítvány sorozatszáma)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Az ügyféltanúsítvány kiállítójának karakterlánca)

Azure Active Directory hozzáadja ezeket a jogcímeket a frissítési jogkivonathoz, ha az ADFS-tokenben (vagy bármely más SAML-jogkivonatban) elérhetők. Ha a frissítési tokent ellenőrizni kell, a rendszer ezt az információt használja a visszavonás ellenőrzéséhez.

Ajánlott eljárásként frissítenie kell a szervezet ADFS-hibájának lapjait a következő információkkal:

* A Microsoft Authenticator telepítésének követelménye Android rendszeren.
* Útmutató felhasználói tanúsítvány beszerzéséhez.

További információ: [AD FS bejelentkezési lapok testreszabása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Egyes Office-alkalmazások (amelyeken engedélyezve van a modern hitelesítés) a kérésben a "*prompt = login*" üzenetet küldik az Azure ad-nek. Alapértelmezés szerint az Azure AD az ADFS-kérelemben *az "**wauth = usernamepassworduri*" (az U/P hitelesítésének megkövetelése) és a "*wfresh = 0*" (az ADFS-t az SSO-állapot mellőzése és a friss hitelesítés elvégzése érdekében kéri). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítania kell az Azure AD alapértelmezett viselkedését. Állítsa a "*PromptLoginBehavior*" beállítást az összevont tartomány beállításai között a "*Letiltva*" értékre.
A feladat végrehajtásához a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) parancsmagot használhatja:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatása

Bizonyos Exchange ActiveSync-alkalmazások az Android 5,0 (nyalóka) vagy újabb verziókban támogatottak. Annak megállapításához, hogy az e-mail-alkalmazás támogatja-e ezt a funkciót, forduljon az alkalmazás-fejlesztőhöz.

## <a name="next-steps"></a>Következő lépések

Ha tanúsítványalapú hitelesítést szeretne konfigurálni a környezetében, olvassa el a következő témakört: a [tanúsítványalapú hitelesítés első lépései Androidon](active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
