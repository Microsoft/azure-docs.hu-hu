---
title: A Azure Lab Services-beli tantermi labor irányítópultjának használata | Microsoft Docs
description: Megtudhatja, hogyan használhatja az irányítópultot egy osztálytermi laborhoz a Azure Lab Servicesban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4e59917aa385a344d79a62441e208df970f42285
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444318"
---
# <a name="dashboard-for-classroom-labs"></a>Az osztályterem Labs irányítópultja
Ez a cikk a Azure Lab Services található osztálytermi labor irányítópult-nézetét ismerteti. 

![Irányítópult](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Költségek és számlázási csempe
Ez a csempe a következő költségbecslés részleteit tartalmazza:

| Beállítás | Érték | 
| ------- | ----- | 
| Kvóta órája | Azon órák maximális száma, ameddig a felhasználó az ütemezett órákon kívül is használhatja a virtuális gépet. |
| Ütemezett órák | A laborban beállított ütemterv alapján felmerülő órák. Ez az érték csak akkor érhető el, ha az összes ütemezett eseménynél be van állítva a kezdő/a dátum. |
| Óra/felhasználó | A kvóta órája és az ütemezett órák összege. |
| Felhasználók maximális száma | A tesztkörnyezetben lévő felhasználók maximális száma az összes igényelni kívánt virtuális gép alapján. |
| Óra x felhasználó | Óra/felhasználó szorozva a felhasználók számával. |
| Módosított kvóta | Az adott felhasználókhoz hozzáadott kvóta-munkaórák összege. |
| Összes óra * $/óra | A kiválasztott virtuálisgép-méret alapján óránkénti díj. Ez a díjszabás alapján történik. |
| Becsült költségek összesen | Ez a tesztkörnyezet maximális díja a jelenlegi beállítások alapján. |

## <a name="template-tile"></a>Sablon csempe
A csempén az alábbi információk jelennek meg:

- A sablon létrehozásának dátuma 
- A sablon utolsó közzétételének dátuma 

Emellett megjelenik egy hivatkozás, amely megnyitja a **sablon** lapját, ahol [kezelheti a SABLONHOZ tartozó virtuális gépet](how-to-create-manage-template.md) a osztályhoz. 

## <a name="virtual-machine-pool-tile"></a>Virtuálisgép-készlet csempe

A csempén az alábbi információk jelennek meg:

- Tanulók (felhasználók) számára hozzárendelt virtuális gépek száma
- Azoknak a virtuális gépeknek a száma, amelyek még nem lettek hozzárendelve a diákokhoz

Emellett megjelenik egy hivatkozás, amely a **virtuális gép készlet** lapjára mutat, ahol a laborban [kezelheti a virtuális gépek készletét](how-to-set-virtual-machine-passwords.md) . 

## <a name="users-tile"></a>Felhasználók csempe

A csempén az alábbi információk jelennek meg:

- Az osztályban regisztrált felhasználók száma
- A laborhoz hozzáadott, de az osztályban nem regisztrált felhasználók száma 

Emellett egy hivatkozásra kattintva megnyithatja a **felhasználók** lapot, ahol a laborhoz tartozó [felhasználókat kezelheti](how-to-configure-student-usage.md) . 

## <a name="schedules-tile"></a>Ütemtervek csempe
A Lab aktuális ütemezett eseményei megjelennek a csempén. Emellett egy hivatkozással is megnyithatja az **ütemterv** oldalt, ahol [ütemterveket hozhat létre és kezelhet](how-to-create-schedules.md). A csempe megjeleníti a csak két ütemezett esemény részleteit, valamint a labor hátralévő ütemezett eseményeinek számát. 

![Ütemezett események](./media/use-dashboard/scheduled-events.png)

