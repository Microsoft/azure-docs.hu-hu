---
title: Mi az Azure egyszeri bejelentkezés (SSO)?
description: Ismerje meg, hogy az egyszeri bejelentkezés (SSO) hogyan működik a Azure Active Directoryokkal. Egyszeri bejelentkezés használata, hogy a felhasználóknak ne kelljen megemlékezniük minden alkalmazás jelszavait. Használja az SSO-t is a fiókok felügyeletének egyszerűsítése érdekében.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260197"
---
# <a name="what-is-single-sign-on-sso"></a>Mi az az egyszeri bejelentkezés (SSO)?

Az egyszeri bejelentkezés azt jelenti, hogy a felhasználóknak nem kell bejelentkezniük minden általuk használt alkalmazásba. A felhasználó egyszer jelentkezik be, és a rendszer a hitelesítő adatokat is használja más alkalmazásokhoz.

Ha Ön a végfelhasználó, valószínűleg nem érdekli az egyszeri bejelentkezés részletei. Csak azokat az alkalmazásokat szeretné használni, amelyekkel hatékonyabbá teheti a jelszavát. Az alkalmazások a következő címen találhatók: https://myapps.microsoft.com .
 
Ha Ön rendszergazda vagy informatikai szakember, olvassa el a következőt: További információ az egyszeri bejelentkezésről és az Azure-beli megvalósításáról.

## <a name="single-sign-on-basics"></a>Egyszeri bejelentkezés alapjai
Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók hogyan jelentkeznek be és használják az alkalmazásokat. Az egyszeri bejelentkezési alapú hitelesítési rendszereket gyakran "modern hitelesítésnek" nevezzük. A modern hitelesítés és az egyszeri bejelentkezés az identitás-és hozzáférés-kezelés (IAM) nevű kategóriába tartozik. Ha szeretné megtudni, hogy mi teszi lehetővé az egyszeri bejelentkezést, tekintse meg ezt a videót.

A hitelesítés alapjai: az alapjai | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Egyszeri bejelentkezés webes alkalmazásokkal
A webalkalmazások hihetetlenül népszerűek. A webalkalmazásokat különböző vállalatok üzemeltetik, és szolgáltatásként elérhetővé tették. A webalkalmazások népszerű példái közé tartozik a Microsoft 365, a GitHub és a Salesforce, és több ezer más is van. A felhasználók a webalkalmazásokat webböngészővel érik el a számítógépükön. Az egyszeri bejelentkezés lehetővé teszi a felhasználók számára a különböző webalkalmazások közötti váltást anélkül, hogy többször kellene bejelentkeznie.

Ha szeretné megtudni, hogyan működik az egyszeri bejelentkezés a Web Apps szolgáltatással, tekintse meg ezt a két videót.

Hitelesítés alapjai: webalkalmazások | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Hitelesítés alapjai: webes egyszeri bejelentkezés | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Felhő és helyszíni üzemeltetett alkalmazások
Az egyszeri bejelentkezés megvalósításának módja attól függ, hogy hol futtatja az alkalmazást. Üzemeltetési ügyek, mert a hálózati forgalom át van irányítva az alkalmazás eléréséhez. Ha egy alkalmazás a helyi hálózaton keresztül érhető el, és egy helyszíni alkalmazásnak nevezi el, akkor nincs szükség arra, hogy a felhasználók hozzáférhessenek az internethez az alkalmazás használatához. Ha az alkalmazás a felhőben üzemeltetett alkalmazásban fut, akkor a felhasználóknak az alkalmazás használatához el kell érniük az internetet.

> [!TIP]
> A felhőben üzemeltetett alkalmazásokat szolgáltatásként szolgáló (SaaS) alkalmazásoknak is nevezik. 

A felhőben üzemeltetett alkalmazások egyszeri bejelentkezése egyszerű. Tudatja az identitás-szolgáltatóval, hogy az alkalmazás használatban van. Ezután konfigurálja úgy az alkalmazást, hogy megbízzon az identitás-szolgáltatóban. Ha szeretné megtudni, hogyan használhatja az Azure AD-t az alkalmazások identitás-szolgáltatójának használatára, tekintse [meg](add-application-portal.md)a gyors üzembe helyezési útmutatót

> [!TIP]
> A felhő és az Internet kifejezések gyakran használhatók felcserélhetővé. Ennek oka a hálózati diagramok használata. Gyakori, hogy egy diagramon a Felhőbeli alakzattal rendelkező nagyméretű számítógép-hálózatokat jelöli, mert nem lehetséges az összes összetevő rajzolása. Az Internet a legismertebb hálózat, így könnyen használhatók a feltételek. A számítógépek hálózata azonban megalkotható egy felhőben is.

Használhat egyszeri bejelentkezést is a helyszíni alkalmazások számára. A helyszíni SSO-hoz szükséges technológiákat alkalmazásproxy néven kell megtenni. További információ: [egyszeri bejelentkezéses beállítások](sso-options.md).

## <a name="multiple-identity-providers"></a>Több identitás-szolgáltató
Ha úgy állítja be az egyszeri bejelentkezést, hogy több identitású szolgáltató között működjön, azt a rendszer összevonásnak nevezzük. Ha szeretné megtudni, hogyan működik az összevonás, tekintse meg ezt a videót.

Hitelesítés alapjai: összevonás | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Következő lépések
* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Az egyszeri bejelentkezés beállításai](sso-options.md)
