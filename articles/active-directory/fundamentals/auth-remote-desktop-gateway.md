---
title: Szolgáltatások Távoli asztali átjáró Azure Active Directory
description: Építészeti útmutató Távoli asztali átjáró szolgáltatások Azure Active Directorysal való eléréséhez.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172737"
---
# <a name="remote-desktop-gateway-services"></a>Távoli asztali átjáró szolgáltatások

A standard Távoli asztali szolgáltatások (RDS) központi telepítése a Windows Serveren futó különböző [Távoli asztal szerepkör-szolgáltatásokat](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) tartalmazza. Az Azure Active Directory (Azure AD) alkalmazásproxy-alapú üzembe helyezése állandó kimenő kapcsolatban áll az összekötő szolgáltatást futtató kiszolgálóval. Más központi telepítések egy terheléselosztó használatával hagyják el a nyitott bejövő kapcsolatokat. Ez a hitelesítési minta lehetővé teszi, hogy több típusú alkalmazást nyújtson a helyszíni alkalmazások Távoli asztali szolgáltatások használatával történő közzétételével. Emellett az Azure AD Application Proxy használatával csökkenti az üzemelő példány támadási felületét is.

## <a name="use-when"></a>A következő esetekben használja

Meg kell adnia a távelérést, és biztosítania kell a Távoli asztali szolgáltatások üzembe helyezését előhitelesítéssel.

![építészeti diagram](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: az Application proxy által kiszolgált RDS elérését biztosítja.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő.

* **Azure ad**: hitelesíti a felhasználót. 

* **Alkalmazásproxy szolgáltatás**: fordított proxyként működik a kérésnek a felhasználótól az RDS-be való továbbításához. Az alkalmazásproxy is kényszerítheti a feltételes hozzáférési szabályzatok alkalmazását. 

* **Távoli asztali szolgáltatások**: különálló virtualizált alkalmazások platformja, amely biztonságos mobil-és távoli asztali hozzáférést biztosít, és lehetővé teszi a végfelhasználók számára, hogy alkalmazásaikat és asztalait a felhőből futtassák. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Távoli asztali átjáró szolgáltatások implementálása az Azure AD-vel

* [Távoli asztal közzététele az Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Helyszíni alkalmazás hozzáadása távoli eléréshez az Application proxyn keresztül az Azure AD-ben](../manage-apps/application-proxy-add-on-premises-application.md)

