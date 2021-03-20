---
title: Közvetlen összevonás beállítása egy AD FS a B2B-Azure AD-hez
description: Ismerje meg, hogyan állíthatja be a közvetlen összevonás AD FS identitás-szolgáltatóként, hogy a vendégek be tudják jelentkezni az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909561"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Példa: közvetlen összevonás Active Directory összevonási szolgáltatások (AD FS) (AD FS) (előzetes verzió)

> [!NOTE]
> A közvetlen összevonás a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan állítható be a [közvetlen összevonás](direct-federation.md) Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával SAML 2,0 vagy WS-Fed Identity Provider néven. A közvetlen összevonás támogatásához bizonyos attribútumokat és jogcímeket konfigurálni kell az identitás-szolgáltatón. Ha szeretné bemutatni, hogyan konfigurálhat egy identitás-szolgáltatót a közvetlen összevonáshoz, használja a Active Directory összevonási szolgáltatások (AD FS) (AD FS) példaként. Bemutatjuk, hogyan állíthat be AD FS mindkettőt SAML-identitás-szolgáltatóként, és WS-Fed-azonosítóként.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan állítható be az SAML és a WS-Fed AD FS az illusztrációs célokra. A közvetlen összevonási integrációk esetében, ahol az Identitáskezelő AD FS, javasoljuk, hogy a WS-Fed használja protokollként. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>AD FS konfigurálása SAML 2,0 közvetlen összevonás esetén
Az Azure AD B2B konfigurálható úgy, hogy az SAML protokollt használó összevonása az alább felsorolt konkrét követelményekkel. Az SAML-konfiguráció lépéseinek szemléltetéséhez ez a szakasz bemutatja, hogyan állíthat be AD FS az SAML 2,0-hez. 

Közvetlen összevonás beállításához a következő attribútumoknak kell szerepelniük az SAML 2,0-válaszban az identitás-szolgáltatótól. Ezek az attribútumok konfigurálhatók az online biztonsági jogkivonat szolgáltatás XML-fájljához való csatolással, vagy manuálisan is. A [test AD FS-példány létrehozása](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) című 12. lépés leírja, hogyan lehet megkeresni a AD FS végpontokat vagy a metaadatok URL-címét, például: `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner identitásszolgáltató kiállítói URI-ja, például: `http://www.example.com/exk10l6w90DHM0yi...`         |

A következő jogcímeket az Identitáskezelő által kiállított SAML 2,0-tokenben kell konfigurálni:


|Attribútum  |Érték  |
|---------|---------|
|NameID formátuma     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A következő szakasz bemutatja, hogyan konfigurálhatja a szükséges attribútumokat és jogcímeket AD FS használatával példaként egy SAML 2,0-identitást szolgáltatóként.

### <a name="before-you-begin"></a>Előkészületek

Az eljárás megkezdése előtt már be kell állítania egy AD FS-kiszolgálót, és működnie kell. AD FS-kiszolgáló beállításával kapcsolatos segítségért lásd: [teszt AD FS 3,0-példány létrehozása Azure-beli virtuális gépen](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Jogcím leírásának hozzáadása

1. A AD FS-kiszolgálón válassza az **eszközök**  >  **AD FS felügyelet** lehetőséget.
2. A navigációs ablaktáblán válassza ki a **szolgáltatási**  >  **jogcímek leírását**.
3. A **műveletek** területen válassza a **jogcím hozzáadása leírást**.
4. A **jogcím leírásának hozzáadása** ablakban adja meg a következő értékeket:

   - **Megjelenítendő név**: állandó azonosító
   - **Jogcím azonosítója**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Jelölje be a **jogcím leírásának közzététele az összevonási metaadatokban jelölőnégyzetet az összevonási szolgáltatás által elfogadható jogcím-típusként**.
   - Jelölje be a **jogcím leírásának közzétételére szolgáló jelölőnégyzetet az összevonási metaadatokban az összevonási szolgáltatás által elküldhető jogcím típusaként**.

5. Kattintson az **OK** gombra.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatóságának és a jogcím szabályainak hozzáadása

1. A AD FS-kiszolgálón lépjen az **eszközök**  >  **AD FS felügyelet** elemre.
2. A navigációs ablaktáblán válassza a **megbízhatósági kapcsolatok** függő entitások  >  **megbízhatóságai** lehetőséget.
3. A **műveletek** területen válassza a **függő entitás megbízhatóságának hozzáadása** lehetőséget. 
4. A függő entitás megbízhatóságának hozzáadása varázslóban az **adatforrás kiválasztásához** használja az **adatimportálás lehetőséget a függő entitás online vagy helyi hálózaton közzétett adatainak importálása** lehetőséggel. Az összevonási metaadatok URL-címének megadása – https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Hagyja meg az egyéb alapértelmezett beállításokat. Válassza a **Bezárás** gombot.
5. Megnyílik a **jogcím-szabályok szerkesztése** varázsló.
6. A **jogcím szabályainak szerkesztése** varázslóban válassza a **szabály hozzáadása** elemet. A **szabály típusának kiválasztása** területen válassza az **LDAP-attribútumok küldése jogcímként** lehetőséget. Kattintson a **Tovább** gombra.
7. A **jogcím beállítása szabályban** adja meg a következő értékeket: 

   - **Jogcím-szabály neve**: e-mail-jogcím szabálya 
   - **Attribútum-tároló**: Active Directory 
   - **LDAP-attribútum**: E-mail-címek 
   - **Kimenő jogcím típusa**: E-mail cím

8. Válassza a **Befejezés** gombot.
9. A **jogcímek szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra. 
10. Kattintson az **OK** gombra.  

### <a name="create-an-email-transform-rule"></a>E-mail átalakítási szabály létrehozása
1. Lépjen a **jogcím szabályainak szerkesztése** elemre, majd kattintson a **szabály hozzáadása** lehetőségre. A **szabály típusának kiválasztása** területen válassza a **bejövő jogcím átalakítása** lehetőséget, majd kattintson a **tovább** gombra. 
2. A **jogcím beállítása szabályban** adja meg a következő értékeket: 

   - **Jogcím-szabály neve**: e-mail-átalakítási szabály 
   - **Bejövő jogcím típusa**: E-mail cím 
   - **Kimenő jogcím típusa**: név azonosítója 
   - **Kimenő név azonosítójának formátuma**: állandó azonosító 
   - Válassza **Az összes jogcímérték továbbítása** lehetőséget.

3. Kattintson a **Finish** (Befejezés) gombra. 
4. A **jogcímek szerkesztése** ablakban megjelennek az új szabályok. Kattintson az **Alkalmaz** gombra. 
5. Kattintson az **OK** gombra. A AD FS-kiszolgáló most már az SAML 2,0 protokollal történő közvetlen összevonás használatára van konfigurálva.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>AD FS konfigurálása WS-Fed közvetlen összevonás számára 
Az Azure AD B2B konfigurálható úgy, hogy az WS-Fed protokollt használó összevonása az alább felsorolt konkrét követelményekkel. A két WS-Fed-szolgáltató jelenleg az Azure AD-vel való kompatibilitásra lett tesztelve AD FS és Shibboleth is. Itt Active Directory összevonási szolgáltatások (AD FS) (AD FS) példaként fogunk használni a WS-Fed-identitás szolgáltatójának. Ha további információra van szüksége a függő entitás megbízhatóságának az Azure AD-vel való WS-Fed megfelelőségének létrehozásáról, töltse le az Azure AD Identity Provider kompatibilitási dokumentációját.

A közvetlen összevonás beállításához a következő attribútumokat kell megkapnia az Identitáskezelő WS-Fed üzenetében. Ezek az attribútumok konfigurálhatók az online biztonsági jogkivonat szolgáltatás XML-fájljához való csatolással, vagy manuálisan is. A [test AD FS-példány létrehozása](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) című 12. lépés leírja, hogyan lehet megkeresni a AD FS végpontokat vagy a metaadatok URL-címét, például: `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner identitásszolgáltató kiállítói URI-ja, például: `http://www.example.com/exk10l6w90DHM0yi...`         |

A identitásszolgáltató által kiadott WS-Fed jogkivonat számára szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A következő szakasz azt szemlélteti, hogyan konfigurálhatja a szükséges attribútumokat és jogcímeket a AD FS használatával WS-Fed-identitás-szolgáltató példája.

### <a name="before-you-begin"></a>Előkészületek
Az eljárás megkezdése előtt már be kell állítania egy AD FS-kiszolgálót, és működnie kell. AD FS-kiszolgáló beállításával kapcsolatos segítségért lásd: [teszt AD FS 3,0-példány létrehozása Azure-beli virtuális gépen](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatóságának és a jogcím szabályainak hozzáadása 
1. A AD FS-kiszolgálón lépjen az **eszközök**  >  **AD FS felügyelet** elemre. 
1. A navigációs ablaktáblán válassza a **megbízhatósági kapcsolatok** függő entitások  >  **megbízhatóságai** lehetőséget. 
1. A **műveletek** területen válassza a **függő entitás megbízhatóságának hozzáadása** lehetőséget.  
1. A függő entitás megbízhatóságának hozzáadása varázslóban az **adatforrás kiválasztásához** használja a **függő entitás online vagy helyi hálózaton közzétett adatainak importálása** lehetőséget. Itt adhatja meg az összevonási metaadatok URL-címét: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Hagyja meg az egyéb alapértelmezett beállításokat. Válassza a **Bezárás** gombot.
1. Megnyílik a **jogcím-szabályok szerkesztése** varázsló. 
1. A **jogcím szabályainak szerkesztése** varázslóban válassza a **szabály hozzáadása** elemet. A **szabály típusának kiválasztása** területen válassza **a jogcímek küldése egyéni szabállyal** lehetőséget. Kattintson a *Tovább* gombra. 
1. A **jogcím beállítása szabályban** adja meg a következő értékeket:

   - **Jogcím-szabály neve**: nem módosítható azonosító kiadása  
   - **Egyéni szabály**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Válassza a **Befejezés** gombot. 
1. A **jogcímek szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra.  
1. A **jogcímek szerkesztése** varázslóban válassza a **szabály hozzáadása** elemet. A összetartozó **szabály típusa** területen válassza az **LDAP-attribútumok küldése jogcímként** lehetőséget. Kattintson a **Tovább** gombra.
1. A **jogcím beállítása szabályban** adja meg a következő értékeket: 

   - **Jogcím-szabály neve**: e-mail-jogcím szabálya  
   - **Attribútum-tároló**: Active Directory  
   - **LDAP-attribútum**: E-mail-címek  
   - **Kimenő jogcím típusa**: E-mail cím 

1.  Válassza a **Befejezés** gombot. 
1.  A **jogcímek szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra.  
1.  Kattintson az **OK** gombra. A AD FS-kiszolgáló mostantól a WS-fed használatával történő közvetlen összevonás használatára van konfigurálva.

## <a name="next-steps"></a>Következő lépések
Ezután konfigurálja az Azure [ad közvetlen összevonását](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) az Azure ad-portálon vagy a PowerShell használatával. 
