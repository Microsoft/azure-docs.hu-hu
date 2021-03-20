---
title: API-k exportálása az Azure API Managementról a Power platformra | Microsoft Docs
description: Ismerje meg, hogyan exportálhat API-kat API Managementról a Power platformra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146654"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>API-k exportálása az Azure API Managementról a Power platformra 

A Microsoft [Power platformot](https://powerplatform.microsoft.com) használó Citizen-fejlesztőknek gyakran kell elérniük a professzionális fejlesztők által fejlesztett és az Azure-ban üzembe helyezett üzleti képességeket. Az [Azure API Management](https://aka.ms/apimrocks) lehetővé teszi a hivatásos fejlesztők számára, hogy API-ként közzétegyék a háttér-szolgáltatást, és az egyéni összekötőként egyszerűen exportálják ezeket az API-kat a Power platformba (Power apps és Power automatizálás). 

Ez a cikk végigvezeti az API-k API Managementról a Power platformra való exportálásának lépésein. 

## <a name="prerequisites"></a>Előfeltételek

+ Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ Győződjön meg arról, hogy van egy API a API Management-példányban, amelyet exportálni szeretne a Power platformba
+ Győződjön meg arról, hogy rendelkezik Power apps vagy Power automatizáló [környezettel](/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="export-an-api"></a>API exportálása

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. Kattintson az exportálni kívánt API melletti három pontra. 
3. Válassza az **Export** (Exportálás) lehetőséget.
4. Válassza **a Power apps és a Power automatizál** lehetőséget.
5. Válassza ki azt a környezetet, amelybe exportálni kívánja az API-t. 
6. Adja meg a megjelenítendő nevet, amelyet az egyéni összekötő neveként fog használni.  
7. Nem kötelező, ha az API-t egy OAuth 2,0-kiszolgáló védi, további részleteket is meg kell adnia, például:,,, `Client ID` `Client secret` `Authorization URL` `Token URL` és `Refresh URL` .  
8. Válassza az **Export** (Exportálás) lehetőséget. 

Az Exportálás befejezése után navigáljon a Power app vagy a Power automatizáló környezetbe. Az API-t egyéni összekötőként fogja látni.

## <a name="next-steps"></a>Következő lépések

* [További információ a Power platformról](https://powerplatform.microsoft.com/)
* [Az oktatóanyagok követésével megismerheti API Management gyakori feladatait](./import-and-publish.md)
