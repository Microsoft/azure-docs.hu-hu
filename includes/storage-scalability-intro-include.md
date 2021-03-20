---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477147"
---
Ez a hivatkozás az Azure Storage méretezhetőségi és teljesítménybeli céljait ismerteti. Az itt felsorolt méretezhetőségi és teljesítményi célok magas végponti célokat szolgálnak, de elérhetők. A Storage-fiók által elért kérelmek aránya és sávszélessége minden esetben a tárolt objektumok méretétől, a felhasznált hozzáférési mintáktól, valamint az alkalmazás által végrehajtott számítási feladatok típusától függ.

Győződjön meg arról, hogy teszteli a szolgáltatást annak megállapításához, hogy a teljesítménye megfelel-e a követelményeknek. Ha lehetséges, kerülje a forgalom sebességének hirtelen kiugrását, és győződjön meg arról, hogy a forgalom megfelelően oszlik meg a partíciók között.

Ha az alkalmazás eléri azt a korlátot, amelyet a partíció képes kezelni a munkaterhelés esetében, az Azure Storage megkezdi a 503-as hibakód (a kiszolgáló foglalt) vagy a 500 (művelet időtúllépése) válaszait. Ha 503 hiba történt, érdemes lehet módosítani az alkalmazást, hogy az újrapróbálkozások exponenciális leállítási szabályzatot használjanak. Az exponenciális leállítási lehetővé teszi a partíció terhelésének csökkentését, valamint az adott partíció felé irányuló adatforgalom megkönnyítése érdekében.
