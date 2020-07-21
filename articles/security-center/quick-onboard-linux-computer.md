---
title: Linux rendszerű számítógépek beléptetése a Azure Security Centerba | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vehet fel Linux rendszerű számítógépeket a Security Centerbe.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 72c0c33c973219a9701c8a7c8d45324681e14850
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529777"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Rövid útmutató: Linux rendszerű számítógépek felvétele a Security Centerbe
Az Azure-előfizetések előkészítése után az ügynök kiépítésével engedélyezheti az Azure-on kívül, például a helyszínen vagy más felhőkben futó Linux-erőforrások Security Centerét. Az ügynököt Log Analytics ügynöknek nevezzük, de OMS-ügynöknek is nevezzük.

Ez a rövid útmutató bemutatja, hogyan telepítheti az ügynököt egy Linux rendszerű számítógépre.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

Ennek a rövid útmutatónak a megkezdése előtt a Security Center Standard díjszabási szintjéhez kell tartoznia. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. Security Center Standard díjmentesen is kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Új Linux rendszerű számítógép hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. **Security Center – az Áttekintés** megnyílik.

   ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza az **Első lépések** elemet.
4. Válassza az **első lépések** fület. ![ Első lépések][3]

5. Az **Új nem Azure-beli számítógépek hozzáadása** menüpont alatt kattintson a **Konfigurálás** elemre, hogy megjelenítse a Log Analytics-munkaterületek listáját. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

    ![Nem Azure-beli számítógép hozzáadása](./media/quick-onboard-linux-computer/non-azure.png)

6. A **Közvetlen ügynök** lapon a **LINUX-ÜGYNÖK LETÖLTÉSE ÉS FELVÉTELE** területen kattintson a **Másolás** gombra a *wget* parancs másolásához.

7. Nyissa meg a Jegyzettömböt, és illessze be a parancsot. Olyan helyre mentse a fájlt, amelyet el lehet érni a Linux rendszerű számítógépről.

## <a name="install-the-agent"></a>Az ügynök telepítése

1. A Linux rendszerű számítógépen nyissa meg a korábban mentett fájlt. Jelölje ki a teljes tartalmát, másolja, nyisson meg egy terminálkonzolt, majd illessze be a parancsot.
2. Amikor a telepítés befejeződött, a *pgrep* parancs futtatásával ellenőrizheti, hogy az *omsagent* telepítve lett-e. A parancs visszaadja az *omsagent* folyamatazonosítóját az alább látható módon:

   ![Az ügynök telepítése][5]

Az ügynök naplói a következő helyen találhatók: */var/opt/Microsoft/omsagent/ \<workspace id> /log/*

  ![Az ügynök naplói][6]

Kis idő múlva (ez akár 30 perc is lehet) az új Linux rendszerű számítógép meg fog jelenni a Security Centerben.

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. A **Számítás** területen áttekintheti az összes virtuális gépet, számítógépet és javaslatot. Minden oszlop egy javaslatcsoportot képvisel. A szín a virtuális gép vagy számítógép aktuális biztonsági állapotát jelöli az adott javaslatra vonatkozóan. A Security Center emellett a biztonsági riasztásokban megjeleníti a számítógépekhez tartozó észleléseket is.

  ![Számítás panel][7] A **Számítás** panelen kétféle ikon található:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Ha már nincs rá szükség, eltávolíthatja az ügynököt a Linux rendszerű számítógépről.

Az ügynök eltávolítása:

1. Töltse le a Linux-ügynök [univerzális szkriptjét](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) a számítógépre.
2. Futtassa a csomag .sh-fájlját a *--purge* argumentummal a számítógépen, ami teljesen eltávolítja az ügynököt és annak konfigurációját.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy Linux rendszerű számítógépen kiépített egy ügynököt. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
