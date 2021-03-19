---
title: 'Többosztályos növelt döntési fa: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a többosztályos növelt döntési famodult a Azure Machine Learning egy osztályozó használatával a címkézett adategységek létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905307"
---
# <a name="multiclass-boosted-decision-tree"></a>Többosztályos súlyozott döntési fa

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy gépi tanulási modellt, amely a megnövelt döntési fák algoritmusán alapul.

A megnövelt döntési fa egy olyan együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább. Az előrejelzések együttesen a fák együttesén alapulnak.

## <a name="how-to-configure"></a>Konfigurálás 

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás egy felügyelt tanulási módszer, szüksége van egy *címkével ellátott adatkészletre* , amely tartalmazza az összes sor értékét tartalmazó címke oszlopot.

Az ilyen típusú modellt a [Train modellel](././train-model.md)is betaníthatja. 

1.  Adja hozzá a **többosztályos növelt döntési fa** modult a folyamathoz.

1.  Válassza ki, hogyan szeretné képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.
    
    + **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  

1. A **kihagyott levelek maximális száma a fa** korlátaiban a bármely fában létrehozható terminál-csomópontok (levelek) maximális száma.
    
    Ennek az értéknek a növelésével növelheti a fa méretét, és nagyobb pontosságot érhet el, ha a túlilleszkedés és a hosszabb betanítási idő fennáll.
  
1. A **mintavételek minimális száma a levél csomópontjaiban** azt jelzi, hogy hány esetben szükséges a terminál csomópont (levél) létrehozása a fában.  

    Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.

1. A **tanulási ráta** meghatározza a lépés méretét a tanulás során. Adjon meg egy 0 és 1 közötti számot.

    A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.

1. A **felépített fák száma** azt jelzi, hogy hány döntési fát kell létrehozni az Ensemble-ban. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

1. A **véletlenszerű számú magok** opcionálisan nem negatív egész számot állítanak be a véletlenszerű mag értékeként való használatra. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.  

    A véletlenszerű mag beállítása alapértelmezés szerint 42. A különböző véletlenszerű magok használatával végzett egymást követő futtatások eltérő eredménnyel rendelkezhetnek.

1. A modell betanítása:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
