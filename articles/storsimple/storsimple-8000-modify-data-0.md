---
title: A 0.8000 StorSimple adatsorozat-eszközre vonatkozó adatértékek módosítása | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Windows PowerShell StorSimple-bővítményet a StorSimple-eszközön található adat0 hálózati adapter újrakonfigurálásához.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961039"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Módosítsa az adat0 hálózati adapter beállításait a StorSimple 8000 Series eszközön

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple-eszköz hat hálózati adapterrel rendelkezik, az adatok 0-tól az ADATOKig 5. Az adat0 csatolót mindig a Windows PowerShell felületén vagy a soros konzolon keresztül konfigurálja, és a rendszer automatikusan engedélyezi a felhőt. Vegye figyelembe, hogy az adat0 hálózati adapter nem konfigurálható a Azure Portalon keresztül.

A StorSimple-eszköz kezdeti üzembe helyezése során a rendszer először a telepítő varázslóval konfigurálja a 0. adatillesztőt. Ha az eszköz működési módban van, előfordulhat, hogy újra kell konfigurálnia az adat0-beállításokat. Ez az oktatóanyag két módszert biztosít a 0. adathálózati beállítások módosítására Windows PowerShell StorSimple-bővítményeon keresztül.

Az oktatóanyag elolvasása után a következőket teheti:

* Adat0 hálózati beállítás módosítása a telepítővarázsló segítségével
* Az adatértékek 0 hálózati beállításainak módosítása a `Set-HcsNetInterface` parancsmag használatával

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>AZ adat0 hálózati beállítások módosítása a telepítővarázsló segítségével
A 0. hálózati beállítások újrakonfigurálásához csatlakozzon a StorSimple eszköz Windows PowerShell-felületéhez, és indítsa el a telepítővarázsló munkamenetét. Az adatok 0 beállításainak módosításához hajtsa végre a következő lépéseket:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Az adat0 hálózati beállítások módosítása a telepítővarázsló segítségével
1. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Amikor a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát**. Az alapértelmezett jelszó: `Password1` .
2. A parancssorba írja be a következőt:
   
    `Invoke-HcsSetupWizard`
3. Megjelenik egy telepítővarázsló, amely segít az eszköz 0 felületének konfigurálásában. Adja meg az IP-cím, az átjáró és a hálózati maszk új értékeit.

> [!NOTE]
> A rögzített vezérlők IP-címeit újra kell konfigurálni a Azure Portal StorSimple eszközének **hálózati beállítások** paneljén. További információért lépjen a [hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces)elemre.

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>AZ adat0 hálózati beállítások módosítása Set-HcsNetInterface parancsmag használatával
Az adat0 hálózati adapter újrakonfigurálásának másik módja a `Set-HcsNetInterface` parancsmag használata. A parancsmagot a rendszer a StorSimple-eszköz Windows PowerShell felületéről hajtja végre. Ha ezt az eljárást használja, a vezérlő rögzített IP-címei is konfigurálhatók itt. Az adatok 0 beállítások módosításához hajtsa végre a következő lépéseket: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Az adat0 hálózati beállítások módosítása a Set-HcsNetInterface parancsmaggal
1. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Amikor a rendszer kéri, adja meg az eszköz rendszergazdai jelszavát. Az alapértelmezett jelszó: `Password1` .
2. A parancssorba írja be a következőt:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    A szögletes zárójelben adja meg a következő értékeket a 0. adattípushoz:
   
   * IPv4-cím
   * IPv4-átjáró
   * IPv4 alhálózati maszk
   * A 0. vezérlő rögzített IPv4-címe
   * Az 1. vezérlő rögzített IPv4-címe
     
     A parancsmag használatával kapcsolatos további információkért látogasson el [Windows PowerShell StorSimple-bővítménye parancsmag-referenciára](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)).

## <a name="next-steps"></a>Következő lépések
* A nem az adat0 hálózati adapterek konfigurálásához használhatja a [Azure Portal hálózati beállításait](storsimple-8000-modify-device-config.md). 
* Ha a hálózati adapterek konfigurálása során problémákba ütközik, tekintse meg az [üzembe helyezési problémák elhárítása](./storsimple-8000-troubleshoot-deployment.md)című témakört.