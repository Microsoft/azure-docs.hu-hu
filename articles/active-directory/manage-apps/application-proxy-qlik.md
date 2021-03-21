---
title: Azure AD alkalmazás proxy-és Qlik értelme | Microsoft Docs
description: Kapcsolja be az Alkalmazásproxyot a Azure Portalban, és telepítse a fordított proxyhoz tartozó összekötőket.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259084"
---
# <a name="application-proxy-and-qlik-sense"></a>Alkalmazásproxy és Qlik értelem 
A Azure Active Directory Application Proxy és a Qlik az értelem összevonásával biztosítható, hogy könnyen tudja használni az alkalmazásproxy-t a Qlik-környezethez való távoli hozzáférés biztosításához.  

## <a name="prerequisites"></a>Előfeltételek 
A forgatókönyv hátralévő része feltételezi, hogy elvégezte a következőket:
 
- A konfigurált [Qlik értelme](https://community.qlik.com/docs/DOC-19822). 
- [Alkalmazásproxy-összekötő telepítése](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Alkalmazások közzététele az Azure-ban 
A QlikSense közzétételéhez két alkalmazást kell közzétennie az Azure-ban.  

### <a name="application-1"></a>Alkalmazás #1: 
Az alkalmazás közzétételéhez kövesse az alábbi lépéseket. Az 1-8-es lépések részletesebb ismertetése: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md). 


1. Jelentkezzen be az Azure Portalra globális rendszergazdaként. 
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. 
3. A panel tetején kattintson a **Hozzáadás** gombra. 
4. Válassza **a helyszíni alkalmazás** lehetőséget. 
5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz a következő útmutatást használhatja: 
   - **Belső URL-cím**: az alkalmazásnak rendelkeznie kell egy belső URL-címmel, amely maga a QlikSense URL-cím. Például: **https&#58;//demo.qlikemm.com:4244** 
   - **Előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem kötelező) 
1. Kattintson a panel alján található **Hozzáadás** gombra. Ekkor megjelenik az alkalmazás, és megnyílik a gyors üzembe helyezés menü. 
2. A gyors üzembe helyezés menüben válassza a **felhasználó kiosztása teszteléshez** lehetőséget, és adjon hozzá legalább egy felhasználót az alkalmazáshoz. Győződjön meg arról, hogy a teszt fiók hozzáfér a helyszíni alkalmazáshoz. 
3. Válassza a **hozzárendelés** lehetőséget a felhasználói teszt hozzárendelésének mentéséhez. 
4. Választható Az App Management panelen válassza az egyszeri bejelentkezés lehetőséget. A legördülő menüben válassza a **Kerberos által korlátozott delegálás** lehetőséget, és töltse ki a kötelező mezőket a Qlik-konfiguráció alapján. Kattintson a **Mentés** gombra. 

### <a name="application-2"></a>Alkalmazás #2: 
A következő kivételekkel hajtsa végre ugyanazokat a lépéseket, mint az Application #1 esetében: 

**#5. lépés**: a belső URL-címnek most a QlikSense URL-címnek kell lennie az alkalmazás által használt hitelesítési porton. Az alapértelmezett érték a HTTPS **4244** , a **4248** a QlikSense pedig az április 2018 előtti verziókban. A QlikSense-kiadások alapértelmezett értéke a 2018. április **443** a https és a **80** a http-hez.  Pl.: **https&#58;//demo.qlik.com:4244**</br></br>
**#10. lépés:** Ne állítson be egyszeri bejelentkezést, és hagyja **letiltani az egyszeri bejelentkezést**
 
 
## <a name="testing"></a>Tesztelés 
Az alkalmazás most már készen áll a tesztelésre. Nyissa meg a QlikSense #1 alkalmazásban való közzétételéhez használt külső URL-címet, és jelentkezzen be mindkét alkalmazáshoz hozzárendelt felhasználóként.  

## <a name="additional-references"></a>További referenciák
További információ a Qlik való közzétételről az alkalmazásproxy használatával kapcsolatban: a Qlik közösségi cikkeinek követése: 
- [Azure AD integrált Windows-hitelesítéssel a Kerberos által korlátozott delegálással a Qlik Sense használatával](https://community.qlik.com/docs/DOC-20183)
- [Qlik-integráció az Azure AD Application Proxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele az alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxy-összekötők használata](application-proxy-connector-groups.md)

