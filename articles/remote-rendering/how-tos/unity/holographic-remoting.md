---
title: A holografikus távelérés és a távoli renderelés használata a Unityben
description: Hogyan használható a holografikus távelérési előzetes verzió az Azure távoli rendereléssel együtt
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92201817"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>A holografikus távelérés és a távoli renderelés használata a Unityben

A [holografikus](/windows/mixed-reality/holographic-remoting-player) távelérés és az Azure távoli renderelés kölcsönösen kizárják egymást egy alkalmazáson belül. Ilyenkor az [egységes lejátszási mód](/windows/mixed-reality/unity-play-mode) is nem érhető el.

Az Unity Editor minden egyes futtatása esetén csak a kettő közül lehet használni. Ha a másikat szeretné használni, először indítsa újra az egységet.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>A 2. HoloLens előzetes verziójának használata Unity lejátszási módban

 Az egység lejátszási módja továbbra is használható, például az alkalmazás felhasználói felületének teszteléséhez. Azonban fontos, hogy az ARR soha ne legyen inicializálva. Ellenkező esetben a rendszer összeomlik.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR-fülhallgató használata az asztal előnézetéhez

Ha a Windows vegyes valóság VR headset van jelen, az egységen belül is megtekinthető. Ebben az esetben a rendszer nem tudja inicializálni az ARR-t, azonban a WMR headset használata közben nem lehet csatlakozni a munkamenethez.