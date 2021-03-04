---
title: Az Azure Object-horgonyok áttekintése
description: Ismerje meg, hogyan segítheti az Azure Object Anchor a fizikai világ objektumainak észlelését.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: cbe52004dddbe74aa02347c026028a8ffd4cf8d7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034191"
---
# <a name="azure-object-anchors-overview"></a>Az Azure Object-horgonyok áttekintése

Az Azure Object-horgonyok lehetővé teszik az alkalmazások számára, hogy 3D modell használatával azonosítsák a fizikai világban lévő objektumokat, és megbecsülik a 6DoF jelentenek. A 6DoF (6 fokos szabadság) olyan rotációs és fordítási módon van definiálva, amely egy 3D-modell és annak fizikai párja, a valódi objektum között van. 

Az Azure Object-horgonyok egy felügyelt szolgáltatásból állnak a modell átalakítására és a HoloLens futtató futtatókörnyezethez készült ügyféloldali SDK-ra. A szolgáltatás elfogad egy 3D objektummodell-modellt, és kiírja az Azure Object-horgonyok modelljét. Az Azure Object-horgonyok modellt a futásidejű SDK-val együtt használva lehetővé teszi, hogy egy HoloLens-alkalmazás betöltsön egy objektummodell-példányt, felismeri és nyomon követhesse a modell példányait a fizikai világban.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Az Azure Object-horgonyok működés közben":::

## <a name="examples"></a>Példák

Az Azure Object-horgonyok által engedélyezett használati esetek például a következők:

- **Betanítás**. Vegyes valósággal kapcsolatos tapasztalatokat hozhat létre a feldolgozók számára anélkül, hogy jelölőket kellene elhelyeznie, vagy időt kell fordítania a hologramos igazítás manuális módosítására. Ha az Automatikus észleléssel és nyomon követéssel bővíteni szeretné a vegyes valóságot, a modelljét az objektum-horgonyok szolgáltatásban kell felvennie, és egy lépéssel közelebb kerül egy jelölős élményhez.

- **Feladat-útmutatás**. A vegyes valóság használatakor a munkatársaik egy csoporton keresztül is leegyszerűsíthető. A digitális utasítások és ajánlott eljárások átfedése a fizikai objektumon – legyen az a gyári szinten lévő gép, vagy a csapat konyhájában lévő kávéfőző – nagy mértékben csökkentheti a feladatok elvégzésének nehézségeit. Ezeknek a élményeknek az elindításához jellemzően valamilyen jelölőre vagy manuális igazításra van szükség, de az objektum-horgonyok esetében olyan élményt hozhat létre, amely automatikusan észleli a feladathoz kapcsolódó objektumot. Ezt követően zökkenőmentesen áthaladhat a vegyes valóság útmutatójában jelölők vagy manuális igazítás nélkül.

- Az **eszköz megkeresése**. Ha a fizikai térben már van valamilyen objektum 3D-modellje, az objektum-horgonyok lehetővé teszik az objektum példányainak megkeresését és nyomon követését a fizikai környezetben.

## <a name="next-steps"></a>Következő lépések

A következő szakaszokban az alkalmazások Azure Object-Horgonyokkal való használatának és létrehozásának első lépéseiről olvashat.

> [!div class="nextstepaction"]
> [Modell betöltése](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens és MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
