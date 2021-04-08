---
title: Azure AD Domain Services a Cloud Solution Providers szolgáltatáshoz | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti és kezelheti Azure Active Directory Domain Services felügyelt tartományokat az Azure Cloud Solution-szolgáltatók számára
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619899"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Domain Services üzembe helyezés és felügyelet az Azure Cloud Solution Providers szolgáltatásban

Az Azure Cloud Solution Providers (CSP) egy Microsoft-partneri program, amely a különböző Microsoft Cloud Services-szolgáltatásokhoz biztosít licenc-csatornát. Az Azure CSP lehetővé teszi a partnerek számára az értékesítések kezelését, a számlázási kapcsolat megadását, a technikai és számlázási támogatás biztosítását, valamint az ügyfél egyetlen kapcsolattartási pontját. Emellett az Azure CSP teljes körű eszközkészletet biztosít, beleértve az önkiszolgáló portált és a kísérő API-kat. Ezek az eszközök lehetővé teszik a CSP-partnerek számára az Azure-erőforrások egyszerű üzembe helyezését és kezelését, valamint az ügyfelek és előfizetésük számlázását.

A [partner Center portál](/partner-center/azure-plan-lp) az összes Azure CSP-partner belépési pontja, és sokoldalú ügyfél-felügyeleti képességeket, automatizált feldolgozást és egyebeket biztosít. Az Azure CSP-partnerek webalapú felhasználói felületen vagy a PowerShell és különféle API-hívások használatával használhatják a partner Center-képességeket.

A következő ábra azt szemlélteti, hogy a CSP-modell hogyan működik magas szinten. Itt a contoso Azure Active Directory (Azure AD) Bérlővel rendelkezik. Rendelkeznek egy olyan CSP-vel, amely az Azure CSP-előfizetésében üzembe helyezi és kezeli az erőforrásokat. A contoso rendszeres (közvetlen) Azure-előfizetésekkel is rendelkezhet, amelyek számlázása közvetlenül a contoso számára történik.

![A CSP-modell áttekintése](./media/csp/csp_model_overview.png)

A CSP-partner bérlője három speciális ügynök csoporttal rendelkezik – a *rendszergazdai* ügynökökkel, az *ügyfélszolgálati* ügynökökkel és az *értékesítési* ügynökökkel.

A *felügyeleti* ügynökök csoport a contoso Azure ad-bérlőben a bérlői rendszergazda szerepkörhöz van rendelve. Ennek eredményeképpen a CSP-partner rendszergazdai ügynökök csoportjába tartozó felhasználónak van bérlői rendszergazdai jogosultsága a contoso Azure AD-bérlőben.

Ha a CSP-partner kiépíti az Azure CSP-előfizetést a contoso számára, akkor az adott előfizetéshez tartozó tulajdonosi szerepkörhöz hozzá van rendelve a rendszergazdai ügynökök csoport. Ennek eredményeképpen a CSP-partner rendszergazdai ügynökei rendelkeznek a szükséges jogosultságokkal az Azure-erőforrások, például virtuális gépek, virtuális hálózatok és Azure AD Domain Services a contoso nevében történő kiépítéséhez.

További információ: [Azure CSP – áttekintés](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Az Azure AD DS Azure CSP-előfizetéssel való használatának előnyei

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directory Domain Servicesekkel. Az évtizedek során számos alkalmazás lett létrehozva az AD-vel való együttműködésre ezen képességek használatával. Számos független szoftvergyártó (ISV) létrehozta és üzembe helyezte az alkalmazásokat az ügyfelek telephelyén. Ezeket az alkalmazásokat nehéz támogatni, mivel gyakran szükséges a különböző környezetekhez való hozzáférés, ahol az alkalmazások telepítve vannak. Az Azure CSP-előfizetésekkel egyszerűbb alternatíva az Azure méretezése és rugalmassága.

Az Azure AD DS támogatja az Azure CSP-előfizetéseket. Az alkalmazást az ügyfél Azure AD-bérlője számára kötött Azure CSP-előfizetésben helyezheti üzembe. Ennek eredményeképpen az alkalmazottak (a támogatási személyzet) kezelhetik, felügyelhetik és szervizelik azokat a virtuális gépeket, amelyeken az alkalmazás üzembe helyezése a szervezet vállalati hitelesítő adataival történik.

Az Azure AD DS felügyelt tartományát az ügyfél Azure AD-bérlője is üzembe helyezheti. Az alkalmazás ezután csatlakozik az ügyfél felügyelt tartományához. Az alkalmazásban a Kerberos/NTLM, az LDAP vagy a [System. DIRECTORYSERVICES API](/dotnet/api/system.directoryservices) -n alapuló képességek zökkenőmentesen működnek az ügyfél tartományán. A végfelhasználók kihasználhatják az alkalmazás szolgáltatásként való felhasználásának előnyeit anélkül, hogy aggódniuk kellene az alkalmazás üzembe helyezését biztosító infrastruktúra fenntartásával.

Az előfizetésben felhasznált Azure-erőforrásokért (beleértve az Azure AD DSt is) minden számlázás vissza lesz terhelve. Az ügyfelekkel való kapcsolat teljes körű vezérlést nyújt, amikor az értékesítés, a számlázás, a technikai támogatás stb. Az Azure CSP platform rugalmassága révén a támogatási ügynökök egy kis csoportja számos olyan ügyfelet képes kiszolgálni, akik az alkalmazás példányaival rendelkeznek.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Az Azure AD DS CSP üzembe helyezési modelljei

Az Azure AD DS-t kétféleképpen használhatja Azure CSP-előfizetéssel. Válassza ki a megfelelőt a biztonság és az egyszerűség alapján, amely az ügyfelekkel kapcsolatos.

### <a name="direct-deployment-model"></a>Közvetlen üzembe helyezési modell

Ebben az üzembe helyezési modellben az Azure AD DS az Azure CSP-előfizetéshez tartozó virtuális hálózaton belül van engedélyezve. A CSP-partner rendszergazdai ügynökei a következő jogosultságokkal rendelkeznek:

* *Globális rendszergazdai* jogosultságok az ügyfél Azure ad-bérlője számára.
* Az *előfizetés tulajdonosának* jogosultságai az Azure CSP-előfizetésben.

![Közvetlen üzembe helyezési modell](./media/csp/csp_direct_deployment_model.png)

Ebben az üzembe helyezési modellben a CSP-szolgáltató rendszergazdai ügynökei felügyelhetik az ügyfél identitásait. Ezek a rendszergazdai ügynökök olyan feladatokat végezhetnek el, mint például az új felhasználók vagy csoportok kiépítése, vagy az ügyfél Azure AD-bérlőn belüli alkalmazás hozzáadása.

Ez az üzembe helyezési modell olyan kisebb szervezetek számára is alkalmazható, amelyek nem rendelkeznek dedikált identitás-rendszergazdával, vagy inkább a CSP-partnert használják az identitások felügyeletére a nevükben.

### <a name="peered-deployment-model"></a>Egyenrangú üzembe helyezési modell

Ebben az üzembe helyezési modellben az Azure AD DS engedélyezve van az ügyfélhez tartozó virtuális hálózaton belül – az ügyfél által fizetett közvetlen Azure-előfizetés. A CSP-partner az ügyfél CSP-előfizetéséhez tartozó virtuális hálózaton belül telepíthet alkalmazásokat. A virtuális hálózatok ezután csatlakozhatnak az Azure Virtual Network-társítás használatával.

Ezzel az üzembe helyezéssel a CSP-partner által az Azure CSP-előfizetésben üzembe helyezett munkaterhelések vagy alkalmazások kapcsolódhatnak az ügyfél közvetlen Azure-előfizetésében kiosztott felügyelt tartományhoz.

![Egyenrangú üzembe helyezési modell](./media/csp/csp_peered_deployment_model.png)

Ez a telepítési modell a jogosultságok elkülönítését biztosítja, és lehetővé teszi, hogy a CSP-partner segélyszolgálat-ügynökei felügyelik az Azure-előfizetést, és üzembe helyezzük és kezelhetik az erőforrásokat. A CSP-partner segélyszolgálat-ügynökének azonban nem kell globális rendszergazdai jogosultságokkal rendelkeznie az ügyfél Azure AD-címtárában. Az ügyfél identitás-rendszergazdái továbbra is kezelhetik a szervezetük identitásait.

Ez a telepítési modell olyan forgatókönyvekhez használható, ahol az ISV a helyszíni alkalmazás egy üzemeltetett verzióját biztosítja, amelyhez az ügyfél Azure AD-hez is csatlakoznia kell.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Azure-AD DS felügyelete CSP-előfizetésekben

A következő fontos szempontokat kell figyelembe venni egy felügyelt tartomány Azure CSP-előfizetésben való felügyeletekor:

* **A CSP felügyeleti ügynökök a hitelesítő adataik használatával létrehozhatnak egy felügyelt tartományt:** Az Azure AD DS támogatja az Azure CSP-előfizetéseket. A CSP-partner felügyeleti ügynökök csoportjához tartozó felhasználók létrehozhatnak egy új felügyelt tartományt.

* A **kriptográfiai szolgáltatók létrehozhatnak új felügyelt tartományokat az ügyfelek számára a PowerShell használatával:** A részletekért lásd: [Az Azure AD DS engedélyezése a PowerShell használatával](powershell-create-instance.md) .

* **A CSP rendszergazdai ügynökei nem végezhetnek folyamatos felügyeleti feladatokat a felügyelt tartományon a hitelesítő adataik használatával:** A CSP rendszergazdai felhasználói a felügyelt tartományon belül nem hajthatnak végre rutin felügyeleti feladatokat a hitelesítő adataik használatával. Ezek a felhasználók kívül esik az ügyfél Azure AD-bérlőn, és a hitelesítő adataik nem érhetők el az ügyfél Azure AD-bérlőn belül. Az Azure AD DS nem fér hozzá ezekhez a felhasználókhoz a Kerberos és az NTLM jelszó-kivonatokhoz, így a felhasználók nem hitelesíthetők a felügyelt tartományokban.

  > [!WARNING]
  > A felügyelt tartományon folyamatban lévő felügyeleti feladatok végrehajtásához létre kell hoznia egy felhasználói fiókot az ügyfél címtárában.
  >
  > A felügyelt tartományhoz a CSP rendszergazda felhasználójának hitelesítő adataival nem lehet bejelentkezni. Használja az ügyfél Azure AD-bérlője számára tartozó felhasználói fiók hitelesítő adatait. Ezekre a hitelesítő adatokra szüksége van olyan feladatokhoz, mint például a virtuális gépek csatlakoztatása a felügyelt tartományhoz, a DNS felügyelete vagy a Csoportházirend felügyelete.

* **A folyamatos felügyelethez létrehozott felhasználói fiókot fel kell venni a *HRE DC-rendszergazdák* csoportba:** a *HRE DC-rendszergazdák* csoport jogosultságokkal rendelkezik bizonyos delegált felügyeleti feladatok végrehajtásához a felügyelt tartományon. Ezek a feladatok a DNS konfigurálását, a szervezeti egységek létrehozását és a csoportházirend felügyeletét foglalják magukban.
    
    Ahhoz, hogy egy CSP-partner felügyelt tartományon hajtsa végre ezeket a feladatokat, felhasználói fiókot kell létrehoznia az ügyfél Azure AD-bérlőn belül. A fiók hitelesítő adatait meg kell osztani a CSP-partner rendszergazdai ügynökével. Ezt a felhasználói fiókot is hozzá kell adni a *HRE DC-rendszergazdák* csoportjához, hogy a felügyelt tartományon lévő konfigurációs feladatok ezen felhasználói fiók használatával legyenek elvégezve.

## <a name="next-steps"></a>Következő lépések

Első lépésként [regisztráljon az Azure CSP programba](/partner-center/enrolling-in-the-csp-program). Ezután engedélyezheti Azure AD Domain Services [a Azure Portal vagy a](tutorial-create-instance.md) [Azure PowerShell](powershell-create-instance.md)használatával.