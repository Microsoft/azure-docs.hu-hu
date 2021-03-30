---
title: A Azure Cloud Shell ablak használata | Microsoft Docs
description: Az Azure Cloud Shell ablak használatának áttekintése.
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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "70860324"
---
# <a name="using-the-azure-cloud-shell-window"></a>A Azure Cloud Shell ablak használata

Ez a dokumentum a Cloud Shell ablak használatát ismerteti.

## <a name="swap-between-bash-and-powershell-environments"></a>Csere a bash és a PowerShell környezet között

A Cloud Shell eszköztár környezeti választójának használatával válthat a bash és a PowerShell környezetek között.  
![Környezet kiválasztása](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Újraindítás Cloud Shell
A gép állapotának alaphelyzetbe állításához kattintson a Cloud Shell eszköztárán található újraindítás ikonra.  
![Újraindítás Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> A Cloud Shell újraindítása a gép állapotának alaphelyzetbe állítását és az Azure-fájlmegosztás által nem megőrzött fájlok elvesztését eredményezi.

## <a name="change-the-text-size"></a>A szöveg méretének módosítása
Kattintson a Beállítások ikonra az ablak bal felső részén, majd vigye a kurzort a "szövegméret" lehetőség fölé, és válassza ki a kívánt szövegméret-méretet. A kiválasztás a munkamenetek között marad.
![Szövegméret](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>A betűtípus módosítása
Kattintson a Beállítások ikonra az ablak bal felső részén, majd vigye a kurzort a "betűkészlet" lehetőség fölé, és válassza ki a kívánt betűtípust.  A kiválasztás a munkamenetek között marad.
![Betűtípus](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Fájlok feltöltése és letöltése
Kattintson a fájlok feltöltése/letöltése ikonra az ablak bal felső részén, majd válassza a feltöltés vagy a letöltés lehetőséget.  
![Fájlok feltöltése/letöltése](media/using-the-shell-window/uploaddownload.png)
* Fájlok feltöltéséhez az előugró ablakban keresse meg a fájlt a helyi számítógépen, válassza ki a kívánt fájlt, majd kattintson a Megnyitás gombra.  A rendszer feltölti a fájlt a `/home/user` könyvtárba.
* Fájl letöltéséhez adja meg a fájl teljes elérési útját az előugró ablakban (azaz alapvetően a könyvtár alatt található elérési utat `/home/user` , amely alapértelmezés szerint megjelenik), majd kattintson a "Letöltés" gombra.  
> [!NOTE] 
> A fájlok és a fájlelérési utak kis-és nagybetűket Cloud Shell. A fájl elérési útjában ellenőrizze a burkolatot.

## <a name="open-another-cloud-shell-window"></a>Másik Cloud Shell ablak megnyitása
Cloud Shell lehetővé teszi több egyidejű munkamenet használatát a böngésző lapjain azáltal, hogy az egyes munkamenetek külön folyamatként léteznek.
Ha kilép egy munkamenetből, mindenképpen lépjen ki az egyes munkamenet-ablakokból, mivel az egyes folyamatok egymástól függetlenül futnak, bár ugyanazon a gépen futnak.  
Kattintson az új munkamenet megnyitása ikonra az ablak bal felső részén. Megnyílik egy új lap, amely egy másik, a meglévő tárolóhoz csatlakoztatott munkamenettel fog megnyílni.
![Új munkamenet megnyitása](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell szerkesztő
* Tekintse át a [Azure Cloud Shell-szerkesztő használata](using-cloud-shell-editor.md) lapot.

## <a name="web-preview"></a>Webes előnézet
Kattintson az ablak bal felső részén található webes előnézet ikonra, válassza a "Konfigurálás" lehetőséget, és adja meg a megnyitni kívánt portot.  Válassza a "nyitott port" lehetőséget, hogy csak a portot nyissa meg, vagy a "Megnyitás és Tallózás" lehetőségre kattintva nyissa meg a portot, és tekintse meg a portot egy új lapon.  
![Webes előnézet](media/using-the-shell-window/preview.png)  
<br>
![Port konfigurálása](media/using-the-shell-window/preview-configure.png)  
Kattintson a webes előnézet ikonra az ablak bal felső részén, majd válassza az "előnézet port..." lehetőséget. egy új lapon lévő nyitott port előnézete. Kattintson a webes előnézet ikonra az ablak bal felső részén, majd válassza a "Port lezárása..." lehetőséget. a nyitott port bezárásához.  
![Előnézet/Bezárás port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>& maximalizálása Cloud Shell ablak méretének csökkentése
Az ablak jobb felső sarkában található Minimalizálás ikonra kattintva elrejtheti azt. Kattintson ismét a Cloud Shell ikonra az elrejtéshez.
A teljes méret ikonra kattintva beállíthatja, hogy az ablak maximális magasságra legyen beállítva. Az ablak előző méretre való visszaállításához kattintson a visszaállítás gombra.  
![Az ablak méretének csökkentése vagy maximalizálása](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cloud Shell ablak átméretezése
Az Cloud Shell ablak átméretezéséhez kattintson és húzza az eszköztár felső szegélyét.

## <a name="scrolling-text-display"></a>Görgethető szöveg megjelenítése
A terminál szövegének áthelyezéséhez görgessen az egérrel vagy az érintőpadba.

## <a name="exit-command"></a>Kilépés parancs
`exit`A Futtatás megszakítja az aktív munkamenetet. Ez a viselkedés alapértelmezés szerint 20 percen belül, interakció nélkül történik.

## <a name="next-steps"></a>Következő lépések

[Bash Cloud Shell rövid útmutatóban](quickstart.md) <br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md)
