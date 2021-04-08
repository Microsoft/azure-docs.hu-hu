---
title: Labs-fogalmak – Azure Lab Services | Microsoft Docs
description: Megismerheti a labor Services alapfogalmait, és azt, hogy miként hozhat létre és kezelhet laborokat.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435518"
---
# <a name="labs-concepts"></a>Tesztkörnyezetekhez kapcsolódó fogalmak

A következő lista a Key Lab Services alapfogalmait és definícióit tartalmazza:

## <a name="quota"></a>Kvóta

A kvóta azt az időkorlátot adja meg (órában), ameddig a pedagógus beállíthatja, hogy a tanulók laboratóriumi virtuális gépet használjanak. A beállítás értéke 0 vagy adott számú óra lehet. Ha a kvóta 0 értékű, a tanulók csak akkor használhatják a virtuális gépet, ha egy ütemterv fut, vagy amikor a pedagógus manuálisan bekapcsolja a virtuális gépet a tanuló számára.  

A kvóta órája akkor számít, ha a tanuló saját maga indítja el a laboratóriumi virtuális gépet.  Ha egy oktató manuálisan indítja el a labor virtuális gépet egy tanuló számára, a rendszer nem használja az adott diákra vonatkozó kvóta-időt.

## <a name="schedules"></a>Ütemezések

Az ütemtervek azon időpontok, amelyeket a pedagógus létrehozhat az osztály számára, így a tanuló virtuális gépek az osztály idejére elérhetők.  Az ütemtervek lehetnek egyszeri vagy ismétlődőek.  A kvóta órája nem használatos az ütemterv futtatásakor.

Az ütemtervek három típusa létezik: standard, csak indítás és leállítás.

- **Standard**.  Ez az ütemterv elindítja az összes tanuló virtuális gépet a megadott kezdési időpontban, és leállítja az összes tanuló virtuális gépet a megadott leállítás időpontjában.
- **Csak indítás**.   Ez az ütemterv az összes tanuló virtuális gépet a megadott időpontban indítja el.  A tanuló virtuális gépek nem állnak le, amíg egy tanuló leállítja a virtuális gépet a Azure Lab Services-portálon, vagy csak leállítási ütemezés történik.
- **Csak leállítás**.  Ez az ütemterv az összes tanuló virtuális gépet leállítja a megadott időpontban.  

## <a name="template-virtual-machine"></a>Sablon virtuális gépe

A laborban található sablon virtuális gépe egy alapszintű virtuálisgép-rendszerkép, amelyből minden felhasználó virtuális gépe létrejött. A trénerek/labor-készítők beállítják a sablon virtuális gépet, és azt a szoftverrel konfigurálja, amelyet a résztvevők számára a laborok betanításához szeretne biztosítani. Amikor közzétesz egy sablon virtuális gépet, Azure Lab Services létrehozza vagy frissíti a laboratóriumi virtuális gépeket a sablon VM-je alapján.

## <a name="user-profiles"></a>Felhasználói profilok

Ez a cikk az Azure Lab Services különböző felhasználói profiljait ismerteti.

### <a name="lab-account-owner"></a>Tesztkörnyezetfiók tulajdonosa

Általában a szervezet felhőalapú erőforrásainak rendszergazdája, aki az Azure-előfizetés tulajdonosa, laboratóriumi fiók tulajdonosaként működik, és a következő feladatokat végzi el:

- Tesztkörnyezetfiókot állít be a vállalat számára.
- Felügyeli és konfigurálja a szabályzatokat minden tesztkörnyezetre vonatkozóan.
- Engedélyeket ad a vállalatban dolgozó személyeknek tesztkörnyezet létrehozására a tesztkörnyezetfiókban.

### <a name="educator"></a>Oktató

A felhasználók, például tanárok vagy online trénerek labor-fiókban hoznak létre laborokat. Az oktató az alábbi feladatokat hajtja végre:

- Létrehozza az osztályterem-tesztkörnyezetet.
- Virtuális gépeket hoz létre a tesztkörnyezetben.
- Telepíti a megfelelő szoftvereket a virtuális gépeken.
- Meghatározza, hogy ki férhet hozzá a tesztkörnyezethez.
- Elküldi a hallgatóknak a tesztkörnyezetre mutató regisztrációs hivatkozást.

### <a name="student"></a>Tanuló

A hallgató az alábbi feladatokat hajtja végre:

- Regisztrál a tesztkörnyezetben a tesztkörnyezet létrehozójától kapott regisztrációs hivatkozással.
- Csatlakozik a tesztkörnyezetben lévő virtuális géphez, és osztálytermi munkához, feladatokhoz és projektekhez használja.

## <a name="next-steps"></a>Következő lépések

Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
