---
title: Frissítés a (z) és a Azure AD-szinkronizáló között | Microsoft Docs
description: Ismerteti, hogyan lehet a következőt frissíteni az rSync és a Azure AD-szinkronizáló rendszerről Azure AD Connectre.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d9e2ea10aefc96c2d4d72ad0020704ba48709b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539416"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>A Microsoft Azure Active Directory Sync és az Azure Active Directory Sync frissítése
Azure AD Connect a legjobb módszer a helyszíni címtár és az Azure AD és a Microsoft 365 összekapcsolására. Ez nagyszerű alkalom arra, hogy a Windows Azure Active Directory Sync (AADSync) vagy a Azure AD-szinkronizáló () eszközről Azure AD Connect frissítsen, mivel ezek az eszközök már elavultak, és a továbbiakban nem támogatottak a 2017. április 13-án.

A két, elavult identitás-szinkronizáló eszköz az egyerdős ügyfelek (például az rSync), valamint a többerdős és más haladó ügyfelek (Azure AD-szinkronizáló) számára lett felkínálva. Ezeket a régebbi eszközöket egyetlen, az összes forgatókönyvhöz elérhető megoldás váltotta fel: Azure AD Connect. Új funkciókat, funkció-fejlesztéseket és új forgatókönyvek támogatását kínálja. Ha továbbra is szinkronizálni szeretné a helyszíni identitás adatait az Azure AD-val és a Microsoft 365ával, javasoljuk, hogy frissítsen a Azure AD Connectra. A Microsoft nem garantálja, hogy ezek a régebbi verziók 2017. december 31-ig működnek.

Az rSync utolsó kiadása 2014 júliusában jelent meg, és a Azure AD-szinkronizáló utolsó kiadása a 2015 májusában jelent meg.

## <a name="what-is-azure-ad-connect"></a>Mi az az Azure AD Connect?
A Azure AD Connect a következő: az rSync és a Azure AD-szinkronizáló utódja. A két támogatott forgatókönyvet kombinálja. További információt a helyszíni [identitások integrálása Azure Active Directory](whatis-hybrid-identity.md)használatával című cikkből olvashat.

## <a name="deprecation-schedule"></a>Elavult ütemterv
| Date | Megjegyzés |
| --- | --- |
| Április 13., 2016 |A Windows Azure Active Directory Sync ("az rSync") és a Microsoft Azure Active Directory Sync ("Azure AD-szinkronizáló") elavultként van bejelentve. |
| Április 13., 2017 |A támogatás véget ér. Az ügyfelek többé nem tudják megnyitni a támogatási esetet, ha az Azure AD Connect először nem frissítik. |
|December 31., 2017|Előfordulhat, hogy az Azure AD nem fogadja el a kommunikációt a Windows Azure Active Directory Sync ("az rSync") és a Microsoft Azure Active Directory Sync ("Azure AD-szinkronizáló") között.
|Április 1., 2021| A Windows Azure Active Directory Sync ("az rSync") és a Microsoft Azure Active Directory Sync ("Azure AD-szinkronizáló") már nem fog működni |

## <a name="how-to-transition-to-azure-ad-connect"></a>Áttérés Azure AD Connectre
Ha a (z)-et futtatja, kétféleképpen frissítheti a szolgáltatást: helyben történő frissítés és párhuzamos üzembe helyezés. A legtöbb ügyfél számára ajánlott helyben történő frissítés, és ha a legutóbbi operációs rendszer, és kevesebb mint 50 000 objektum van. Más esetekben javasolt egy párhuzamos üzembe helyezést végezni, ahol a rendszer áthelyezi az rSync konfigurációját egy Azure AD Connect rendszert futtató új kiszolgálóra.

| Megoldás | Eset |
| --- | --- |
| [Frissítés a DirSync szolgáltatásról](how-to-dirsync-upgrade-get-started.md) |<li>Ha már fut egy már meglévő, az rSync-kiszolgálóval.</li> |
| [Frissítés Azure AD-szinkronizálóról](how-to-upgrade-previous-version.md) |<li>Ha Azure AD-szinkronizáló.</li> |

Ha szeretné megtekinteni, hogyan végezheti el a helyi frissítést a (z) Azure AD Connect, akkor tekintse meg ezt a Channel 9 videót:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>GYIK
**K: értesítést kaptam az Azure-csapattól és/vagy az Microsoft 365 üzenetközpont üzenetből, de a csatlakozási lehetőséget használom.**  
Az értesítést a 1,0-es Build-számú Azure AD Connect használó ügyfeleknek is elküldték \* . 0 (pre-1,1 kiadás használata). A Microsoft azt javasolja, hogy az ügyfelek a Azure AD Connect kiadásaiban maradjanak naprakészek. Az 1,1-es verzióban bevezetett [automatikus frissítési](how-to-connect-install-automatic-upgrade.md) funkció megkönnyíti a Azure ad Connect telepítésének legújabb verzióját.

**K: az rSync/Azure AD-szinkronizáló a 2017 április 13-án abbahagyja a munkát?**  
Az rSync/Azure AD-szinkronizáló 2017. április 13-án továbbra is működni fog.  Az Azure AD azonban már nem fogadja el a kommunikációt az rSync/Azure AD-szinkronizálótól a 2017. december 31-ig. Az rsync és a Azure AD-szinkronizáló az 2021. április 1. után már nem fog működni.

**K: milyen verziójú rSync-verziót frissíthetek a szolgáltatásból?**  
A rendszer a jelenleg használatban lévő bármelyik, az rSync-es kiadásból való frissítést támogatja. 

**K: mi a helyzet az Azure AD-összekötővel a FIM/a webszolgáltatáshoz?**  
Az FIM/a rendszerhez készült Azure AD-összekötőt **nem** jelentették be elavultként. Ez a **szolgáltatás rögzítése**; nincs új funkció hozzáadva, és nem kap hibajavításokat. A Microsoft azt javasolja, hogy az ügyfelek a Azure AD Connectre való áttérés megtervezéséhez használják. Erősen ajánlott az új üzemelő példányok nem indíthatók el a használatával. Ez az összekötő a jövőben elavultként lesz bejelentve.

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
