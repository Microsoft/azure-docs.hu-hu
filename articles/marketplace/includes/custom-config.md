---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284322"
---
Ha további konfigurációra van szüksége, az indításkor futó ütemezett feladat használatával hajtsa végre a végső módosításokat a virtuális gépen a telepítés után. Ügyeljen a következőkre is:

- Ha ez egy egyszeri futtatású feladat, a feladat a sikeres befejezést követően törli magát.
- A konfigurációk nem hivatkozhatnak a C vagy a D meghajtón kívüli meghajtókra, mert csak ez a két meghajtó garantáltan létezik (a C meghajtó az operációsrendszer-lemez, a D meghajtó pedig az ideiglenes helyi lemez).

A Linux testreszabásával kapcsolatos további információkért lásd: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](../../virtual-machines/extensions/features-linux.md).