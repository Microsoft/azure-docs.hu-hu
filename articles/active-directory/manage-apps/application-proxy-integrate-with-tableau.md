---
title: Azure Active Directory Application Proxy és tabló | Microsoft Docs
description: Ismerje meg, hogyan használható a Azure Active Directory (Azure AD) alkalmazásproxy a Tablós környezet távelérésének biztosításához.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258625"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy és tabló 

A Azure Active Directory Application Proxy és a tabló a partnerekkel együttműködve biztosítja, hogy könnyen tudja használni az alkalmazásproxy-t, hogy távoli hozzáférést biztosítson a tabló üzembe helyezéséhez. Ez a cikk bemutatja, hogyan konfigurálhatja ezt a forgatókönyvet.  

## <a name="prerequisites"></a>Előfeltételek 

A cikkben szereplő forgatókönyv feltételezi, hogy rendelkezik a következővel:

- A [tabló](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurálva van. 

- Egy telepített [alkalmazásproxy-összekötő](application-proxy-add-on-premises-application.md) . 

 
## <a name="enabling-application-proxy-for-tableau"></a>Alkalmazásproxy engedélyezése a Tablóhoz 

Az alkalmazásproxy támogatja a OAuth 2,0 engedélyezési folyamatot, amely a tabló megfelelő működéséhez szükséges. Ez azt jelenti, hogy az alkalmazás engedélyezéséhez nem szükségesek további speciális lépések, mint az alábbi közzétételi lépések követésével.


## <a name="publish-your-applications-in-azure"></a>Alkalmazások közzététele az Azure-ban 

A tabló közzétételéhez közzé kell tennie egy alkalmazást az Azure Portalon.

A következő esetében:

- Az 1-8-es lépések részletes utasításait lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md). 
- Információk az alkalmazásproxy mezőihez tartozó tabló-értékek megkereséséről: a tabló dokumentációja.  

**Az alkalmazás közzététele**: 


1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) alkalmazás-rendszergazdaként. 

2. Válassza **Azure Active Directory > vállalati alkalmazások** lehetőséget. 

3. A panel tetején kattintson a **Hozzáadás** gombra. 

4. Válassza **a helyszíni alkalmazás** lehetőséget. 

5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz a következő útmutatást használhatja: 

    - **Belső URL-cím**: az alkalmazásnak tartalmaznia kell egy belső URL-címet, amely maga a tabló URL-címe. Például: `https://adventure-works.tableau.com`. 

    - **Előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem kötelező). 

6. A panel tetején kattintson a **Hozzáadás** gombra. Ekkor megjelenik az alkalmazás, és megnyílik a gyors üzembe helyezés menü. 

7. A gyors üzembe helyezés menüben válassza a **felhasználó kiosztása teszteléshez** lehetőséget, és adjon hozzá legalább egy felhasználót az alkalmazáshoz. Győződjön meg arról, hogy a teszt fiók hozzáfér a helyszíni alkalmazáshoz. 

8. Válassza a **hozzárendelés** lehetőséget a felhasználói teszt hozzárendelésének mentéséhez. 

9. Választható Az App Management lapon válassza az **egyszeri bejelentkezés** lehetőséget. Válassza az **integrált Windows-hitelesítés** lehetőséget a legördülő menüből, és töltse ki a szükséges mezőket a tabló konfigurációja alapján. Kattintson a **Mentés** gombra. 

 

## <a name="testing"></a>Tesztelés 

Az alkalmazás most már készen áll a tesztelésre. Nyissa meg a tabló közzétételéhez használt külső URL-címet, és jelentkezzen be mindkét alkalmazáshoz hozzárendelt felhasználóként.



## <a name="next-steps"></a>Következő lépések

További információ az Azure AD Application Proxyról: [biztonságos távoli hozzáférés biztosítása a helyszíni alkalmazásokhoz](application-proxy.md).

