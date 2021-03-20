---
title: Magas rendelkezésre állás és vészhelyreállítás
description: Megtudhatja, hogyan tervezheti meg a Batch-alkalmazást regionális leállás esetén.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831006"
---
# <a name="design-your-batch-application-for-high-availability"></a>A Batch-alkalmazás tervezése a magas rendelkezésre állás érdekében

Azure Batch az összes Azure-régióban elérhető, de a Batch-fiók létrehozásakor egy adott régióhoz kell tartoznia. Ezután a Batch-fiók összes művelete erre a régióra vonatkozik. A készletek és a társított virtuális gépek (VM-EK) például a Batch-fiókkal megegyező régióban jönnek létre.

A Batch szolgáltatást használó alkalmazások tervezésekor figyelembe kell vennie, hogy a Batch ne legyen elérhető egy régióban. Előfordulhat, hogy ritkán fordul elő, amikor a régió egészében, a teljes batch szolgáltatásban, vagy az adott batch-fiókban problémát tapasztal.

Ha a Batch-t használó alkalmazást vagy megoldást mindig elérhetőnek kell lennie, akkor azt úgy kell megtervezni, hogy egy másik régióba kerüljön feladatátvételre, vagy hogy a számítási feladatok mindig két vagy több régió között legyenek felosztva. Mindkét módszernek legalább két batch-fiókra van szüksége, és minden fiók egy másik régióban található.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Több batch-fiók több régióban

A különböző régiókban több batch-fiók használata lehetővé teszi, hogy az alkalmazás továbbra is fut, ha egy adott régióban található batch-fiók elérhetetlenné válik. Ha az alkalmazásnak nagy rendelkezésre állású kell lennie, több fiókra van szükség, különösen fontos.

Bizonyos esetekben előfordulhat, hogy az alkalmazások két vagy több régiót szándékosan használnak. Ha például jelentős mennyiségű kapacitásra van szüksége, több régió használata szükséges lehet egy nagyméretű alkalmazás kezeléséhez vagy a jövőbeli növekedéshez. Ezeknek az alkalmazásoknak több batch-fiókra is szükségük lesz (egy-egy régiónként használatos).

## <a name="design-considerations-for-providing-failover"></a>Tervezési szempontok a feladatátvétel biztosításához

Ha egy másik régióba való feladatátvételi képességet biztosít, a megoldás minden összetevőjét figyelembe kell venni; nem elegendő, ha csak egy második batch-fiókkal rendelkezik. A legtöbb batch-alkalmazásban például szükség van egy Azure Storage-fiókra, és a Storage-fióknak és a Batch-fióknak ugyanabban a régióban kell lennie az elfogadható teljesítmény érdekében.

A feladatátvételt biztosító megoldás tervezésekor vegye figyelembe a következő szempontokat:

- Minden szükséges fiók előzetes létrehozása minden régióban, például a Batch-fiók és a Storage-fiók. A fiókok létrehozásakor gyakran nem számítunk fel díjat, és csak akkor kell fizetnie, ha a fiók használatban van, vagy ha az adatok tárolása történik.
- Győződjön meg arról, hogy a megfelelő [kvóták](batch-quota-limit.md) előre vannak beállítva az összes fióknál, így a szükséges számú magot a Batch-fiók használatával foglalhatja le.
- A sablonok és/vagy parancsfájlok segítségével automatizálhatja az alkalmazás központi telepítését egy adott régióban.
- Az alkalmazások bináris fájljainak és az adatok naprakészen tartása minden régióban. Naprakészen tarthatja, hogy a régiót gyorsan online állapotba hozhatja anélkül, hogy várnia kellene a fájlok feltöltésére és telepítésére. Ha például egy egyéni alkalmazást a készlet csomópontjain telepítenek és hivatkoznak a Batch alkalmazáscsomag használatával, akkor az alkalmazás új verziójának előállításakor fel kell töltenie az egyes batch-fiókokat, és a készlet konfigurációja hivatkozik rá (vagy az új verziót kell megadnia az alapértelmezett verziónak).
- Az alkalmazásban a Batch, a Storage és bármely más szolgáltatás meghívásával egyszerűen átválthatja az ügyfeleket, illetve a terhelést különböző régiókba.
- A normál működés részeként érdemes gyakran átváltani egy másik régióra. Ha például két üzemelő példány külön régióban található, váltson át minden hónapban a másik régióra.

## <a name="next-steps"></a>Következő lépések

- További információ a Batch-fiókok [Azure Portal](batch-account-create-portal.md), az [Azure CLI](./scripts/batch-cli-sample-create-account.md), a [POWERSHELL](batch-powershell-cmdlets-get-started.md)vagy a [Batch Management API](batch-management-dotnet.md)használatával történő létrehozásáról.
- Ismerje meg a [Batch-fiókhoz társított alapértelmezett kvótákat](batch-quota-limit.md) és a kvóták növelésének módját.
