---
title: Azure Security Center készültségi ütemterv | Microsoft Docs
description: Ez a dokumentum egy készültségi ütemtervet biztosít, amelynek segítségével gyorsan megismerkedhet az Azure Security Centerrel.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 9d74ea2b967112a794cda204cbbfcac707e1d7c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879462"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center készültségi ütemterv
Ez a dokumentum egy készültségi ütemtervet tartalmaz, amely az Azure Security Center használatának első lépéseiben nyújt segítséget.

## <a name="understanding-security-center"></a>A Security Center megismerése
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. 

Használja az alábbi erőforrásokat a Security Center használatának elkezdéséhez.

Cikkek
* [Az Azure Security Center bemutatása](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Azure Security Center – rövid útmutató](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videók
* [Rövid bemutatóvideó](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [A Security Center megelőzési, észlelési és kezelési képességeinek áttekintése](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Tervezés és műveletek

Ahhoz, hogy a lehető legnagyobb mértékben kihasználhassa a Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan használják a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos működésre, monitorozásra, irányításra és incidensmegoldásra vonatkozó követelményeket.

A következő forrásanyagok segítségére lehetnek a tervezési és műveleti folyamatok során.

Cikk
* [Azure Security Center tervezési és üzemeltetési útmutató](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)


### <a name="onboarding-computers-to-security-center"></a>Számítógépek regisztrációja a Security Centerben
A Security Center automatikusan észleli azokat az Azure-előfizetéseket vagy munkaterületeket, amelyeken nincs engedélyezve a Security Center Standard. Ez a Security Center ingyenes verzióját használó Azure-előfizetéseket és az olyan munkaterületeket is magában foglalja, amelyeken nincs engedélyezve a biztonsági megoldás.

A következő forrásanyagok segítségére lehetnek a regisztrációs folyamatok során.

Cikk
* [Regisztráció az Azure Security Center Standardra a biztonság növelése érdekében](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Videó
* [Hibrid Azure Security Center – Áttekintés](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Biztonsági hibák csökkentése a Security Centerrel
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében.

A következő forrásanyagok segítségére lehetnek a biztonsági riasztások kezelésében és az erőforrások védelmében.

Cikkek    
* [Biztonsági állapotfigyelés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Hálózat védelme az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Az Azure SQL-szolgáltatás és -adatok védelme az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Videó    
* [Biztonsági hibák csökkentése az Azure Security Center használatával](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>A Security Center használata incidensmegoldásra
A költségek és a károk csökkentése érdekében fontos, hogy a támadás megkezdése előtt egy incidensre vonatkozó válaszintézkedések legyenek érvényben. Az Azure Security Center az incidensmegoldás több szakaszában is alkalmazható.

Az alábbi forrásanyagokból megtudhatja, hogyan építhető be a Security Center az incidensmegoldási folyamatba.

Videók    
* [Az Azure Security Center használata incidensmegoldásra](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Gyors válasz a fenyegetésekre az új generációs biztonsági műveletekkel és vizsgálattal](https://youtu.be/e8iFCz5RM4g)

Cikkek    
* [Az Azure Security Center használata incidensmegoldásra](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Válasz automatizálása a munkafolyamat-automatizálással](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Speciális felhővédelem

Az Azure-beli virtuális gépek kihasználhatják a Security Center speciális felhővédelmi képességeit. Ezen képességek közé tartozik az igény szerinti virtuális gépekhez való hozzáférés, valamint az adaptív alkalmazások vezérlése.

Az alábbi forrásanyagokból megismerheti, hogyan használhatja ezeket a képességeket a Security Centerben.

Videók    
* [Azure Security Center – igény szerinti virtuális gépekhez való hozzáférés](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Adaptív alkalmazásvezérlők](https://youtu.be/wWWekI1Y9ck)

Cikkek    
* [A virtuális gépekhez való hozzáférés kezelése igény szerint](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Adaptív alkalmazás-vezérlőelemek Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Gyakorlati tevékenységek

* [Security Center – gyakorlati labor](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Webalkalmazási tűzfal (WAF) javasolt forgatókönyve a Security Centerben](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center-forgatókönyv: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>További háttéranyagok
* [Security Center – dokumentációs oldal](https://docs.microsoft.com/azure/security-center/)
* [Security Center – REST API-dokumentációs oldal](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Security Center – gyakori kérdések (GYIK)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Security Center – díjszabási oldal](https://azure.microsoft.com/pricing/details/security-center/)
* [Az identitásbiztonságra vonatkozó ajánlott eljárások](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Ajánlott hálózati biztonsági eljárások](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [PaaS-javaslatok](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Megfelelőség](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [A log Analytics-ügyfelek mostantól a Azure Security Center használatával védik a hibrid felhőalapú számítási feladatait](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Közösségi forrásanyagok

* [Security Center – UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Security Center – közösségi fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



