---
title: Hibák elhárítása az Azure Percept DK helyszíni használata során
description: Hibaelhárítási tippek a beléptetési folyamat során észlelt leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662318"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Az Azure Percept DK bevezetési élményének hibaelhárítási útmutatója

Íme néhány probléma, amely az Azure Percept DK bevezetési élményében merülhet fel. Ha az útmutató lépéseinek használata után a probléma továbbra is fennáll. Forduljon az Azure ügyfélszolgálatához.

|Probléma|Ok|Áthidaló megoldás|
|:-----|:------|:----------|
|Az Azure-fiók regisztrációs oldalaihoz vagy a Azure Portalhoz való csatlakozáskor automatikusan bejelentkezhet egy gyorsítótárazott fiókkal. Ha ez nem a használni kívánt fiók, akkor olyan élményt eredményezhet, amely nem konzisztens a dokumentációval.|Ez általában azért van, mert a böngésző egyik beállítása "Megjegyzés" egy korábban használt fiókra vonatkozik.|Az Azure lapon kattintson a lap jobb felső sarkára, ahol megjelenik a fiók neve, majd kattintson a "kijelentkezés" gombra. Ezután bejelentkezhet a megfelelő fiókkal.|
|Az Azure Percept DK hozzáférési pont (scz-XXXX) hálózata nem jelenik meg az elérhető Wi-Fi hálózatok listáján.|Ez általában egy ideiglenes probléma, amely kis idő alatt feloldja magát.|Várjon, amíg a hálózat megjelenik. Ha nem több, mint 15 percet vesz igénybe, indítsa újra az eszközt.|
|A Kapcsolódás az Azure Percept DK hozzáférési ponthoz gyakran megszakad.|Ennek oka lehet az eszköz és a gazdaszámítógép közötti gyenge kapcsolat. Azt is okozhatja, hogy a gazdaszámítógép más Wi-Fi kapcsolatainak interferencia is lehet.|Győződjön meg arról, hogy az antennák megfelelően vannak csatlakoztatva a fejlesztői csomaghoz. Ha a fejlesztői csomag távol van a gazdagéptől, próbálja meg közelebbről áthelyezni. Kapcsolja ki a többi internetkapcsolatot, például az HTH/5G-t, ha azok a gazdaszámítógépen futnak.|
|A gazdaszámítógép biztonsági figyelmeztetést jelenít meg az Azure Percept DK hozzáférési ponttal létesített kapcsolatról.|Ez egy ismert probléma, amely egy későbbi frissítésben lesz kijavítva.|A fejlesztői készlet Wi-Fi hozzáférési ponton keresztül is nyugodtan folytathatja a bevezetési élményt.|
|Az Azure Percept DK hozzáférési pont (scz-XXXX) hálózata megjelenik a hálózati listán, de nem tud csatlakozni.|Ennek oka lehet a fejlesztői készlet Wi-Fi hozzáférési pont ideiglenes sérülése.|Indítsa újra a fejlesztői készlet, és próbálkozzon újra.|
|Nem lehet csatlakozni egy Wi-Fi hálózathoz a telepítési élmény során.|A Wi-Fi hálózatnak jelenleg internetkapcsolattal kell rendelkeznie, hogy kommunikálni tudjon az Azure-ban. Az EAP [PEAP/MSCHAP], a kötött portálok és a vállalati EAP-TLS kapcsolat jelenleg nem támogatott.|Győződjön meg arról, hogy Wi-Fi hálózati kapcsolat támogatott, és rendelkezik internetkapcsolattal.|