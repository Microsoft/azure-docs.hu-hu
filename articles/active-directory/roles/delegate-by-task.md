---
title: Szerepkörök delegálása rendszergazdai feladat szerint – Azure Active Directory | Microsoft Docs
description: Az identitási feladatokhoz delegált szerepkörök Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d096bcd15254df4081a005b268934659e43037d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380304"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Rendszergazdai szerepkörök felügyeleti feladat szerint Azure Active Directory

Ebből a cikkből megtudhatja, hogy milyen információkra van szükség a felhasználók rendszergazdai engedélyeinek korlátozásához, ha a Azure Active Directory (Azure AD) szolgáltatásban a legkevésbé Kiemelt szerepköröket rendeli hozzá. Az egyes feladatok végrehajtásához szükséges rendszergazdai feladatokat és a minimálisan Kiemelt szerepkört, valamint a feladatot elvégző további nem globális rendszergazdai szerepköröket is megtalálja.

## <a name="application-proxy"></a>Alkalmazásproxy

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Alkalmazásproxy alkalmazásának konfigurálása | Alkalmazás-rendszergazda | 
Összekötő csoport tulajdonságainak konfigurálása | Alkalmazás-rendszergazda | 
Alkalmazás-regisztráció létrehozása, ha a képesség le van tiltva az összes felhasználónál | Alkalmazás-fejlesztő | Cloud Application Administrator, alkalmazás-rendszergazda
Összekötő-csoport létrehozása | Alkalmazás-rendszergazda | 
Összekötő-csoport törlése | Alkalmazás-rendszergazda | 
Alkalmazásproxy letiltása | Alkalmazás-rendszergazda | 
Összekötő szolgáltatás letöltése | Alkalmazás-rendszergazda | 
Az összes konfiguráció olvasása | Alkalmazás-rendszergazda | 

## <a name="external-identitiesb2c"></a>Külső identitások/B2C

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD B2C könyvtárak létrehozása | Minden nem vendég felhasználó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | 
B2C-alkalmazások létrehozása | Globális rendszergazda | 
Vállalati alkalmazások létrehozása | Felhőalkalmazás-rendszergazda | alkalmazás-rendszergazda
B2C-szabályzatok létrehozása, olvasása, frissítése és törlése | B2C IEF házirend rendszergazdája | 
Identitás-szolgáltatók létrehozása, olvasása, frissítése és törlése | Külső identitás-szolgáltató rendszergazdája | 
Jelszó-visszaállítási felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Külső azonosító – felhasználói folyamat rendszergazdája | 
Profil-szerkesztési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Külső azonosító – felhasználói folyamat rendszergazdája | 
Bejelentkezési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Külső azonosító – felhasználói folyamat rendszergazdája | 
Regisztrációs felhasználói folyamat létrehozása, olvasása, frissítése és törlése |Külső azonosító – felhasználói folyamat rendszergazdája | 
Felhasználói attribútumok létrehozása, olvasása, frissítése és törlése | Külső azonosító felhasználói flow-attribútumának rendszergazdája | 
Felhasználók létrehozása, olvasása, frissítése és törlése | Felhasználói rendszergazda
Az összes konfiguráció olvasása | Globális olvasó | 
B2C-naplók olvasása | Globális olvasó ([lásd a dokumentációt](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C globális olvasók nem rendelkeznek ugyanazzal az engedélyekkel, mint az Azure AD globális rendszergazdái. Ha Azure AD B2C globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg róla, hogy Azure AD B2C könyvtárban van, és nem Azure AD-címtár.

## <a name="company-branding"></a>Vállalati védjegyezés

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati arculat konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Vállalat tulajdonságai

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati tulajdonságok konfigurálása | Globális rendszergazda | 

## <a name="connect"></a>Csatlakozás

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Továbbító hitelesítés | Globális rendszergazda  | 
Az összes konfiguráció olvasása | Globális olvasó | Globális rendszergazda  |
Zökkenőmentes egyszeri bejelentkezés | Globális rendszergazda  | 

## <a name="cloud-provisioning"></a>Felhőbeli kiépítés

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Továbbító hitelesítés | Hibrid identitás-rendszergazda  | 
Az összes konfiguráció olvasása | Globális olvasó | Hibrid identitás-rendszergazda  |
Zökkenőmentes egyszeri bejelentkezés | Hibrid identitás-rendszergazda  | 

## <a name="connect-health"></a>Connect Health

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szolgáltatások hozzáadása vagy törlése | Tulajdonos ([lásd a dokumentációt](../hybrid/how-to-connect-health-operations.md)) | 
Hibajavítások alkalmazása szinkronizálási hiba esetén | Közreműködő ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Tulajdonos
Értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Tulajdonos
Beállítások konfigurálása | Tulajdonos ([lásd a dokumentációt](../hybrid/how-to-connect-health-operations.md)) | 
Szinkronizálási értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Tulajdonos
ADFS biztonsági jelentések beolvasása | Biztonsági olvasó | Közreműködő, tulajdonos
Az összes konfiguráció olvasása | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos
Szinkronizálási hibák olvasása | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatások olvasása | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatás metrikáinak és értesítéseinek megtekintése | Olvasó ([lásd a dokumentációt](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Közreműködő, tulajdonos

## <a name="custom-domain-names"></a>Egyéni tartománynevek

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Tartományok kezelése | Tartománynév-rendszergazda | 
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Tartományi szolgáltatások

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD Domain Services példány létrehozása | Globális rendszergazda | 
Az összes Azure AD Domain Services feladat végrehajtása | Azure AD DC-rendszergazdák csoport ([lásd a dokumentációt](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Az összes konfiguráció olvasása | AD DS szolgáltatást tartalmazó Azure-előfizetés olvasója | 

## <a name="devices"></a>Eszközök

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Eszköz letiltása | Felhőalapú eszköz rendszergazdája | 
Eszköz engedélyezése | Felhőalapú eszköz rendszergazdája | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | 
BitLocker-kulcsok olvasása | Biztonsági olvasó | Jelszó-rendszergazda, biztonsági rendszergazda

## <a name="enterprise-applications"></a>Vállalati alkalmazások

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Beleegyezik a delegált engedélyekkel | Cloud Application Administrator | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek belefoglalása nem tartalmazza a Microsoft Graph | Cloud Application Administrator | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek Microsoft Graphhoz való beleegyezett | Kiemelt szerepkörű rendszergazda | 
A saját adatokhoz hozzáférő alkalmazások beleegyezett | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | 
Vállalati alkalmazás létrehozása | Cloud Application Administrator | Alkalmazás-rendszergazda
Alkalmazásproxy kezelése | Alkalmazás-rendszergazda | 
Felhasználói beállítások kezelése | Globális rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának olvasása | Biztonsági olvasó | Biztonsági rendszergazda, felhasználói rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | 
Vállalati alkalmazás-hozzárendelések frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások tulajdonosainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás tulajdonságainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások üzembe helyezésének frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás önkiszolgáló frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda
Egyszeri bejelentkezési tulajdonságok frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Cloud Application Administrator, alkalmazás-rendszergazda

## <a name="entitlement-management"></a>Jogosultságkezelés
Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Erőforrások hozzáadása egy katalógushoz | Felhasználói rendszergazda | A jogosultságok kezelése szolgáltatással delegálhatja ezt a feladatot a katalógus tulajdonosának ([lásd a dokumentációt](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)).
SharePoint Online-webhelyek hozzáadása a katalógushoz | Globális rendszergazda


## <a name="groups"></a>Csoportok

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Felhasználói rendszergazda | 
Csoport létrehozása | Csoportok rendszergazdája | Felhasználói rendszergazda
Csoport vagy alkalmazás hozzáférési felülvizsgálatának létrehozása, frissítése vagy törlése | Felhasználói rendszergazda | 
Csoport lejáratának kezelése | Felhasználói rendszergazda | 
Csoportbeállítások kezelése | Csoportok rendszergazdája | Felhasználói rendszergazda | 
Az összes konfiguráció olvasása (kivéve a rejtett tagságot) | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md))
Rejtett tagság olvasása | Csoporttag | Csoport tulajdonosa, jelszó-rendszergazda, Exchange-rendszergazda, SharePoint-rendszergazda, csapat rendszergazdája, felhasználói rendszergazda
Rejtett tagsággal rendelkező csoportok tagságának olvasása | Segélyszolgálat rendszergazdája | Felhasználói rendszergazda, csapat rendszergazdája
Licenc visszavonása | Licencek rendszergazdája | Felhasználói rendszergazda
Csoporttagság frissítése | Csoport tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Felhasználói rendszergazda
Csoport tulajdonosainak frissítése | Csoport tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Felhasználói rendszergazda
Csoport tulajdonságainak frissítése | Csoport tulajdonosa ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | Felhasználói rendszergazda
Csoport törlése | Csoportok rendszergazdája | Felhasználói rendszergazda

## <a name="identity-protection"></a>Identity Protection

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Riasztási értesítések konfigurálása| Biztonsági rendszergazda | 
MFA-házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
A bejelentkezési kockázati házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
Felhasználói kockázati házirend konfigurálása és engedélyezése vagy letiltása | Biztonsági rendszergazda | 
Heti kivonatok konfigurálása | Biztonsági rendszergazda| 
Az összes kockázati észlelés bezárása | Biztonsági rendszergazda | 
Biztonsági rés javítása vagy bezárása | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Az összes kockázati észlelés olvasása | Biztonsági olvasó | 
Biztonsági rések beolvasása | Biztonsági olvasó | 

## <a name="licenses"></a>Licencek

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Licencek rendszergazdája | Felhasználói rendszergazda
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md))
Licenc visszavonása | Licencek rendszergazdája | Felhasználói rendszergazda
Előfizetés kipróbálása vagy megvásárlása | Számlázási adminisztrátor | 


## <a name="monitoring---audit-logs"></a>Figyelés – naplók

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Naplófájlok olvasása | Jelentések olvasója | Biztonsági olvasó, biztonsági rendszergazda

## <a name="monitoring---sign-ins"></a>Figyelés – bejelentkezések

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Bejelentkezési naplók olvasása | Jelentések olvasója | Biztonsági olvasó, biztonsági rendszergazda

## <a name="multi-factor-authentication"></a>Többtényezős hitelesítés

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
A kiválasztott felhasználók által létrehozott összes meglévő alkalmazás jelszavának törlése | Globális rendszergazda | 
MFA letiltása | Hitelesítési rendszergazda (PowerShell használatával) | Kiemelt jogosultságú hitelesítés rendszergazdája (PowerShell használatával)
MFA engedélyezése | Hitelesítési rendszergazda (PowerShell használatával) | Kiemelt jogosultságú hitelesítés rendszergazdája (PowerShell használatával) 
MFA szolgáltatás beállításainak kezelése | Hitelesítési házirend rendszergazdája | 
A kapcsolatfelvételi módszerek megadásának megkövetelése a kiválasztott felhasználóknak | Hitelesítés rendszergazdája | 
A többtényezős hitelesítés visszaállítása az összes megjegyzett eszközön  | Hitelesítés rendszergazdája | 

## <a name="mfa-server"></a>MFA-kiszolgáló

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók blokkolása/feloldása | Hitelesítési házirend rendszergazdája | 
Fiókzárolási konfigurálása | Hitelesítési házirend rendszergazdája | 
Gyorsítótárazási szabályok konfigurálása | Hitelesítési házirend rendszergazdája | 
Csalási riasztás konfigurálása | Hitelesítési házirend rendszergazdája
Értesítések konfigurálása | Hitelesítési házirend rendszergazdája | 
Egyszeri Mellőzés konfigurálása | Hitelesítési házirend rendszergazdája | 
Telefonhívás beállításainak konfigurálása | Hitelesítési házirend rendszergazdája | 
Szolgáltatók konfigurálása | Hitelesítési házirend rendszergazdája | 
Kiszolgálóbeállítások konfigurálása | Hitelesítési házirend rendszergazdája | 
Tevékenység jelentésének olvasása | Globális olvasó | 
Az összes konfiguráció olvasása | Globális olvasó | 
Kiszolgáló állapotának olvasása | Globális olvasó |  

## <a name="organizational-relationships"></a>Céges kapcsolatok

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Identitás-szolgáltatók kezelése | Külső identitás-szolgáltató rendszergazdája | 
Beállítások kezelése | Globális rendszergazda | 
Használati feltételek kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális olvasó | 

## <a name="password-reset"></a>Új jelszó létrehozása

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda |
Testreszabás konfigurálása | Globális rendszergazda |
Értesítés konfigurálása | Globális rendszergazda |
Helyszíni integráció konfigurálása | Globális rendszergazda |
Jelszó-visszaállítási tulajdonságok konfigurálása | Felhasználói rendszergazda | Globális rendszergazda
Regisztráció konfigurálása | Globális rendszergazda |
Az összes konfiguráció olvasása | Biztonsági rendszergazda | Felhasználói rendszergazda |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók társítása szerepkörökhöz | Kiemelt szerepkörű rendszergazda | 
Szerepkör-beállítások konfigurálása | Kiemelt szerepkörű rendszergazda | 
Naplózási tevékenység megtekintése | Biztonsági olvasó | 
Szerepkör-tagságok megtekintése | Biztonsági olvasó | 

## <a name="roles-and-administrators"></a>Szerepkörök és rendszergazdák

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szerepkör-hozzárendelések kezelése | Kiemelt szerepkörű rendszergazda | 
Azure AD-szerepkör hozzáférési felülvizsgálatának olvasása  | Biztonsági olvasó | Biztonsági rendszergazda, Kiemelt szerepkörű rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Biztonság – hitelesítési módszerek

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Jelszavas védelem konfigurálása | Biztonsági rendszergazda
Az intelligens zárolás konfigurálása | Biztonsági rendszergazda
Az összes konfiguráció olvasása | Globális olvasó | 

## <a name="security---conditional-access"></a>Biztonság – feltételes hozzáférés

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
MFA megbízható IP-címeinek konfigurálása | Feltételes hozzáférésű rendszergazda | 
Egyéni vezérlők létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Szabályzatok létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolat tanúsítványának törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat letiltása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Egyéni vezérlők kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Elnevezett helyeinek olvasása | Biztonsági olvasó | Feltételes hozzáférésű rendszergazda, biztonsági rendszergazda

## <a name="security---identity-security-score"></a>Biztonság – identitás biztonsági pontszáma

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök | 
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Biztonsági pontszám olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Esemény állapotának frissítése | Biztonsági rendszergazda | 

## <a name="security---risky-sign-ins"></a>Biztonság – kockázatos bejelentkezések

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Kockázatos bejelentkezések olvasása | Biztonsági olvasó | 

## <a name="security---users-flagged-for-risk"></a>Biztonság – a kockázatnak megjelölt felhasználók

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes esemény elvetését | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
A kockázatnak megjelölt felhasználók olvasása | Biztonsági olvasó | 

## <a name="users"></a>Felhasználók

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználó hozzáadása a címtárbeli szerepkörhöz | Kiemelt szerepkörű rendszergazda | 
Felhasználó felvétele a csoportba | Felhasználói rendszergazda | 
Licenc hozzárendelése | Licencek rendszergazdája | Felhasználói rendszergazda
Vendég felhasználó létrehozása | Vendég meghívója | Felhasználói rendszergazda
Felhasználó létrehozása | Felhasználói rendszergazda | 
Felhasználók törlése | Felhasználói rendszergazda | 
Korlátozott rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd a dokumentációt) | Felhasználói rendszergazda | 
Nem rendszergazdák frissítési tokenek érvénytelenítése (lásd a dokumentációt) | Jelszókezelő | Felhasználói rendszergazda
A Kiemelt rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd: dokumentáció) | Kiemelt jogosultságú hitelesítés rendszergazdája | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](../fundamentals/users-default-permissions.md) | 
Korlátozott rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Felhasználói rendszergazda | 
Nem rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Jelszókezelő | Felhasználói rendszergazda
Emelt szintű rendszergazdák jelszavának alaphelyzetbe állítása | Kiemelt jogosultságú hitelesítés rendszergazdája | 
Licenc visszavonása | Licencek rendszergazdája | Felhasználói rendszergazda
Az összes tulajdonság frissítése az egyszerű felhasználónév kivételével | Felhasználói rendszergazda | 
Egyszerű felhasználónevek frissítése korlátozott rendszergazdák számára (lásd: dokumentáció) | Felhasználói rendszergazda | 
Az egyszerű felhasználónév tulajdonság frissítése a Kiemelt rendszergazdáknál (lásd: dokumentáció) | Globális rendszergazda | 
Felhasználói beállítások frissítése | Globális rendszergazda | 
Hitelesítési módszerek frissítése | Hitelesítés rendszergazdája | Privilegizált hitelesítő rendszergazda, globális rendszergazda


## <a name="support"></a>Támogatás

Feladat | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Támogatási jegy beküldése | Szolgáltatás-rendszergazda | Alkalmazás-rendszergazda, Azure Information Protection rendszergazda, számlázási rendszergazda, Felhőbeli alkalmazás rendszergazdája, megfelelőségi rendszergazda, Dynamics 365-rendszergazda, asztali elemzési rendszergazda, Exchange-rendszergazda, jelszó-rendszergazda, Intune-rendszergazda, Skype vállalati rendszergazda, Power BI rendszergazda, privilegizált hitelesítő rendszergazda, SharePoint-rendszergazda, csapat kommunikációs rendszergazda, csapat rendszergazdája, felhasználói rendszergazda, munkahelyi elemzési rendszergazda

## <a name="next-steps"></a>Következő lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](permissions-reference.md)
