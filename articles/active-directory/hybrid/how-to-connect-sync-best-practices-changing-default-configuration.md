---
title: 'Azure AD Connect Sync: az alapértelmezett konfiguráció módosítása | Microsoft Docs'
description: Az Azure AD Connect Sync alapértelmezett konfigurációjának módosítására vonatkozó ajánlott eljárásokat tartalmazza.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357698"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: ajánlott eljárások az alapértelmezett konfiguráció módosításához
Ennek a témakörnek a célja, hogy leírja Azure AD Connect szinkronizálás támogatott és nem támogatott módosításait.

A Azure AD Connect által létrehozott konfiguráció a legtöbb olyan környezet esetében működik, amely szinkronizálja a helyszíni Active Directory az Azure AD-vel. Bizonyos esetekben azonban szükség van bizonyos változtatásokra a konfigurációban, hogy az megfeleljen egy adott igénynek vagy követelménynek.

## <a name="changes-to-the-service-account"></a>A szolgáltatásfiók módosításai
Azure AD Connect szinkronizálás a telepítővarázsló által létrehozott szolgáltatásfiók alatt fut. Ez a szolgáltatásfiók a szinkronizálás által használt adatbázis titkosítási kulcsait tárolja. A szolgáltatás 127 karakter hosszú jelszóval jön létre, a jelszó pedig nem jár le.

* A szolgáltatásfiók jelszavának módosítása vagy alaphelyzetbe állítása nem **támogatott** . Ezzel elpusztítja a titkosítási kulcsokat, és a szolgáltatás nem tud hozzáférni az adatbázishoz, és nem indítható el.

## <a name="changes-to-the-scheduler"></a>Az ütemező módosításai
A build 1,1 (február 2016) verziótól kezdődően az [ütemező](how-to-connect-sync-feature-scheduler.md) úgy is beállítható, hogy az alapértelmezett 30 percnél eltérő szinkronizálási ciklus legyen.

## <a name="changes-to-synchronization-rules"></a>A szinkronizálási szabályok módosításai
A telepítővarázsló olyan konfigurációt biztosít, amelyet a leggyakoribb forgatókönyvek esetében érdemes feldolgozni. Ha módosítania kell a konfigurációt, ezeket a szabályokat továbbra is támogatott konfigurációval kell végrehajtania.

> [!WARNING]
> Ha módosítja az alapértelmezett szinkronizálási szabályokat, akkor ezek a módosítások felül lesznek írva, amikor a következő Azure AD Connect frissülnek, ami váratlan és valószínűleg nemkívánatos szinkronizálási eredményeket eredményez.

* Módosíthatja az [attribútumok folyamatait](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) , ha az alapértelmezett közvetlen attribútum-folyamatok nem alkalmasak a szervezet számára.
* Ha nem kívánja elkészíteni [az attribútumot](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) , és el szeretné távolítani az Azure ad-ben meglévő attribútum-értékeket, akkor ehhez a forgatókönyvhöz létre kell hoznia egy szabályt.
* Törlés helyett nem [kívánt szinkronizálási szabályt kell letiltani](#disable-an-unwanted-sync-rule) . A rendszer a frissítés során egy törölt szabályt hoz létre.
* [Egy beépített szabály módosításához](#change-an-out-of-box-rule)készítsen másolatot az eredeti szabályról, és tiltsa le a beépített szabályt. A szinkronizálási szabály szerkesztője megkéri és segít Önnek.
* Exportálja egyéni szinkronizálási szabályait a szinkronizálási szabályok szerkesztőjének használatával. A szerkesztő egy PowerShell-szkriptet biztosít, amellyel egyszerűen újra létrehozhatja őket egy vész-helyreállítási forgatókönyvben.

> [!WARNING]
> A beépített szinkronizálási szabályok ujjlenyomattal rendelkeznek. Ha módosítja ezeket a szabályokat, az ujjlenyomat már nem megfelelő. A jövőben problémák merülhetnek fel, amikor új Azure AD Connect-kiadást próbál alkalmazni. Ebben a cikkben csak a módosításokat hajtja végre.

### <a name="disable-an-unwanted-sync-rule"></a>Nemkívánatos szinkronizálási szabály letiltása
Ne törölje a beépített szinkronizálási szabályt. A következő frissítés során újra létrejön.

Bizonyos esetekben a telepítővarázsló olyan konfigurációt hozott létre, amely nem működik a topológiában. Ha például van egy fiók-erőforrás erdős topológiája, de kibővítette a sémát a fiók erdőben az Exchange-sémával, akkor az Exchange-szabályok a fiók erdőhöz és az erőforrás-erdőhöz jönnek létre. Ebben az esetben le kell tiltania az Exchange szinkronizálási szabályát.

![Letiltott szinkronizálási szabály](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

A fenti képen a telepítővarázsló egy régi Exchange 2003-sémát talált a fiók erdőben. Ez a séma-bővítmény hozzá lett adva az erőforrás-erdőnek a fabrikam környezetében való bevezetése előtt. Annak biztosítása érdekében, hogy a régi Exchange-implementációból ne legyenek szinkronizálva attribútumok, a szinkronizálási szabályt le kell tiltani az ábrán látható módon.

### <a name="change-an-out-of-box-rule"></a>Egy beépített szabály módosítása
Az egyetlen alkalommal, amikor módosítania kell egy beépített szabályt, ha módosítania kell az illesztési szabályt. Ha módosítania kell egy attribútum folyamatát, akkor a beépített szabályoknál magasabb prioritású szinkronizálási szabályt kell létrehoznia. Az egyetlen szabály, amelynek a klónozása gyakorlatilag szükséges, a szabály az **ad-User JOIN szolgáltatásban**. Az összes többi szabályt felülbírálhatja magasabb prioritási szabállyal.

Ha módosítania kell egy beépített szabályt, akkor készítsen másolatot a beépített szabályról, és tiltsa le az eredeti szabályt. Ezután végezze el a módosításokat a klónozott szabályban. A szinkronizálási szabály szerkesztője segíti a lépéseket. Amikor megnyit egy beépített szabályt, a következő párbeszédpanel jelenik meg:  
![Figyelmeztetés a Box szabály alól](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

A szabály másolatának létrehozásához válassza az **Igen** lehetőséget. Ekkor megnyílik a klónozott szabály.  
![Klónozott szabály](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

A klónozott szabályon végezze el a hatókör, a csatlakozás és az átalakítások szükséges módosításait.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
