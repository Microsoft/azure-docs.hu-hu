---
title: Az Azure Active Directory (Azure AD) naplózott tevékenységeire vonatkozó referencia | Microsoft Docs
description: Tekintse át az Azure Active Directoryban (Azure AD) naplókban rögzíthető tevékenységeket.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f9976faf2a803e747bce8b851a3555e6d746b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89231146"
---
# <a name="azure-ad-audit-activity-reference"></a>Azure AD naplózott tevékenységekre vonatkozó referencia

A Azure Active Directory-(Azure AD-) jelentésekkel megtekintheti a környezete működésének meghatározásához szükséges információkat.

Az Azure AD jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenységjelentések** 
    - [Bejelentkezések](concept-sign-ins.md) – információkat biztosít a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról
    - [Naplók](concept-audit-logs.md) – az Azure ad-n belüli különböző szolgáltatások által végzett módosítások naplókban való nyomon követését teszi lehetővé. 
    
- **Biztonsági jelentések** 
    - [Kockázatos bejelentkezések](../identity-protection/overview-identity-protection.md) – a kockázatos bejelentkezés egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek. 
    - [Kockázatnak](../identity-protection/overview-identity-protection.md) kitett felhasználók – a kockázatos felhasználók egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült. 

Ez a cikk a naplókban rögzíthető tevékenységeket sorolja fel.

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

|Naplózási kategória|Tevékenység|
|---|---|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat befejezve|
|Hozzáférési felülvizsgálatok|Jóváhagyó hozzáadása a kérelem-jóváhagyáshoz|
|Hozzáférési felülvizsgálatok|Felülvizsgáló hozzáadása a hozzáférési felülvizsgálathoz|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat alkalmazása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat létrehozása|
|Hozzáférési felülvizsgálatok|Program létrehozása|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyás létrehozása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat törlése|
|Hozzáférési felülvizsgálatok|Program törlése|
|Hozzáférési felülvizsgálatok|Programvezérlő csatolása|
|Hozzáférési felülvizsgálatok|Azure AD hozzáférési felülvizsgálatok előkészítése|
|Hozzáférési felülvizsgálatok|Felülvizsgáló eltávolítása a hozzáférési felülvizsgálatból|
|Hozzáférési felülvizsgálatok|Felülvizsgálat leállításának kérése|
|Hozzáférési felülvizsgálatok|Felülvizsgálat-alkalmazás eredményének kérése|
|Hozzáférési felülvizsgálatok|RBAC-szerepkörtagság felülvizsgálata|
|Hozzáférési felülvizsgálatok|Alkalmazás-hozzárendelés felülvizsgálata|
|Hozzáférési felülvizsgálatok|Csoporttagság felülvizsgálata|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyási kérelem felülvizsgálata|
|Hozzáférési felülvizsgálatok|Programvezérlő leválasztása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat frissítése|
|Hozzáférési felülvizsgálatok|Az Azure AD hozzáférési felülvizsgálatok bevezetési állapotának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati e-mail-értesítési beállításainak frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési szám beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat ismétlődési időtartamának frissítése napokban beállítás|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati emlékeztető beállításainak frissítése|
|Hozzáférési felülvizsgálatok|Program frissítése|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyás frissítése|
|Hozzáférési felülvizsgálatok|Felhasználó letiltva|

## <a name="account-provisioning"></a>Fiók kiépítése

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|V2 alkalmazás engedélymegadásainak beolvasása|
|Alkalmazáskezelés|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|Alkalmazáskezelés|V1 alkalmazás frissítése|
|Alkalmazáskezelés|V2 alkalmazás frissítése|
|Alkalmazáskezelés|V2 alkalmazás engedélymegadásának frissítése|
|Alkalmazáskezelés|Oauth2Permissiongrant hozzáadása|
|Alkalmazáskezelés|Alkalmazásszerepkör-hozzárendelés hozzáadása a szolgáltatásnévhez|

## <a name="application-proxy"></a>Alkalmazásproxy

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Alkalmazás hozzáadása|
|Alkalmazáskezelés|Tulajdonos hozzáadása alkalmazáshoz|
|Alkalmazáskezelés|Tulajdonos hozzáadása szolgáltatásnévhez|
|Alkalmazáskezelés|Szabályzat hozzáadása szolgáltatásnévhez|
|Címtárkezelés|Szolgáltatásnév hozzáadása|
|Címtárkezelés|Szolgáltatásnév hitelesítő adatainak hozzáadása|
|Címtárkezelés|Hozzájárulás az alkalmazáshoz|
|Címtárkezelés|Alkalmazás törlése|
|Címtárkezelés|Alkalmazás végleges törlése|
|Címtárkezelés|Az Oauth2Permissiongrant eltávolítása|
|Címtárkezelés|Alkalmazásszerepkör-hozzárendelés eltávolítása a szolgáltatásnévből|
|Címtárkezelés|Tulajdonos eltávolítása az alkalmazásból|
|Erőforrás|Tulajdonos eltávolítása az egyszerű szolgáltatásnévből|
|Erőforrás|Szabályzat eltávolítása az egyszerű szolgáltatásnévből|
|Erőforrás|Szolgáltatásnév eltávolítása|


## <a name="automated-password-rollover"></a>Automatizált jelszóváltás

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Egyszerű szolgáltatásnév hitelesítő adatainak eltávolítása|


## <a name="b2c"></a>B2C

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Alkalmazás visszaállítása|
|Alkalmazáskezelés|Jóváhagyás visszavonása|
|Alkalmazáskezelés|Az alkalmazás frissítése|
|Alkalmazáskezelés|Külső titkos kódok frissítése|
|Alkalmazáskezelés|Szolgáltatásnév frissítése|
|Alkalmazáskezelés|Hozzáférési jogkivonat kiadása az alkalmazásnak|
|Alkalmazáskezelés|Hitelesítési kód kiadása az alkalmazásnak|
|Alkalmazáskezelés|id_token kiadása az alkalmazásnak|
|Alkalmazáskezelés|Helyi fiók hitelesítő adatainak ellenőrzése|
|Alkalmazáskezelés|Felhasználóhitelesítés ellenőrzése|
|Alkalmazáskezelés|V2 alkalmazásengedélyek hozzáadása|
|Alkalmazáskezelés|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|Alkalmazáskezelés|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|Alkalmazáskezelés|AdminPolicyDatas-SetResources|
|Alkalmazáskezelés|AdminUserJourneys-GetResources|
|Alkalmazáskezelés|AdminUserJourneys-RemoveResources|
|Hitelesítés|AdminUserJourneys-SetResources|
|Hitelesítés|IdentityProvider létrehozása|
|Hitelesítés|V1 alkalmazás létrehozása|
|Hitelesítés|V2 alkalmazás létrehozása|
|Hitelesítés|Egyéni tartomány létrehozása a bérlőben|
|Engedélyezés|Új AdminUserJourney létrehozása|
|Engedélyezés|Honosított json-erőforrás létrehozása|
|Engedélyezés|Új egyéni identitásszolgáltató létrehozása|
|Engedélyezés|Új identitásszolgáltató létrehozása|
|Engedélyezés|B2C címtárerőforrás létrehozása vagy frissítése|
|Engedélyezés|Szabályzat létrehozása|
|Engedélyezés|trustFramework szabályzat létrehozása|
|Engedélyezés|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|Engedélyezés|Felhasználói attribútum létrehozása|
|Engedélyezés|CreateTrustFrameworkPolicy|
|Engedélyezés|Új AdminUserJourney létrehozása vagy frissítése|
|Engedélyezés|Identitásszolgáltató törlése|
|Engedélyezés|IdentityProvider törlése|
|Engedélyezés|V1 alkalmazás törlése|
|Engedélyezés|V2 alkalmazás törlése|
|Engedélyezés|V2 alkalmazás engedélymegadásának törlése|
|Engedélyezés|B2C címtárerőforrás törlése|
|Engedélyezés|CPIM-kulcstároló törlése|
|Engedélyezés|trustFramework szabályzat törlése|
|Engedélyezés|Felhasználói attribútum törlése|
|Engedélyezés|B2C funkció engedélyezése|
|Engedélyezés|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|Engedélyezés|Egyéni identitásszolgáltató lekérése|
|Engedélyezés|Identitásszolgáltató lekérése|
|Engedélyezés|V1 és V2 alkalmazások lekérése|
|Engedélyezés|V1 alkalmazás lekérése|
|Engedélyezés|V1 alkalmazások lekérése|
|Engedélyezés|V2 alkalmazás lekérése|
|Engedélyezés|V2 alkalmazások lekérése|
|Engedélyezés|B2C-címtár erőforrásának beolvasása|
|Engedélyezés|A bérlőben lévő egyéni tartományok listájának lekérése|
|Engedélyezés|Felhasználói út lekérése|
|Engedélyezés|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|Engedélyezés|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|Engedélyezés|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|Engedélyezés|Elérhető kimeneti jogcímek listájának lekérése|
|Engedélyezés|A felhasználói út tartalomdefinícióinak lekérése|
|Engedélyezés|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|Engedélyezés|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|Engedélyezés|Az összes rendszergazdai folyamat|
|Engedélyezés|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|Engedélyezés|Felhasználó bérlői listájának lekérése|
|Engedélyezés|Helyi fiókok önellenőrző jogcímeinek lekérése|
|Engedélyezés|Honosított json-erőforrás lekérése|
|Engedélyezés|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|Engedélyezés|Szabályzatok lekérése|
|Engedélyezés|Szabályzat lekérése|
|Engedélyezés|Bérlő erőforrás-tulajdonságainak lekérése|
|Engedélyezés|Támogatott identitásszolgáltatók listájának lekérése|
|Engedélyezés|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|Engedélyezés|Bérlői adatok lekérése|
|Engedélyezés|Bérlő számára engedélyezett funkciók lekérése|
|Engedélyezés|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|Engedélyezés|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|Engedélyezés|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|Engedélyezés|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|Engedélyezés|Bérlők listájának lekérése|
|Engedélyezés|tenantDomains lekérése|
|Engedélyezés|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|Engedélyezés|Rendszergazdai folyamathoz tartozó információk lekérése|
|Engedélyezés|A bérlőhöz tartozó UserJourneys lista lekérése|
|Engedélyezés|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|Engedélyezés|TrustFramework szabályzat lekérése|
|Engedélyezés|TrustFramework szabályzat lekérése xml-ként|
|Engedélyezés|Felhasználói attribútum lekérése|
|Engedélyezés|Felhasználói attribútumok lekérése|
|Engedélyezés|Felhasználói utak listájának lekérése|
|Engedélyezés|GetIEFPolicies|
|Engedélyezés|GetIdentityProviders|
|Engedélyezés|GetTrustFrameworkPolicy|
|Engedélyezés|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|Engedélyezés|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|Engedélyezés|Lekérdezi a bérlő típusát|
|Engedélyezés|MigrateTenantMetadata|
|Engedélyezés|IdentityProvider javítása|
|Engedélyezés|PutTrustFrameworkPolicy|
|Engedélyezés|PutTrustFrameworkpolicy|
|Engedélyezés|Felhasználói út eltávolítása|
|Engedélyezés|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|Engedélyezés|V2 alkalmazás engedélymegadásainak beolvasása|
|Engedélyezés|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|Engedélyezés|Egyéni identitásszolgáltató frissítése|
|Engedélyezés|Identitásszolgáltató frissítése|
|Engedélyezés|Helyi identitásszolgáltató frissítése|
|Engedélyezés|V1 alkalmazás frissítése|
|Engedélyezés|V2 alkalmazás frissítése|
|Engedélyezés|V2 alkalmazás engedélymegadásának frissítése|
|Engedélyezés|Szabályzat frissítése|
|Engedélyezés|Felhasználói attribútum frissítése|
|Engedélyezés|CPIM titkosított kulcs feltöltése|
|Engedélyezés|Felhasználói hitelesítés: az API le van tiltva a bérlő funkciókészletéhez|
|Engedélyezés|Felhasználói hitelesítés: A felhasználó bérlői rendszergazdaként kap hozzáférést|
|Engedélyezés|Felhasználói hitelesítés: Felhasználó hitelesített felhasználóként kapott hozzáférési jogokat|
|Engedélyezés|B2C funkció engedélyezésének ellenőrzése|
|Engedélyezés|Annak ellenőrzése, hogy a szolgáltatás engedélyezve van-e|
|Engedélyezés|Program létrehozása|
|Engedélyezés|Program törlése|
|Engedélyezés|Programvezérlő csatolása|
|Engedélyezés|Azure AD hozzáférési felülvizsgálatok előkészítése|
|Engedélyezés|Programvezérlő leválasztása|
|Engedélyezés|Program frissítése|
|Engedélyezés|Asztali egyszeri bejelentkezés letiltása|
|Engedélyezés|Asztali egyszeri bejelentkezés letiltása adott tartományon|
|Engedélyezés|Alkalmazásproxy letiltása|
|Engedélyezés|Átmenő hitelesítés letiltása|
|Engedélyezés|Asztali egyszeri bejelentkezés engedélyezése|
|Címtárkezelés|Asztali egyszeri bejelentkezés engedélyezése adott tartományhoz|
|Címtárkezelés|Alkalmazásproxy engedélyezése|
|Címtárkezelés|Átmenő hitelesítés engedélyezése|
|Címtárkezelés|Egyéni tartomány létrehozása a bérlőben|
|Címtárkezelés|B2C funkció engedélyezése|
|Címtárkezelés|A bérlőben lévő egyéni tartományok listájának lekérése|
|Címtárkezelés|Bérlő erőforrás-tulajdonságainak lekérése|
|Címtárkezelés|Bérlői adatok lekérése|
|Címtárkezelés|Bérlő számára engedélyezett funkciók lekérése|
|Címtárkezelés|tenantDomains lekérése|
|Kulcs|Lekérdezi a bérlő típusát|
|Kulcs|B2C funkció engedélyezésének ellenőrzése|
|Kulcs|Annak ellenőrzése, hogy a szolgáltatás engedélyezve van-e|
|Kulcs|Partner hozzáadása vállalathoz|
|Kulcs|Nem ellenőrzött tartomány hozzáadása|
|Kulcs|Ellenőrzött tartomány hozzáadása|
|Kulcs|Vállalat létrehozása|
|Kulcs|Vállalati beállítások létrehozása|
|Kulcs|Vállalati beállítások törlése|
|Kulcs|Partner lefokozása|
|Kulcs|Címtár törölve|
|Egyéb|Címtár véglegesen törölve|
|Egyéb|Ez a címtár törlésre van ütemezve|
|Erőforrás|Vállalat előléptetése partnerré|
|Erőforrás|Rights Management-tulajdonságok végleges törlése|
|Erőforrás|Partner eltávolítása a vállalatból|
|Erőforrás|Nem ellenőrzött tartomány eltávolítása|
|Erőforrás|Ellenőrzött tartomány eltávolítása|
|Erőforrás|Cégadatok beállítása|
|Erőforrás|A DirSync funkció beállítása|
|Erőforrás|A DirSyncEnabled jelölő beállítása|
|Erőforrás|Partnerség beállítása|
|Erőforrás|Véletlen törlés küszöbértékének beállítása|
|Erőforrás|Vállalati adatok engedélyezett helyének beállítása|
|Erőforrás|Vállalati nemzetközi szolgáltatás engedélyezésének beállítása|
|Erőforrás|Címtárszolgáltatás beállítása a bérlőn|
|Erőforrás|Tartomány hitelesítésének beállítása|
|Erőforrás|Tartomány összevonási beállításainak beállítása|
|Erőforrás|Jelszószabályzat beállítása|
|Erőforrás|Rights Management tulajdonságainak beállítása|
|Erőforrás|Vállalat frissítése|
|Erőforrás|Vállalati beállítások frissítése|
|Erőforrás|Frissítési tartomány|
|Erőforrás|Tartomány ellenőrzése|
|Erőforrás|E-mailek ellenőrzött tartományának ellenőrzése|
|Erőforrás|Előkészítés|
|Erőforrás|Riasztási beállítások frissítése|
|Erőforrás|Heti összefoglaló beállításainak frissítése|
|Erőforrás|Jelszóvisszaíró letiltása a címtárhoz|
|Erőforrás|Jelszóvisszaíró engedélyezése a címtárhoz|
|Erőforrás|Alkalmazásszerepkör-hozzárendelés hozzáadása csoporthoz|
|Erőforrás|Csoport hozzáadása|
|Erőforrás|Tag hozzáadása csoporthoz|
|Erőforrás|Tulajdonos hozzáadása csoporthoz|
|Erőforrás|Csoportbeállítások létrehozása|
|Erőforrás|Csoport törlése|
|Erőforrás|Csoportbeállítások törlése|
|Erőforrás|Csoportalapú licenc felhasználókra való alkalmazásának befejezése|
|Erőforrás|Csoport végleges törlése|
|Erőforrás|Alkalmazás szerepkör-hozzárendelésének eltávolítása a csoportból|
|Erőforrás|Tag eltávolítása a csoportból|
|Erőforrás|Tulajdonos eltávolítása a csoportból|
|Erőforrás|Csoport visszaállítása|
|Erőforrás|Csoportlicenc beállítása|
|Erőforrás|Csoport beállítása felhasználó által kezeltként|
|Erőforrás|Csoportalapú licenc felhasználókra való alkalmazásának megkezdése|
|Erőforrás|Csoportlicenc újraszámolásának aktiválása|
|Erőforrás|Csoport frissítése|
|Erőforrás|Csoportbeállítások frissítése|
|Erőforrás|Tag hozzáadása|
|Erőforrás|Csoport létrehozása|
|Erőforrás|Csoport törlése|
|Erőforrás|Tag eltávolítása|
|Erőforrás|Csoport frissítése|
|Erőforrás|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem jóváhagyása|
|Erőforrás|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem visszavonása|
|Erőforrás|Életciklus-kezelési szabályzat létrehozása|
|Erőforrás|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem törlése|
|Erőforrás|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem elutasítása|
|Erőforrás|Csoport megújítása|
|Erőforrás|Csoporttagság kérése|
|Erőforrás|Dinamikus csoport tulajdonságainak beállítása|
|Erőforrás|Életciklus-kezelési szabályzat frissítése|
|Erőforrás|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|Erőforrás|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|Erőforrás|CPIM-kulcstároló törlése|
|Erőforrás|Kulcstároló törlése|
|Erőforrás|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|Erőforrás|Kulcstároló metaadatainak lekérése|
|Erőforrás|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|Erőforrás|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|Erőforrás|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|Erőforrás|Kulcstároló mentése|
|Erőforrás|CPIM titkosított kulcs feltöltése|
|Erőforrás|Hitelesítési kód kiadása az alkalmazásnak|
|Erőforrás|id_token kiadása az alkalmazásnak|


## <a name="core-directory"></a>Alapvető könyvtár

|Naplózási kategória|Tevékenység|
|---|---|
|Adminisztratív egységek kezelése|Egyetlen kockázati észlelési típus letöltése|
|Adminisztratív egységek kezelése|Rendszergazdák és a heti összefoglalók letöltésének jóváhagyása|
|Adminisztratív egységek kezelése|Az összes kockázati észlelési típus letöltése|
|Adminisztratív egységek kezelése|Ingyenes felhasználói kockázati észlelések letöltése|
|Adminisztratív egységek kezelése|Kockázatosként megjelölt felhasználók letöltése|
|Alkalmazáskezelés|Feldolgozott kötegelt meghívók|
|Alkalmazáskezelés|Kötegelt meghívók feltöltve|
|Alkalmazáskezelés|Tulajdonos hozzáadása szabályzathoz|
|Alkalmazáskezelés|Házirend hozzáadása|
|Alkalmazáskezelés|Szabályzat törlése|
|Alkalmazáskezelés|Szabályzat hitelesítő adatainak eltávolítása|
|Alkalmazáskezelés|Szabályzat frissítése|
|Alkalmazáskezelés|Az MFA regisztrációs szabályzatának beállítása|
|Alkalmazáskezelés|Bejelentkezési kockázati szabályzat beállítása|
|Alkalmazáskezelés|Felhasználói kockázati szabályzat beállítása|
|Alkalmazáskezelés|Használati feltételek elfogadása|
|Alkalmazáskezelés|Használati feltételek létrehozása|
|Alkalmazáskezelés|Használati feltételek elutasítása|
|Alkalmazáskezelés|Használati feltételek törlése|
|Alkalmazáskezelés|Használati feltételek szerkesztése|
|Alkalmazáskezelés|Használati feltételek közzététele|
|Alkalmazáskezelés|Használati feltételek közzétételének visszavonása|
|Alkalmazáskezelés|Alkalmazás TLS/SSL-tanúsítványának hozzáadása|
|Alkalmazáskezelés|TLS-kötés törlése|
|Alkalmazáskezelés|Összekötő regisztrálása|
|Alkalmazáskezelés|AdminPolicyDatas-RemoveResources|
|Alkalmazáskezelés|AdminPolicyDatas-SetResources|
|Alkalmazáskezelés|AdminUserJourneys-GetResources|
|Címtárkezelés|AdminUserJourneys-RemoveResources|
|Címtárkezelés|AdminUserJourneys-SetResources|
|Címtárkezelés|IdentityProvider létrehozása|
|Címtárkezelés|Új AdminUserJourney létrehozása|
|Címtárkezelés|Honosított json-erőforrás létrehozása|
|Címtárkezelés|Új egyéni identitásszolgáltató létrehozása|
|Címtárkezelés|Új identitásszolgáltató létrehozása|
|Címtárkezelés|B2C címtárerőforrás létrehozása vagy frissítése|
|Címtárkezelés|Szabályzat létrehozása|
|Címtárkezelés|trustFramework szabályzat létrehozása|
|Címtárkezelés|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|Címtárkezelés|Felhasználói attribútum létrehozása|
|Címtárkezelés|CreateTrustFrameworkPolicy|
|Címtárkezelés|Identitásszolgáltató törlése|
|Címtárkezelés|IdentityProvider törlése|
|Címtárkezelés|B2C címtárerőforrás törlése|
|Címtárkezelés|trustFramework szabályzat törlése|
|Címtárkezelés|Felhasználói attribútum törlése|
|Címtárkezelés|Erőforráscsoporthoz tartozó B2C címtárerőforrások lekérése|
|Címtárkezelés|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|Címtárkezelés|Egyéni identitásszolgáltató lekérése|
|Címtárkezelés|Identitásszolgáltató lekérése|
|Címtárkezelés|B2C-címtár erőforrásának beolvasása|
|Címtárkezelés|Felhasználói út lekérése|
|Címtárkezelés|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|Címtárkezelés|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|Címtárkezelés|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|Címtárkezelés|Elérhető kimeneti jogcímek listájának lekérése|
|Címtárkezelés|A felhasználói út tartalomdefinícióinak lekérése|
|Címtárkezelés|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|Címtárkezelés|Az összes rendszergazdai folyamat|
|Címtárkezelés|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|Csoportkezelés|Csoporttagok tömeges letöltése – elindítva|
|Csoportkezelés|Csoporttagok tömeges letöltése – kész|
|Csoportkezelés|Csoporttagok csoportos importálása – elindítva|
|Csoportkezelés|Csoporttagok tömeges importálása – kész|
|Csoportkezelés|Csoporttagok tömeges eltávolítása – elindítva|
|Csoportkezelés|Csoporttagok tömeges eltávolítása – kész|
|Csoportkezelés|Csoportos letöltési csoportok – elindítva|
|Csoportkezelés|Csoportos letöltési csoportok – kész|
|Csoportkezelés|Felhasználó bérlői listájának lekérése|
|Csoportkezelés|Helyi fiókok önellenőrző jogcímeinek lekérése|
|Csoportkezelés|Honosított json-erőforrás lekérése|
|Csoportkezelés|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|Csoportkezelés|Szabályzatok lekérése|
|Csoportkezelés|Szabályzat lekérése|
|Csoportkezelés|Támogatott identitásszolgáltatók listájának lekérése|
|Csoportkezelés|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|Csoportkezelés|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|Csoportkezelés|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|Csoportkezelés|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|Csoportkezelés|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|Csoportkezelés|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|Csoportkezelés|Rendszergazdai folyamathoz tartozó információk lekérése|
|Csoportkezelés|A bérlőhöz tartozó UserJourneys lista lekérése|
|Csoportkezelés|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|Csoportkezelés|TrustFramework szabályzat lekérése|
|Csoportkezelés|TrustFramework szabályzat lekérése xml-ként|
|Csoportkezelés|Felhasználói attribútum lekérése|
|Szabályzatkezelés|Felhasználói attribútumok lekérése|
|Szabályzatkezelés|Felhasználói utak listájának lekérése|
|Szabályzatkezelés|GetIEFPolicies|
|Szabályzatkezelés|GetIdentityProviders|
|Szabályzatkezelés|GetTrustFrameworkPolicy|
|Erőforrás|MigrateTenantMetadata|
|Erőforrás|Erőforrások áthelyezése|
|Erőforrás|IdentityProvider javítása|
|Erőforrás|PutTrustFrameworkPolicy|
|Erőforrás|PutTrustFrameworkpolicy|
|Erőforrás|Felhasználói út eltávolítása|
|Erőforrás|Egyéni identitásszolgáltató frissítése|
|Erőforrás|Identitásszolgáltató frissítése|
|Erőforrás|Helyi identitásszolgáltató frissítése|
|Erőforrás|B2C címtárerőforrás frissítése|
|Erőforrás|Szabályzat frissítése|
|Erőforrás|Előfizetés állapotának frissítése|
|Szerepkörkezelés|Felhasználói attribútum frissítése|
|Szerepkörkezelés|Erőforrások áthelyezésének ellenőrzése|
|Szerepkörkezelés|Eszköz hozzáadása|
|Szerepkörkezelés|Eszközkonfiguráció hozzáadása|
|Szerepkörkezelés|Regisztrált felhasználó hozzáadása eszközhöz|
|Szerepkörkezelés|Regisztrált felhasználók hozzáadása eszközhöz|
|Szerepkörkezelés|Eszköz törlése|
|Szerepkörkezelés|Eszköz konfigurációjának törlése|
|Szerepkörkezelés|Az eszköz már nem megfelelő|
|Szerepkörkezelés|Az eszköz már nincs felügyelve|
|Felhasználókezelés|AccessReview_Review|
|Felhasználókezelés|AccessReview_Update|
|Felhasználókezelés|ActivationAborted|
|Felhasználókezelés|ActivationApproved|
|Felhasználókezelés|ActivationCanceled|
|Felhasználókezelés|ActivationRequested|
|Felhasználókezelés|Jogosult tag hozzáadása szerepkörhöz|
|Felhasználókezelés|Tag hozzáadása szerepkörhöz|
|Felhasználókezelés|Szerepkör-hozzárendelés hozzáadása szerepkör-definícióhoz|
|Felhasználókezelés|Szerepkör hozzáadása sablonból|
|Felhasználókezelés|Hatókörön belüli tag hozzáadása a szerepkörhöz|
|Felhasználókezelés|Hozzáadva|
|Felhasználókezelés|Értékadás|
|Felhasználókezelés|Felhasználók tömeges létrehozása – elindítva|
|Felhasználókezelés|Felhasználók tömeges létrehozása – kész|
|Felhasználókezelés|Felhasználók tömeges törlése – elindítva|
|Felhasználókezelés|Felhasználók tömeges törlése – kész|
|Felhasználókezelés|Felhasználók tömeges letöltése – elindítva|
|Felhasználókezelés|Felhasználók tömeges letöltése – kész|
|Felhasználókezelés|Törölt felhasználók tömeges visszaállítása – elindítva|
|Felhasználókezelés|Törölt felhasználók tömeges visszaállítása – kész|
|Felhasználókezelés|Felhasználók tömeges meghívása – elindítva|
|Felhasználókezelés|Felhasználók tömeges meghívása – kész|
|Felhasználókezelés|Regisztrált felhasználó eltávolítása az eszközről|
|Felhasználókezelés|Regisztrált felhasználók eltávolítása az eszközről|
|Felhasználókezelés|Jogosult tag eltávolítása a szerepkörből|
|Felhasználókezelés|Tag eltávolítása szerepkörből|
|Felhasználókezelés|Szerepkör-hozzárendelés eltávolítása a szerepkör-definícióból|
|Felhasználókezelés|Hatókörön belüli tag eltávolítása a szerepkörből|
|Felhasználókezelés|Eszköz frissítése|
|Felhasználókezelés|Eszköz konfigurációjának frissítése|
|Felhasználókezelés|Szerepkör frissítése|






## <a name="identity-protection"></a>Identity protection

|Naplózási kategória|Tevékenység|
|---|---|
|Címtárkezelés|Jogosultságszint emelése|
|Címtárkezelés|Eltávolítva|
|Címtárkezelés|Szerepkör-beállítások módosításai|
|Egyéb|ScanAlertsNow|
|Egyéb|Regisztráció|
|Egyéb|Jogosultságszint csökkentése|
|Egyéb|UpdateAlertSettings|
|Egyéb|UpdateCurrentState|
|Szabályzatkezelés|Hozzáférési felülvizsgálat befejezve|
|Szabályzatkezelés|Jóváhagyó hozzáadása a kérelem-jóváhagyáshoz|
|Szabályzatkezelés|Felülvizsgáló hozzáadása a hozzáférési felülvizsgálathoz|
|Felhasználókezelés|Hozzáférési felülvizsgálat alkalmazása|
|Felhasználókezelés|Hozzáférési felülvizsgálat létrehozása|


## <a name="invited-users"></a>Meghívott felhasználók

|Naplózási kategória|Tevékenység|
|---|---|
|Egyéb|Kérelem-jóváhagyás létrehozása|
|Egyéb|Hozzáférési felülvizsgálat törlése|
|Felhasználókezelés|Felülvizsgáló eltávolítása a hozzáférési felülvizsgálatból|
|Felhasználókezelés|Felülvizsgálat-alkalmazás eredményének kérése|
|Felhasználókezelés|Felülvizsgálat leállításának kérése|
|Felhasználókezelés|Alkalmazás-hozzárendelés felülvizsgálata|
|Felhasználókezelés|Csoporttagság felülvizsgálata|
|Felhasználókezelés|RBAC-szerepkörtagság felülvizsgálata|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Naplózási kategória|Tevékenység|
|---|---|
|Csoportkezelés|Kérelem-jóváhagyási kérelem felülvizsgálata|
|Csoportkezelés|Hozzáférési felülvizsgálat frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálati e-mail-értesítési beállításainak frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálati ismétlődési szám beállításának frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálat ismétlődési időtartamának frissítése napokban beállítás|
|Felhasználókezelés|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Felhasználókezelés|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Naplózási kategória|Tevékenység|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Hozzáadva|
|PIM|AddedOutsidePIM|
|PIM|Értékadás|
|PIM|DismissAlert|
|PIM|Jogosultságszint emelése|
|PIM|ReactivateAlert|
|PIM|Eltávolítva|
|PIM|RemovedOutsidePIM|
|PIM|Felülvizsgálat leállításának kérése|
|PIM|Szerepkör-beállítások módosításai|
|PIM|ScanAlertsNow|
|PIM|Regisztráció|
|PIM|Szüntesse|
|PIM|Jogosultságszint csökkentése|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Önkiszolgáló csoportkezelés

|Naplózási kategória|Tevékenység|
|---|---|
|Csoportkezelés|Új felhasználói jelszó kérése|
|Csoportkezelés|Felhasználó visszaállítása|
|Csoportkezelés|Felhasználói jelszó kényszerített módosításának beállítása|
|Csoportkezelés|Felhasználókezelő beállítása|
|Csoportkezelés|A felhasználók oath token metaadatainak engedélyezettre állítása|
|Csoportkezelés|StsRefreshTokenValidFrom időbélyeg frissítése|
|Csoportkezelés|Külső titkos kódok frissítése|
|Csoportkezelés|Felhasználó frissítése|
|Csoportkezelés|A rendszergazda ideiglenes jelszót hoz létre|


## <a name="self-service-password-management"></a>Önkiszolgáló jelszókezelés

|Naplózási kategória|Tevékenység|
|---|---|
|Címtárkezelés|A rendszergazda kötelezővé teszi a felhasználó számára az új jelszó kérését|
|Címtárkezelés|Külső felhasználó hozzárendelése alkalmazáshoz|
|Felhasználókezelés|E-mail nincs elküldve, a felhasználó leiratkozott|
|Felhasználókezelés|Külső felhasználó meghívása|
|Felhasználókezelés|Külső felhasználó meghívásának beváltása|
|Felhasználókezelés|Bérlő ugrásszerű létrehozása|
|Felhasználókezelés|Felhasználó ugrásszerű létrehozása|
|Felhasználókezelés|Felhasználói jelszó regisztrációja|
|Felhasználókezelés|Új felhasználói jelszó kérése|
|Felhasználókezelés|Új jelszó önkiszolgáló kérésének blokkolása|


## <a name="terms-of-use"></a>Használati feltételek

|Naplózási kategória|Tevékenység|
|---|---|
|Használati feltételek|Használati feltételek elfogadása|
|Használati feltételek|Használati feltételek létrehozása|
|Használati feltételek|Használati feltételek elutasítása|
|Használati feltételek|Törlési engedély|
|Használati feltételek|Használati feltételek törlése|
|Használati feltételek|Használati feltételek szerkesztése|
|Használati feltételek|Használati feltételek lejárta|
|Használati feltételek|A használati feltételek nehezen törölhető|
|Használati feltételek|Használati feltételek közzététele|
|Használati feltételek|Használati feltételek közzétételének visszavonása|


## <a name="next-steps"></a>Következő lépések

- [Azure ad-jelentések – áttekintés](overview-reports.md).
- [Naplózott jelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)