---
title: Felhasználói felületi keretrendszerbeli csoportok beágyazása
titleSuffix: An Azure Communication Services quickstart
description: Ebből a dokumentumból megtudhatja, hogyan használható az Azure kommunikációs szolgáltatás felhasználói felületi keretrendszerének beágyazási funkciója kulcsrakész hívási élmények létrehozásához.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e55cfb1a4dff7bfda2323e68777d6f50514b1608
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930234"
---
# <a name="teams-embed"></a>Csoportok beágyazása

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


A Teams beágyazás egy olyan Azure kommunikációs szolgáltatási képesség, amely a közös vállalkozások közötti és a vállalatok közötti telefonálási interakcióra összpontosít. A csapat beágyazási rendszerének lényege a [videó-és hanghívás](../voice-video-calling/calling-sdk-features.md), de a csapatok beágyazási rendszere az Azure-beli egyszerű hívások révén teljes körű felhasználói élményt biztosít a Microsoft Teams-találkozókon alapuló felhasználók számára.

Az SDK-k beágyazva vannak, és ezeket a képességeket kulcsrakész, összetett formátumban teszik elérhetővé. Eldobni a csapatokat az alkalmazás vászonba, és az SDK teljes felhasználói élményt generál. Mivel ez a felhasználói élmény nagyon hasonlít a Microsoft Teams üléseire, a következő előnyöket veheti igénybe:

- Csökkentett fejlesztési idő és mérnöki komplexitás
- A csapatokkal folytatott végfelhasználói ismeretek
- A [csapat végfelhasználói betanítási tartalom](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67) újbóli használatának lehetősége

A csoportok beágyazás a csapat ülésein támogatott legtöbb funkciót biztosítja, beleértve a következőket:

- A felhasználók hang-és video-eszközeinek konfigurálását megelőző felhasználói élmény
- A hang-és video-eszközök konfigurálásának behívási élménye
- [Videós hátterek](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): lehetővé teszi a résztvevők számára a hátterek életlenítését vagy cseréjét
- [Több lehetőség a](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) Képtár nagyméretű katalógusához, együttesen mód, fókusz, rögzítés és reflektorfény
- [Tartalom megosztása](https://support.microsoft.comoffice/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8#ID0EABAAA=Mobile): a résztvevők megoszthatják a képernyőt

További információ erről a felhasználói felületről más Azure kommunikációs SDK-k esetén: a [felhasználói felület SDK-koncepciójának bemutatása](ui-sdk-overview.md). 
