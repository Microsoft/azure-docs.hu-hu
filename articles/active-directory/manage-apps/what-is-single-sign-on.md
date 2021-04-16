---
title: Mi az az Azure-beli egyszeri bejelentkezés (SSO)?
description: Megtudhatja, hogyan működik az egyszeri bejelentkezés (SSO) a Azure Active Directory. Használjon SSO-t, hogy a felhasználóknak ne kell minden alkalmazáshoz jelszót megjegyeznie. Használja az SSO-t is a fiókkezelés felügyeletének egyszerűsítéséhez.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379502"
---
# <a name="what-is-single-sign-on-sso"></a>Mi az az egyszeri bejelentkezés (SSO)?

Az egyszeri bejelentkezés azt jelenti, hogy a felhasználónak nem kell minden alkalmazásba bejelentkeznie. A felhasználó egyszer bejelentkezik, és ezt a hitelesítő adatokat más alkalmazások is használják.

Ha Ön végfelhasználó, valószínűleg nem igazán érdeklik az SSO részletei. Csak olyan alkalmazásokat szeretne használni, amelyek hatékonyabbá teszik a munkavégzést anélkül, hogy ilyen sok jelszót begépelné. Az alkalmazásokat a következő címen találhatja meg: https://myapps.microsoft.com .
 
Ha Ön rendszergazda vagy it-rendszergazda, olvassa el a következőt, ha többet szeretne megtudni az SSO-val és annak Az Azure-ban való alkalmazásának mikéntjről.

## <a name="single-sign-on-basics"></a>Az egyszeri bejelentkezés alapjai
Az egyszeri bejelentkezés óriási ugrást jelent a felhasználók bejelentkezésében és az alkalmazások használatában. Az egyszeri bejelentkezésen alapuló hitelesítési rendszereket gyakran "modern hitelesítésnek" nevezik. A modern hitelesítés és az egyszeri bejelentkezés az Identitás- és hozzáférés-kezelés (IAM) nevű számítási kategóriába tartozik. Ebből a videóból megértheti, hogy mi teszi lehetővé az egyszeri bejelentkezést.

A hitelesítés alapjai: A | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Egyszeri bejelentkezés webalkalmazásokkal
A webalkalmazások rendkívül népszerűek. A webalkalmazásokat különböző vállalatok üzemeltetik, és szolgáltatásként érhetők el. Néhány népszerű webalkalmazás például a Microsoft 365, a GitHub és a Salesforce, és még több ezer van. A felhasználók egy webböngészővel férnek hozzá a webalkalmazáshoz a számítógépükről. Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók többszöri bejelentkezés nélkül navigálnak a különböző webalkalmazások között.

Ha többet szeretne megtudni arról, hogyan működik az egyszeri bejelentkezés a webalkalmazásokkal, tekintse meg ezt a két videót.

A hitelesítés alapjai: Webalkalmazások | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

A hitelesítés alapjai: Webes egyszeri bejelentkezéses | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Felhőbeli és helyszíni üzemeltetett alkalmazások
Az egyszeri bejelentkezés implementációja az alkalmazás helyétől függ. Az üzemeltetés fontos, mert a hálózati forgalom az alkalmazáshoz való hozzáféréshez van irányítva. Ha egy alkalmazást a helyi hálózaton üzemeltet és ér el, amit helyszíni alkalmazásnak nevezünk, akkor a felhasználóknak nem kell az internethez hozzáférniük az alkalmazáshoz. Ha az alkalmazást máshol üzemeltetik, más néven felhőben üzemeltetett alkalmazásként, akkor a felhasználóknak az alkalmazás használathoz az internethez kell hozzáférniük.

> [!TIP]
> A felhőben üzemeltetett alkalmazásokat SaaS-alkalmazásoknak is nevezik. 

A felhőben üzemeltetett alkalmazások esetében az egyszeri bejelentkezés egyszerű. Tudatja az identitásszolgáltatóval, hogy az alkalmazás használja. Ezután úgy konfigurálja az alkalmazást, hogy megbízhat az identitásszolgáltatóban. Az Azure AD alkalmazásidentitás-szolgáltatóként való használatával kapcsolatban lásd az alkalmazáskezelés rövid [útmutatóját.](add-application-portal.md)

> [!TIP]
> A felhő és az internet kifejezéseket gyakran felcserélhetőnek használják. Ennek oka a hálózati diagramok miatt van. A diagramon felhőalakzattal ábrázolt nagy számítógép-hálózatokat gyakran jelölik, mert nem lehet minden összetevőt megrajzolni. Az internet a legismertebb hálózat, így könnyen használhatók felcserélhető kifejezések. Azonban bármilyen számítógépes hálózat megalkotható felhőként.

Helyszíni alkalmazásokhoz egyszeri bejelentkezést is használhat. A helyszíni SSO-t a következő technológiának nevezzük: alkalmazásproxy. További információ: Egyszeri [bejelentkezés beállításai.](sso-options.md)

## <a name="multiple-identity-providers"></a>Több identitásszolgáltató
Ha az egyszeri bejelentkezést több identitásszolgáltató között is beállítja, ezt összevonásnak nevezzük. Ebből a videóból megtudhatja, hogyan működik az összevonás.

A hitelesítés alapjai: Összevonási | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Következő lépések
* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
* [Az egyszeri bejelentkezés beállításai](sso-options.md)
