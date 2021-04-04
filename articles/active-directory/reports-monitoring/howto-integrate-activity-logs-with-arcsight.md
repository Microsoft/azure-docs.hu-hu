---
title: Naplók integrálása a ArcSight a Azure Monitor használatával | Microsoft Docs
description: Ismerje meg, hogyan integrálhatja Azure Active Directory-naplókat a ArcSight a Azure Monitor használatával
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230517"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Azure Active Directory-naplók integrálása a ArcSight a Azure Monitor használatával

A [Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) egy olyan biztonsági információ-és ESEMÉNYKEZELŐ (SIEM) megoldás, amely segít a platformon észlelni és reagálni a biztonsági fenyegetéseket. Az Azure AD-hez készült ArcSight-összekötővel most már átirányíthatja a Azure Active Directory (Azure AD) naplóit a ArcSight Azure Monitor használatával. Ez a funkció lehetővé teszi, hogy a ArcSight használatával figyelje a bérlőt a biztonsági kompromisszumra.  

Ebből a cikkből megtudhatja, hogyan irányíthatja át az Azure AD-naplókat a ArcSight Azure Monitor használatával. 

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Az Azure AD-tevékenységek naplóit tartalmazó Azure Event hub. Megtudhatja, hogyan [továbbíthatja a tevékenység naplóit egy Event hubhoz](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* A ArcSight syslog NG Daemon SmartConnector (SmartConnector) vagy a ArcSight Load Balancer konfigurált példánya. Ha az eseményeket a ArcSight-Load Balancer küldik, a rendszer Következésképpen a Load Balancer továbbítja azokat a SmartConnector.

Töltse le és nyissa meg az [Azure monitor Event hub ArcSight-SmartConnector tartozó konfigurációs útmutatót](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Ez az útmutató a Azure Monitor ArcSight-SmartConnector telepítéséhez és konfigurálásához szükséges lépéseket tartalmazza. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Azure AD-naplók integrálása a ArcSight

1. Először végezze el a konfigurációs útmutató **Előfeltételek** című szakaszának lépéseit. Ez a szakasz a következő lépéseket tartalmazza:
    * Felhasználói engedélyek beállítása az Azure-ban annak biztosítása érdekében, hogy az összekötő telepítéséhez és konfigurálásához a **tulajdonos** szerepkörrel rendelkező felhasználó legyen.
    * Nyissa meg a portokat a kiszolgálón a syslog NG Daemon SmartConnector, hogy az elérhető legyen az Azure-ból. 
    * A központi telepítés Windows PowerShell-parancsfájlt futtat, ezért engedélyeznie kell a PowerShellt, hogy parancsfájlokat futtasson azon a gépen, amelyen az összekötőt telepíteni kívánja.

2. Az összekötő üzembe helyezéséhez kövesse a konfigurációs útmutató **összekötő üzembe helyezése** című szakaszának lépéseit. Ebből a szakaszból megtudhatja, hogyan töltheti le és csomagolhatja ki az összekötőt, konfigurálhatja az alkalmazás tulajdonságait, és futtathatja a kibontott mappában lévő telepítési parancsfájlt. 

3. A **központi telepítés ellenőrzése az Azure-ban** című témakör lépéseit követve ellenőrizze, hogy az összekötő helyesen van-e beállítva, és megfelelően működik-e. Ellenőrizze a következőket:
    * A szükséges Azure-függvények az Azure-előfizetésében jönnek létre.
    * Az Azure AD-naplók a megfelelő helyre vannak továbbítva. 
    * A központi telepítés Alkalmazásbeállítások az Azure Function apps alkalmazás beállításaiban maradnak meg. 
    * A ArcSight új erőforráscsoport jön létre az Azure-ban, egy Azure AD-alkalmazással a ArcSight-összekötőhöz, valamint a leképezett fájlokat CEF formátumban tartalmazó Storage-fiókokhoz.

4. Végül végezze el a telepítés utáni lépéseket a konfigurációs útmutató **üzembe helyezés utáni konfigurációjában** . Ez a szakasz azt ismerteti, hogyan hajthat végre további konfigurálást, ha olyan App Service-csomagot használ, amely megakadályozza, hogy a Function apps időtúllépés után tétlen maradjon, konfigurálja az erőforrás-naplók adatfolyam-továbbítását az Event hub-ból, és frissítse a SysLog NG Daemon SmartConnector-tároló tanúsítványát, hogy társítsa az újonnan létrehozott Storage-fiókkal.

5. A konfigurációs útmutató azt is ismerteti, hogyan szabhatja testre az összekötő tulajdonságait az Azure-ban, és hogyan frissítheti és távolíthatja el az összekötőt. A teljesítménnyel kapcsolatos fejlesztéseket is tartalmaz, beleértve az Azure-használati [tervre](https://azure.microsoft.com/pricing/details/functions) való frissítést, valamint az ArcSight Load Balancer konfigurálását is, ha az esemény terhelése nagyobb, mint amit egyetlen syslog ng démon SmartConnector tud kezelni.

## <a name="next-steps"></a>Következő lépések

[Konfigurációs útmutató a Azure Monitor Event hub-hoz készült ArcSight-SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)