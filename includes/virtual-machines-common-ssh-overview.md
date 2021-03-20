---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016367"
---
## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok áttekintése

Az [SSH](https://www.ssh.com/ssh/) egy titkosított kapcsolati protokoll, amely biztonságos bejelentkezéseket biztosít a nem biztonságos kapcsolatokon keresztül. Az SSH az Azure-ban üzemeltetett Linux rendszerű virtuális gépek alapértelmezett kapcsolati protokollja. Bár az SSH titkosított kapcsolatot biztosít, az SSH-kapcsolatokkal rendelkező jelszavak használatával továbbra is sebezhetővé válik a virtuális gép a találgatásos támadásokkal szemben. Azt javasoljuk, hogy SSH-kapcsolaton keresztül csatlakozzon egy virtuális géphez egy nyilvános titkos kulcspár, más néven *SSH*-kulcs használatával. 

- A *nyilvános kulcs* a linuxos virtuális gépre kerül.

- A *titkos kulcs* a helyi rendszeren marad. Védje a titkos kulcsot. Ne ossza meg senkivel.

Amikor SSH-ügyféllel csatlakozik a linuxos virtuális géphez (amely a nyilvános kulccsal rendelkezik), a távoli virtuális gép teszteli az ügyfelet, hogy ellenőrizze, hogy a megfelelő titkos kulccsal rendelkezik-e. Ha az ügyfél rendelkezik titkos kulccsal, a rendszer hozzáférést kap a virtuális géphez. 

A szervezet biztonsági házirendjeitől függően a több Azure-beli virtuális gép és szolgáltatás eléréséhez egyetlen nyilvános titkos kulcspár is felhasználható. Az elérni kívánt virtuális gépekhez vagy szolgáltatásokhoz nem kell külön kulcspárt. 

A nyilvános kulcs bárkivel megosztható, de csak Ön (vagy a helyi biztonsági infrastruktúra) férhetnek hozzá a titkos kulcshoz.