---
title: Bejelentkezések AD FS az Azure AD-ben a csatlakozási állapottal | Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan integrálható AD FS bejelentkezések a Azure AD Connect Health bejelentkezési jelentéssel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574790"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS bejelentkezések az Azure AD-ben a kapcsolat állapota – előzetes verzió

AD FS bejelentkezések mostantól integrálhatók a Azure Active Directory bejelentkezési jelentésbe a kapcsolati állapot használatával. Az [Azure ad bejelentkezési jelentései](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) jelentést tartalmaz arról, hogy amikor a felhasználók, az alkalmazások és a felügyelt erőforrások bejelentkeznek az Azure ad-be és a hozzáférés erőforrásaiba. 

A AD FS-ügynökhöz tartozó kapcsolódási állapot a kiszolgáló verziójától függően több AD FS eseményazonosító összekapcsolását teszi lehetővé a kérés és a hiba részleteinek megadása esetén, ha a kérelem meghiúsul. Ez az információ összefügg az Azure AD bejelentkezési jelentési sémájával, és megjelenik az Azure AD Sign-In-jelentés UX-ben. A jelentés mellett új Log Analytics Stream is elérhető a AD FS-és egy új Azure Monitor munkafüzet-sablonnal. A sablon használható és módosítható a részletes elemzésekhez olyan forgatókönyvek esetén, mint például a AD FS fiókzárolási, a helytelen jelszó-próbálkozások és a váratlan bejelentkezési kísérletek.

## <a name="prerequisites"></a>Előfeltételek
* A legújabb verzióra telepített és frissített AD FS Azure AD Connect Health.
* Globális rendszergazda vagy jelentések olvasó szerepkör az Azure AD-bejelentkezések megtekintéséhez

## <a name="what-data-is-displayed-in-the-report"></a>Milyen adatsorok jelennek meg a jelentésben?
A rendelkezésre álló adattükrök ugyanazok az Azure AD-bejelentkezésekhez elérhetőek. A bejelentkezés típusa alapján öt lap érhető el, amely az Azure AD vagy a AD FS. A összekapcsolási állapot összekapcsolja az eseményeket a AD FStól, a kiszolgáló verziójától függ, és megfelel a AD FS sémának. 



#### <a name="user-sign-ins"></a>Felhasználói bejelentkezések 
A bejelentkezések panel minden lapja az alábbi alapértelmezett értékeket jeleníti meg:
* Bejelentkezés dátuma
* Kérés azonosítója
* Felhasználónév vagy felhasználói azonosító
* A bejelentkezés állapota
* A bejelentkezéshez használt eszköz IP-címe
* Sign-In azonosítója

#### <a name="authentication-method-information"></a>Hitelesítési módszer adatai
A hitelesítés lapon a következő értékek jelenhetnek meg. A hitelesítési módszer az AD FS naplókból származik.

|Hitelesítési módszer|Leírás|
|-----|-----|
|Űrlapok|Felhasználónév/jelszó hitelesítése|
|Windows|Integrált Windows-hitelesítés|
|Tanúsítvány|Hitelesítés intelligens kártyával/VirtualSmart tanúsítvánnyal|
|WindowsHelloForBusiness|Ez a mező a Windows Hello for Business szolgáltatással történő hitelesítésre szolgál. (Microsoft Passport hitelesítés)|
|Eszköz | Akkor jelenik meg, ha az eszköz hitelesítése "elsődleges" hitelesítés az intranetről/extranetről és az eszköz hitelesítése folyamatban van.  Ebben az esetben nincs külön felhasználói hitelesítés.| 
|Összevont|AD FS nem hajtotta végre a hitelesítést, de eljuttatta azt egy harmadik féltől származó identitás-szolgáltatónak.|
|SSO |Egyszeri bejelentkezéses token használata esetén ez a mező jelenik meg. Ha az egyszeri bejelentkezés MFA-val rendelkezik, többtényezős formában jelenik meg|
|Többtényezős|Ha egy egyszeri bejelentkezési token MFA-val rendelkezik, és a hitelesítéshez használt, akkor ez a mező többtényezős formában jelenik meg.|
|Azure MFA|Az Azure MFA a AD FS további hitelesítési szolgáltatóként van kiválasztva, és a hitelesítéshez lett használva.|
|ADFSExternalAuthenticationProvider|Ez a mező abban az esetben, ha egy külső gyártótól származó hitelesítésszolgáltató regisztrálva van, és a hitelesítéshez használt|


#### <a name="ad-fs-additional-details"></a>AD FS további részletek
AD FS bejelentkezésekhez az alábbi adatok érhetők el:
* Server Name (Kiszolgáló neve)
* IP-lánc
* Protokoll

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics és Azure Monitor engedélyezése
Log Analytics engedélyezhető a AD FS bejelentkezésekhez, és bármely más, Log Analytics integrált összetevővel, például a Sentinel használatával használható.

> [!NOTE] 
> AD FS a bejelentkezések a szervezetében AD FS bejelentkezések mennyiségétől függően jelentősen növelhetik Log Analytics költségeket. Log Analytics engedélyezéséhez és letiltásához jelölje be a stream jelölőnégyzetét.

A szolgáltatás Log Analyticsának engedélyezéséhez navigáljon a Log Analytics panelre, és válassza a "ADFSSignIns" stream lehetőséget. Ez a beállítás lehetővé teszi, hogy AD FS bejelentkezések Log Analyticsba folynak.

A frissített Azure Monitor munkafüzet-sablon eléréséhez nyissa meg a "Azure Monitor sablonok" elemet, és válassza a "bejelentkezések" munkafüzetet.
A munkafüzetekkel kapcsolatos további információkért tekintse meg [Azure monitor munkafüzeteket](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Gyakori kérdések
***Milyen típusú bejelentkezéseket lehet látni?***
A bejelentkezési jelentés az O-Auth, a WS-fed, az SAML és a WS-Trust protokollok használatával támogatja a bejelentkezéseket. 

***Hogyan jelennek meg különböző típusú bejelentkezések a bejelentkezési jelentésben?***
Ha a rendszer zökkenőmentes egyszeri bejelentkezéses bejelentkezést végez, egy sor jelenik meg a bejelentkezéshez egy korrelációs AZONOSÍTÓval.
Ha egyetlen faktoros hitelesítést hajt végre, két sor lesz feltöltve ugyanazzal a korrelációs AZONOSÍTÓval, de két különböző hitelesítési módszerrel (például űrlapok, SSO).
A többtényezős hitelesítés esetében három sort kell megadnia egy megosztott korrelációs AZONOSÍTÓval, és három megfelelő hitelesítési módszert (például Forms, AzureMFA, többtényezős). Ebben az esetben a többtényezős példa azt mutatja, hogy az egyszeri bejelentkezés MFA-t tartalmaz.

***Melyek a jelentésekben látható hibák?***
A Sign-In jelentésben és a leírásokban feltöltött AD FS kapcsolódó hibák teljes listáját a következő témakörben találja: [AD FS Súgó hibakód-referenciája](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***A bejelentkezés "user" szakaszában "00000000-0000-0000-0000-000000000000" jelenik meg. az mit jelent?***
Ha a bejelentkezés sikertelen volt, és a megkísérelt UPN nem felel meg egy meglévő UPN-nek, a "user", a "username" és a "felhasználói azonosító" mezők "00000000-0000-0000-0000-000000000000", a "bejelentkezési azonosító" pedig a felhasználó által megadott megkísérelt értékkel lesz feltöltve. Ezekben az esetekben a bejelentkezni próbáló felhasználó nem létezik.

***Hogyan lehet összekapcsolni a helyszíni eseményeket az Azure AD bejelentkezési jelentésével?***
A AD FS Azure AD Connect Health ügynöke összekapcsolja az eseményazonosító AD FS a kiszolgáló verziójától függ. Az események a AD FS-kiszolgálók biztonsági Naplójánál lesznek elérhetők. 

***Miért látok NotSet vagy NotApplicable az alkalmazás-AZONOSÍTÓban vagy a névben egyes AD FS bejelentkezésekhez?***
A AD FS Sign-In jelentés megjeleníti a OAuth azonosítóit a OAuth-bejelentkezések alkalmazás-azonosító mezőjében. A WS-fed WS-Trust bejelentkezési forgatókönyvek esetében az alkalmazás azonosítója NotSet vagy NotApplicable, és az erőforrás-azonosítók és a függő entitások azonosítói megjelennek az erőforrás-azonosító mezőben.

***Van-e még több ismert probléma a jelentés előzetes verziójában?***
A jelentés egy ismert hibával rendelkezik, ahol az "alapinformációk" lap "hitelesítési követelmény" mezőjének egyetlen faktoros hitelesítési értéke lesz a bejelentkezéstől függetlenül AD FS bejelentkezésekhez. Emellett a hitelesítés részletei lap a követelmény mezőben az "elsődleges vagy másodlagos" feliratot jeleníti meg, és az elsődleges vagy másodlagos hitelesítési típusok megkülönböztetésére szolgáló javítás folyamatban van.


## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Kockázatos IP-jelentés](how-to-connect-health-adfs-risky-ip.md)





