---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009677"
---
> [!NOTE]
> A webalkalmazások 20 perc inaktivitás után időtúllépést okozhatnak. és csak a tényleges webalkalmazásnak küldött kérések állíthatják alaphelyzetbe az időzítőt. Az alkalmazás konfigurációjának megtekintése a Azure Portal, vagy a speciális eszközök webhelyre irányuló kérések ( `https://<app_name>.scm.azurewebsites.net` ) nem állítja alaphelyzetbe az időzítőt. Ha úgy állítja be a webalkalmazást, hogy folyamatos vagy ütemezett (időzítő-trigger) webjobs-feladatokat futtasson, engedélyezze az **Always On** beállítást a webalkalmazás Azure **konfigurációs** lapján, hogy a webjobs megbízhatóan fusson. Ez a funkció csak az alapszintű, a standard és a prémium [szintű díjszabásban](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)érhető el.
