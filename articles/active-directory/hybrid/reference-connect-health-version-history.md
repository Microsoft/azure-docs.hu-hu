---
title: Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)
description: Ez a dokumentum ismerteti a Azure AD Connect Health kiadásait, valamint azt, hogy mi szerepel a kiadásokban.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7653f850edc910fc78b14a628b87dcb22aeb903
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279414"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Version History (Az Azure AD Connect Health verzióelőzményei)
A Azure Active Directory csapat rendszeresen frissíti Azure AD Connect Health új szolgáltatásokkal és funkciókkal. Ez a cikk felsorolja a kiadott verziókat és szolgáltatásokat.  

> [!NOTE]
> A csatlakozás állapota automatikusan frissül, amikor megjelent az új verzió. Győződjön meg arról, hogy az automatikus frissítési beállítások engedélyezve vannak a Azure Portal.
>

A szinkronizálás Azure AD Connect Health integrálva van Azure AD Connect telepítéssel. További információ a szolgáltatással kapcsolatos visszajelzések [Azure ad Connect kiadási előzményeiről](./reference-connect-version-history.md) , szavazás a [kapcsolat állapota felhasználói hangcsatornán](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="april-2020"></a>2020. április
**Ügynök frissítése**

- AD FS-ügynök Azure AD Connect Health (verzió: 3.1.77.0)

   1.   A (z) AD FS szolgáltatáshoz tartozó egyszerű szolgáltatásnév (SPN) hibajavítása "riasztás, amely esetében a riasztás helytelenül lett bejelentve.


## <a name="july-2019"></a>2019. július
**Ügynök frissítése**
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.59.0) 
   1. Szöveg módosítása a TestWindowsTransport
   2. AD FS RP-feltöltés módosításai
   
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.56.0) 
   1. TestWindowsTransport-teszt hozzáadása és az WsTrust-végpontok ellenőrzésének eltávolítása a CheckOffice365Endpoints-tesztben
   2. Az operációs rendszer és a .NET-információk naplózása
   3. Növelje az RP-konfiguráció üzenetének feltöltési méretét 1MB-ra.
   4. Hibajavítások
   
* AD DS-ügynök Azure AD Connect Health (verzió: 3.1.56.0) 
   1. Az operációs rendszer és a .NET-információk naplózása 
   2. Hibajavítások

## <a name="may-2019"></a>2019. május
**Ügynök frissítése:** 
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.51.0) 
   1. Hibajavítás az azonos ügyfél-kérelem-azonosítóval rendelkező több bejelentkezés közötti különbségtételhez.
   2. Hibajavítás a helytelen felhasználónevek és jelszavak elemzéséhez a nyelvi honosított kiszolgálókon.   

## <a name="april-2019"></a>2019. április
**Ügynök frissítése:** 
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.46.0) 
   1. Az ADFS duplikált SPN-riasztási folyamatának javítása

## <a name="march-2019"></a>2019. március
**Ügynök frissítése:** 
* AD DS-ügynök Azure AD Connect Health (verzió: 3.1.41.0)  
   1. .NET-verziók gyűjteménye
   2. A teljesítményszámláló-gyűjtemények fejlesztése bizonyos kategóriák hiányában
   3. Hibajavítás a több figyelő ügynök példányainak ívásának megakadályozására

* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.41.0) 
   1. AD FS tesztelési parancsfájlok integrálása és frissítése a ADFSToolBox használatával
   2. .NET-verziók gyűjteményének implementálása
   3. A teljesítményszámláló-gyűjtemények fejlesztése bizonyos kategóriák hiányában
   4. Hibajavítás a több figyelő ügynök példányainak ívásának megakadályozására


## <a name="november-2018"></a>2018. november
**Új GA-funkciók:** 
* Azure AD Connect Health szinkronizáláshoz – duplikált attribútum-szinkronizálási hibák diagnosztizálása és szervizelése a portálról

**Ügynök frissítése:** 
* AD DS-ügynök Azure AD Connect Health (verzió: 3.1.24.0) 
   1. Transport Layer Security (TLS) protokoll 1,2-es verziójának megfelelősége és kényszerítése
   2. A globális katalógus riasztási zaj csökkentése
   3. Rendszerállapot-ügynök regisztrációja – hibajavítások

* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.24.0)  
   1. Transport Layer Security (TLS) protokoll 1,2-es verziójának megfelelősége és kényszerítése
   2. Test-ADFSRequestToken támogatása honosított operációs rendszerekhez
   3. Megoldott diagnosztikai ügynök EventHandler zárolási hibája
   4. Rendszerállapot-ügynök regisztrációja – hibajavítások

## <a name="august-2018"></a>2018. augusztus 
*  Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 3.1.7.0) szinkronizálásához (verzió: 1.1.880.0)    
   1. Gyorsjavítás a [felügyeleti ügynök magas CPU-problémája számára a .NET-keretrendszer tudásbázisának kiadásaival](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>2018. június 
**Új előzetes verziójú funkciók:** 
* Azure AD Connect Health szinkronizáláshoz – duplikált attribútum-szinkronizálási hibák diagnosztizálása és szervizelése a portálról 

**Ügynök frissítése:** 
* AD DS-ügynök Azure AD Connect Health (verzió: 3.1.7.0)    
  1. Gyorsjavítás a [felügyeleti ügynök magas CPU-problémája számára a .NET-keretrendszer tudásbázisának kiadásaival](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.7.0)  
  1. Gyorsjavítás a [felügyeleti ügynök magas CPU-problémája számára a .NET-keretrendszer tudásbázisának kiadásaival](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Eredmények javításai az ADFS-kiszolgálón 2016 másodlagos kiszolgálón
   
* AD FS-ügynök Azure AD Connect Health (verzió: 3.1.2.0)  
  1. Gyorsjavítás az ügynök memóriájának kezeléséhez és a kapcsolódó riasztásokhoz, kifejezetten a 3.0.244.0 verzióhoz


## <a name="may-2018"></a>2018. május
**Ügynök frissítése:**
* AD DS-ügynök Azure AD Connect Health (verzió: 3.0.244.0)
  1. Ügynök adatvédelem fejlesztése  
  2. Hibajavítások és általános javítások

* AD FS-ügynök Azure AD Connect Health (verzió: 3.0.244.0)
  1. Az Agent Diagnostics szolgáltatás és a kapcsolódó PowerShell-modulok fejlesztése
  2. Ügynök adatvédelem fejlesztése  
  3. Hibajavítások és általános javítások

* Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 3.0.164.0) szinkronizálásához (verzió: 1.1.819.0) 
  1. Ügynök adatvédelem fejlesztése  
  2. Hibajavítások és általános javítások


## <a name="march-2018"></a>2018. március
**Új előzetes verziójú funkciók:**
* Azure AD Connect Health AD FS kockázatos IP-jelentéshez és riasztáshoz.

**Ügynök frissítése:**

* AD DS-ügynök Azure AD Connect Health (verzió: 3.0.176.0)
  1. Az ügynök rendelkezésre állásának fejlesztése 
  2. Hibajavítások és általános javítások
* AD FS-ügynök Azure AD Connect Health (verzió: 3.0.176.0)
  1. Az ügynök rendelkezésre állásának fejlesztése 
  2. Hibajavítások és általános javítások
* Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 3.0.129.0) szinkronizálásához (verzió: 1.1.750.0)  
  1. Az ügynök rendelkezésre állásának fejlesztése 
  2. Hibajavítások és általános javítások

## <a name="december-2017"></a>2017. december
**Ügynök frissítése:**

* AD DS-ügynök Azure AD Connect Health (verzió: 3.0.145.0)
  1. Az ügynök rendelkezésre állásának fejlesztése 
  2. Új ügynökkel kapcsolatos hibaelhárítási parancsok hozzáadva
  3. Hibajavítások és általános javítások
* AD FS-ügynök Azure AD Connect Health (verzió: 3.0.145.0)
  1. Új ügynökkel kapcsolatos hibaelhárítási parancsok hozzáadva
  2. Az ügynök rendelkezésre állásának fejlesztése 
  3. Hibajavítások és általános javítások
  
## <a name="october-2017"></a>2017. október
**Ügynök frissítése:**

 * Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 3.0.129.0) szinkronizálásához (verzió: 1.1.649.0)
<br></br> Javítottuk a kompatibilitási problémát Azure AD Connect és Azure AD Connect Health ügynök között a szinkronizáláshoz. Ez a probléma azokat az ügyfeleket érinti, akik Azure AD Connect helyszíni frissítést végeznek a verzió 1.1.647.0, de jelenleg az állapotfigyelő 3.0.127.0 verziója van telepítve. A frissítés után az állapotfigyelő ügynök többé nem küldhet egészségügyi információkat Azure AD Connect szinkronizálási szolgáltatásról az Azure AD-Állapotfigyelő szolgáltatásba. Ezzel a javítással az állapotfigyelő ügynök verziója 3.0.129.0 van telepítve Azure AD Connect helyben történő frissítés során. Az állapotfigyelő ügynök 3.0.129.0 verziója nem rendelkezik kompatibilitási problémával a Azure AD Connect verzió 1.1.649.0.

## <a name="july-2017"></a>2017. július
**Ügynök frissítése:**

* AD DS-ügynök Azure AD Connect Health (verzió: 3.0.68.0)
  1. Hibajavítások és általános javítások
  2. Szuverén felhő támogatása
* AD FS-ügynök Azure AD Connect Health (verzió: 3.0.68.0)
  1. Hibajavítások és általános javítások
  2. Szuverén felhő támogatása
* Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 3.0.68.0) szinkronizálásához (verzió: 1.1.614.0)
  1. Microsoft Azure Government Cloud és Microsoft Cloud Germany támogatása

## <a name="april-2017"></a>2017. április      
**Ügynök frissítése:**

* AD FS-ügynök Azure AD Connect Health (verzió: 3.0.12.0)
  1. Hibajavítások és általános javítások
* AD DS-ügynök Azure AD Connect Health (verzió: 3.0.12.0)
  1. Teljesítményszámlálók feltöltésének fejlesztése
  2. Hibajavítások és általános javítások

## <a name="october-2016"></a>2016. október
**Ügynök frissítése:**

* AD FS-ügynök Azure AD Connect Health (verzió: 2.6.408.0)
* Az ügyfél IP-címeinek észlelése a hitelesítési kérelmekben – Újdonságok
* Riasztásokkal kapcsolatos hibajavítások
* AD DS-ügynök Azure AD Connect Health (verzió: 2.6.408.0)
* Riasztásokkal kapcsolatos hibajavítások.
* Azure AD Connect Health-ügynök a (Azure AD Connect verzióhoz tartozó 2.6.353.0) szinkronizálásához (verzió: 1.1.281.0)
* Adja meg a szinkronizálási hibajelentésekhez szükséges adatjelentéseket
* Riasztásokkal kapcsolatos hibajavítások

**Új előzetes verziójú funkciók:**

* Szinkronizálási hibajelentések Azure AD Connect

**Új funkciók:**

* A AD FS-IP-cím mező Azure AD Connect Health a hibás felhasználónévvel/jelszóval rendelkező leggyakoribb 50-felhasználók jelentésében érhető el.

## <a name="july-2016"></a>2016. július
**Új előzetes verziójú funkciók:**

* [AD DS Azure ad Connect Health](how-to-connect-health-adds.md).

## <a name="january-2016"></a>2016. január
**Ügynök frissítése:**

* AD FS-ügynök Azure AD Connect Health (verzió: 2.6.91.1512)

**Új funkciók:**

* [Azure AD Connect Health-ügynökökhöz tartozó kapcsolódási eszköz tesztelése](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015. november
**Új funkciók:**

* [Azure szerepköralapú hozzáférés-vezérlés támogatása (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Új előzetes verziójú funkciók:**

* [Azure ad Connect Health szinkronizáláshoz](how-to-connect-health-sync.md).

**Kijavított problémák:**

* Hibajavítások az ügynök regisztrációja során észlelt hibák esetén.

## <a name="september-2015"></a>2015. szeptember
**Új funkciók:**

* Helytelen felhasználónév-jelszó jelentés AD FS
* A nem hitelesített HTTP-proxy konfigurálásának támogatása
* Az ügynök konfigurálásának támogatása a Server Core-ban
* A AD FS riasztások fejlesztése
* A Azure AD Connect Health-ügynökkel kapcsolatos újdonságok a csatlakozás és az adatfeltöltés AD FS.

**Kijavított problémák:**

* Hibajavítások a használati adatokban AD FS a hibák típusaihoz.

## <a name="june-2015"></a>2015. június
**Azure AD Connect Health első kiadása AD FS és AD FS proxyhoz.**

**Új funkciók:**

* Riasztások az e-mail-értesítésekkel AD FS és AD FS proxykiszolgálók figyelésére.
* Könnyű hozzáférés AD FS topológia és minták AD FS teljesítményszámlálók számára.
* A sikeres jogkivonat-kérések trendje az alkalmazások, a hitelesítési módszerek, a hálózati hely kérése stb. szerint csoportosított AD FS-kiszolgálókon.
* A sikertelen kérések trendjei a AD FS-kiszolgálókon alkalmazások, rekordtípusok stb. szerint csoportosítva.
* Egyszerűbb ügynök üzembe helyezése az Azure AD globális rendszergazdai hitelesítő adataival.  

## <a name="next-steps"></a>Következő lépések
További információ a helyszíni [identitás-infrastruktúra és a szinkronizálási szolgáltatások Felhőbeli figyeléséről](./whatis-azure-ad-connect.md).