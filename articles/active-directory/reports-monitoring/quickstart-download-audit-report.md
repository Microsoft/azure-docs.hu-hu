---
title: 'Rövid útmutató: Naplózási jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le naplózási jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68989680"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rövid útmutató: Naplózási jelentés letöltése Azure portál használatával

Ebből a rövid útmutatóból megtudhatja, hogyan tölthető le az elmúlt 24 órában a bérlőhöz tartozó naplófájlok CSV-fájlja. Legfeljebb 250 000 rekordot tölthet le a Azure Portalból. A rekordok a legutóbbiek szerint rendezve jelennek meg, így alapértelmezés szerint a legfrissebb 250 000 rekordokat kapja meg. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy Azure Active Directory-bérlő. 
* Az a felhasználó, aki a **biztonsági rendszergazda**, a **biztonsági olvasó** vagy a **globális rendszergazdai** szerepkör tagja a Bérlőnek. Ezen felül a bérlőnél minden felhasználó elérheti a magára vonatkozó auditnaplókat.

## <a name="quickstart-download-an-audit-report"></a>Első lépések: Naplózási jelentés letöltése

1. Navigáljon a [Azure Portal](https://portal.azure.com).
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza az **Auditnaplót**. 
4. Válassza az **elmúlt 24 órát** a **Dátumtartomány** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra auditnaplóját. 
5. Válassza a **Letöltés** gombot, válassza a **CSV** formátumot Fájlformátumként, és adjon meg egy fájlnevet a szűrt rekordokat tartalmazó CSV-fájl letöltéséhez. 

![Jelentéskészítés](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
