---
title: Az Azure AD Reporting API használatának első | Microsoft Docs
description: A Azure Active Directory Reporting API első lépések
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e5a095c87e46839c7c120bdd6d8db1595164e57
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532526"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Első lépések a Azure Active Directory Reporting API-val

Azure Active Directory különböző jelentéseket [biztosít,](overview-reports.md)amelyek hasznos információkat tartalmaznak az olyan alkalmazásokhoz, mint a SIEM-rendszerek, a naplózási és az üzletiintelligencia-eszközök. 

Az Azure AD Microsoft Graph API használatával programozott hozzáférést kaphat az adatokhoz REST-alapú API-kon keresztül. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk áttekintést nyújt a jelentéskészítési API-ról, beleértve a hozzáférési módokat.

Ha problémákba fog belefutni, tekintse meg, hogyan lehet támogatást [kapni a Azure Active Directory.](../fundamentals/active-directory-troubleshooting-support-howto.md)

## <a name="prerequisites"></a>Előfeltételek

A jelentéskészítési API felhasználói beavatkozással vagy anélkül történő eléréséhez a következőt kell használnia:

1. Szerepkörök hozzárendelése (biztonsági olvasó, biztonsági rendszergazda, globális rendszergazda)
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások gyűjtése

Részletes útmutatásért tekintse meg a Azure Active Directory [REPORTING API elérésének előfeltételeit.](howto-configure-prerequisites-for-reporting-api.md) 

## <a name="api-endpoints"></a>API-végpontok 

Az Microsoft Graph naplókhoz a Microsoft Graph API-végpont, a bejelentkezések `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` api-végpontja pedig `https://graph.microsoft.com/v1.0/auditLogs/signIns` . További információkért tekintse meg a [naplózási API-referenciát](/graph/api/resources/directoryaudit) és [a bejelentkezési API-referenciát.](/graph/api/resources/signIn)

Az Identity [Protection kockázatészlelési](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API-jának használatával szoftveres hozzáférést kaphat a biztonsági észleléshez a Microsoft Graph. További információ: Első lépések a Azure Active Directory Identity Protection [és Microsoft Graph.](../identity-protection/howto-identity-protection-graph-api.md) 
  
A kiépítési naplók [API-jának használatával](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) programozott hozzáférést kaphat a bérlőben történt kiépítési eseményekhez. 

## <a name="apis-with-microsoft-graph-explorer"></a>API-k a Microsoft Graph Explorerrel

A bejelentkezési [és Microsoft Graph API-adatok](https://developer.microsoft.com/graph/graph-explorer) ellenőrzéséhez használhatja a Microsoft Graph Explorert. Jelentkezzen be a fiókjába mindkét bejelentkezési gombbal a Graph Explorer felhasználói felületén, és állítsa be az **AuditLog.Read.All** és **a Directory.Read.All** engedélyeket a bérlőhöz az ábrán látható módon.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Engedélyek módosítása felhasználói felület](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Tanúsítványok használata az Azure AD Reporting API eléréséhez 

Ha felhasználói beavatkozás nélkül tervezi lekérni a jelentéskészítési adatokat, használja az Azure AD Reporting API-t tanúsítványokkal.

Részletes útmutatásért lásd: [Adatok lekérte az Azure AD Reporting API használatával tanúsítványokkal.](tutorial-access-api-with-certificates.md)

## <a name="next-steps"></a>Következő lépések

 * [A Reporting API elérésének előfeltételei](howto-configure-prerequisites-for-reporting-api.md) 
 * [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
 * [Az Azure AD Reporting API hibáinak elhárítása](troubleshoot-graph-api.md)