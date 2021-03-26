---
title: Az Azure Percept DK telepítési felületével kapcsolatos hibák elhárítása
description: Hibaelhárítási tippek a telepítési élmény során előforduló leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608493"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Az Azure Percept DK telepítési élményének hibaelhárítási útmutatója

Tekintse meg az alábbi táblázatot az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md)észlelt gyakori problémák megoldásához. Ha a probléma továbbra is fennáll, forduljon az Azure ügyfélszolgálatához.

|Probléma|Ok|Áthidaló megoldás|
|:-----|:------|:----------|
|Az Azure-fiók regisztrációs oldalaihoz vagy a Azure Portalhoz való csatlakozáskor automatikusan bejelentkezhet egy gyorsítótárazott fiókkal. Ha ez nem a használni kívánt fiók, akkor olyan élményt eredményezhet, amely nem konzisztens a dokumentációval.|Ez általában azért van, mert a böngésző egyik beállítása "Megjegyzés" egy korábban használt fiókra vonatkozik.|Az Azure lapon kattintson a jobb felső sarokban található fiók nevére, és válassza a **kijelentkezés** lehetőséget. Ezután bejelentkezhet a megfelelő fiókkal.|
|Az Azure Percept DK Wi-Fi hozzáférési pont (scz-XXXX vagy APD-XXXX) nem jelenik meg az elérhető Wi-Fi hálózatok listájában.|Ez általában egy ideiglenes probléma, amely 15 percen belül feloldódik.|Várjon, amíg a hálózat megjelenik. Ha 15 percnél hosszabb ideig nem jelenik meg, indítsa újra az eszközt.|
|A Kapcsolódás az Azure Percept DK Wi-Fi hozzáférési ponthoz gyakran megszakad.|Ennek oka lehet az eszköz és a gazdaszámítógép közötti gyenge kapcsolat. Azt is okozhatja, hogy a gazdaszámítógép más Wi-Fi kapcsolatainak interferencia is lehet.|Győződjön meg arról, hogy az antennák megfelelően vannak csatlakoztatva a fejlesztői csomaghoz. Ha a fejlesztői csomag távol van a gazdagéptől, próbálja meg közelebbről áthelyezni. Kapcsolja ki a többi internetkapcsolatot, például az HTH/5G-t, ha azok a gazdaszámítógépen futnak.|
|A gazdaszámítógép biztonsági figyelmeztetést jelenít meg az Azure Percept DK hozzáférési ponttal létesített kapcsolatról.|Ez egy ismert probléma, amely egy későbbi frissítésben lesz kijavítva.|A telepítési élmény továbbra is biztonságos.|
|Az Azure Percept DK Wi-Fi hozzáférési pont (scz-XXXX vagy APD-XXXX) megjelenik a hálózati listán, de nem tud csatlakozni.|Ezt a fejlesztői csomag Wi-Fi hozzáférési pontjának ideiglenes sérülése okozhatja.|Indítsa újra a fejlesztői készletet, és próbálkozzon újra.|
|Nem lehet csatlakozni egy Wi-Fi hálózathoz a telepítési élmény során.|A Wi-Fi hálózatnak jelenleg internetkapcsolattal kell rendelkeznie az Azure-vel való kommunikációhoz. Az EAP [PEAP/MSCHAP], a kötött portálok és a vállalati EAP-TLS kapcsolat jelenleg nem támogatott.|Győződjön meg arról, hogy a Wi-Fi hálózati típusa támogatott, és rendelkezik internetkapcsolattal.|