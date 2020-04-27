---
title: A Azure Cloud Shell szerkesztő használata | Microsoft Docs
description: A Azure Cloud Shell-szerkesztő használatának áttekintése.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "60199208"
---
# <a name="using-the-azure-cloud-shell-editor"></a>A Azure Cloud Shell szerkesztő használata

A Azure Cloud Shell tartalmaz egy integrált fájlkezelőt, amely a nyílt forráskódú [Monaco-szerkesztőből](https://github.com/Microsoft/monaco-editor)készült. A Cloud Shell szerkesztőjének támogatott funkciói közé tartozik a nyelvi kiemelés, a parancsválaszték és a fájlkezelő.

![Cloud Shell szerkesztő](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>A szerkesztő megnyitása

Fájlok egyszerű létrehozásához és szerkesztéséhez indítsa el a szerkesztőt a Cloud Shell terminálján, a `code .` parancs futtatásával. Ez a művelet a terminálon beállított aktív munkakönyvtárral nyitja meg a szerkesztőt.

Ha közvetlenül szeretne megnyitni egy fájlt gyorsszerkesztéshez, futtassa a `code <filename>` parancsot a szerkesztő fájlkezelő nélküli megnyitásához.

A szerkesztő felhasználói felületről való megnyitásához kattintson a `{}` szerkesztő ikonra az eszköztárból. Ekkor megnyílik a szerkesztő, és alapértelmezés szerint a `/home/<user>` könyvtárba irányítja a fájlkezelőt.

## <a name="closing-the-editor"></a>A szerkesztő bezárása

A szerkesztő bezárásához nyissa meg `...` a szerkesztő jobb felső sarkában található művelet panelt, `Close editor`és válassza a elemet.

![Szerkesztő lezárása](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Parancs paletta

A parancs-paletta elindításához használja `F1` a kulcsot, ha a fókusz be van állítva a szerkesztőben. A parancs-paletta megnyitása a műveleti panelen is megtehető.

![Cmd-paletta](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Hozzájárulás a Monaco-szerkesztőhöz

A Cloud Shell szerkesztőben támogatott nyelvi kiemelés támogatása a [Monacói szerkesztőben](https://github.com/Microsoft/monaco-editor)a Monarch szintaxis-definíciók használatával érhető el. A hozzájárulások létrehozásával kapcsolatban olvassa el a [Monaco közreműködői útmutatót](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>További lépések
[Próbálja ki a bash-gyors útmutatót Cloud Shell](quickstart.md)
[tekintse meg az integrált Cloud Shell eszközök teljes listáját](features.md)