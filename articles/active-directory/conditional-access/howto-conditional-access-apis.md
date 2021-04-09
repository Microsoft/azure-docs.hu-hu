---
title: Feltételes hozzáférési API-k és PowerShell-Azure Active Directory
description: Az Azure AD feltételes hozzáférési API-k és a PowerShell használata a szabályzatok, például a kód kezelésére
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860213"
---
# <a name="conditional-access-programmatic-access"></a>Feltételes hozzáférés: programozott hozzáférés

Számos szervezet kifejezte, hogy a lehető legtöbbet kell kezelnie a környezetek, például a programkódok számára. A Microsoft Graph használatával olyan feltételes hozzáférési szabályzatokat kezelhet, mint bármely más kód a környezetben.

A Microsoft Graph olyan egységesített programozható modellt biztosít, amelyet a szervezetek a Microsoft 365, a Windows 10 és a Enterprise Mobility + Securityban lévő adatkezeléshez használhatnak. A Microsoft Graphról további információt a [Microsoft Graph áttekintése](/graph/overview)című cikkben talál.

![A gráf részét képező elsődleges erőforrásokat és kapcsolatokat bemutató kép](./media/howto-conditional-access-apis/microsoft-graph.png)

A következő példák a támogatás nélkül érhetők el. Ezeket a példákat a szervezete eszközeinek alapjaként használhatja. 

Az alábbi példák többsége olyan eszközöket használ, mint például a [felügyelt identitások](../managed-identities-azure-resources/overview.md), a [Logic apps](../../logic-apps/logic-apps-overview.md), a [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), a [csapatok](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)és a [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Konfigurálás

### <a name="powershell"></a>PowerShell

Sok rendszergazda számára a PowerShell már ismert parancsfájlkezelési eszköz. Az alábbi példa bemutatja, hogyan használható az [Azure ad PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD) a feltételes hozzáférési házirendek kezeléséhez.

- [Feltételes hozzáférési szabályzatok konfigurálása az Azure AD PowerShell-parancsokkal](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

Ez a példa a feltételes hozzáférésű Graph API-kon elérhető alapszintű létrehozási, olvasási, frissítési és törlési (szifilisz) lehetőségeket mutatja be. A példa néhány JSON-sablont is tartalmaz, amelyekkel létrehozhat néhány minta házirendet.

- [Feltételes hozzáférési szabályzatok konfigurálása Microsoft Graph API-hívásokkal](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Konfigurálás sablonok használatával

A feltételes hozzáférési API-k használatával feltételes hozzáférési házirendeket telepíthet az üzem előtti környezetbe sablon használatával.

- [Feltételes hozzáférési szabályzatok konfigurálása Microsoft Graph API-sablonokkal](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Tesztelés

Ez a példa a biztonságos üzembe helyezési gyakorlatokat modellezi olyan jóváhagyási munkafolyamatokkal, amelyek a feltételes hozzáférési szabályzatokat az egyik környezetből, például az üzem előtti, egy másikhoz, például az éles környezethez is másolhatják.

- [Feltételes hozzáférési szabályzatok előléptetése tesztelési környezetekben](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Üzembe helyezés

Ez a példa egy olyan mechanizmust biztosít, amely lehetővé teszi, hogy a fokozatos üzembe helyezés feltételes hozzáférési szabályzatokat fokozatosan a felhasználói populációhoz végezze el, így a támogatási hatás és a problémák korai szakaszában is kezelhetők.

- [Feltételes hozzáférési szabályzatok üzembe helyezése üzemi környezetekben jóváhagyási munkafolyamatokkal](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

Ez a példa egy olyan mechanizmust biztosít, amely a feltételes hozzáférési szabályzatok időbeli változásait figyeli, és riasztásokat indíthat a kulcsfontosságú házirendek megváltozásakor.

- [A telepített feltételes hozzáférési szabályzatok figyelése a változásokhoz és a riasztások elindításához](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Kezelés

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A feltételes hozzáférési szabályzatok biztonsági mentésének és visszaállításának automatizálása a következő példában szereplő csapatok jóváhagyásával.

- [Feltételes hozzáférési szabályzatok biztonsági mentési és visszaállítási folyamatának kezelése Microsoft Graph API-hívások használatával](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok

Több rendszergazda is létrehozhat feltételes hozzáférési szabályzatokat, és elfelejtheti a [vészhelyzeti hozzáférési fiókjainak](../roles/security-emergency-access.md) kizárását a szabályzatok számára. Ez a példa biztosítja, hogy a rendszer minden házirendet frissít, hogy tartalmazza a kijelölt sürgősségi hozzáférési fiókokat.

- [Vészhelyzeti hozzáférési fiókok hozzárendelésének kezelése a feltételes hozzáférési házirendekhez Microsoft Graph API-hívások használatával](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Válságterv készítése

A dolgok nem mindig a kívánt módon működnek, amikor erre szükség van, hogy vissza lehessen térni egy olyan állapotba, ahol a munka folytatódhat. Az alábbi példa segítségével visszaállíthatja a szabályzatokat egy ismert, jó készenléti tervre, és letilthatja az egyéb feltételes hozzáférési házirendeket.

- [Feltételes hozzáférési készenléti szabályzatok aktiválásának kezelése Microsoft Graph API-hívások használatával](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Közösségi közreműködés

Ezek a minták a GitHub- [tárházban](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)érhetők el. Örömmel támogatjuk a közösségi hozzájárulások alapos GitHub-problémáit és a lekéréses kérelmeket.

## <a name="next-steps"></a>Következő lépések

- [A Microsoft Graph áttekintése](/graph/overview)

- [Feltételes hozzáférési API](/graph/api/resources/conditionalaccesspolicy)

- [Elnevezett Location API](/graph/api/resources/namedlocation)
