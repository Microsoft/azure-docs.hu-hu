---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor
description: Ez a cikk bemutatja, hogyan csatlakoztathatók a ITSM-termékek és-szolgáltatások a biztonságos exportálással Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és felügyeletéhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: b1295904f25d38c97578fa6fd6ede4ecd50c0456
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728977"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Az Azure és a ITSM-eszközök összekapcsolhatók a biztonságos exportálás használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a kapcsolatot az IT szolgáltatás-kezelő (ITSM) termék vagy szolgáltatás között a biztonságos exportálás használatával.

A biztonságos exportálás a [it-szolgáltatásmenedzsmenti csatoló (ITSMC)](./itsmc-overview.md)frissített verziója. Mindkét verzió lehetővé teszi, hogy munkaelemeket hozzon létre egy ITSM eszközben, amikor Azure Monitor riasztásokat küld. A funkciók mérőszám-, napló-és műveletnapló-riasztásokat tartalmaznak.

A ITSMC a Felhasználónév és a jelszó hitelesítő adatait használja. A biztonságos exportálás erősebb hitelesítéssel rendelkezik, mert Azure Active Directoryt (Azure AD) használ. Az Azure AD a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása. Segít a felhasználóknak bejelentkezni, és hozzáférni a belső vagy külső erőforrásokhoz. Az Azure AD és a ITSM segítségével azonosíthatja a külső rendszernek elküldett Azure-riasztásokat (az Azure AD-alkalmazás azonosítója alapján).

> [!NOTE]
> Az Azure ITSM-eszközökhöz való összekapcsolásának lehetősége a biztonságos exportálás előzetes verzióban érhető el.

## <a name="secure-export-architecture"></a>Biztonságos exportálási architektúra

A biztonságos exportálási architektúra a következő új képességeket mutatja be:

* **Új műveleti csoport**: a rendszer a ITSM eszközre küld riasztásokat a biztonságos webhook műveleti csoporton keresztül, a ITSMC által használt ITSM-műveleti csoport helyett.
* **Azure ad-hitelesítés**: a hitelesítés a Felhasználónév/jelszó hitelesítő adatai helyett az Azure ad-n keresztül történik.

## <a name="secure-export-data-flow"></a>Az adatforgalom biztonságos exportálása

A biztonságos exportálási adatfolyam lépései a következők:

1. Azure Monitor a biztonságos exportálás használatára konfigurált riasztást küld.
2. A riasztási adattartalmat egy biztonságos webhook-művelet küldi el a ITSM eszközhöz.
3. A ITSM alkalmazás ellenőrzi az Azure AD-t, ha a riasztás engedélyezve van a ITSM eszköz megadására.
4. Ha a riasztás engedélyezve van, az alkalmazás:
   
   1. Létrehoz egy munkaelemet (például egy incidenst) a ITSM eszközben.
   2. A konfigurációs tétel (CI) AZONOSÍTÓjának kötése az ügyfél-felügyeleti adatbázishoz (CMDB).

![Diagram, amely bemutatja, hogyan kommunikál a ITSM eszköz az Azure-ban A D, az Azure-riasztásokkal és egy műveleti csoporttal.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>A biztonságos exportálás előnyei

Az integráció fő előnyei a következők:

* **Jobb hitelesítés**: az Azure ad biztonságosabb hitelesítést biztosít a ITSMC-ben gyakran előforduló időtúllépések nélkül.
* **A ITSM eszközben megoldott riasztások: a** metrikus riasztások "tüzelt" és "megoldott" állapotokat valósítanak meg. Ha a feltétel teljesül, a riasztás állapota "tüzelt". Ha a feltétel már nem teljesül, a riasztás állapota "feloldva". A ITSMC-ben a riasztások nem oldhatók fel automatikusan. A biztonságos exportálással a megoldott állapot a ITSM eszközre áramlik, így automatikusan frissül.
* **[Gyakori riasztási séma](./alerts-common-schema.md)**: a ITSMC a riasztási adatok sémája eltér a riasztás típusa alapján. A biztonságos exportálásnál létezik egy általános séma az összes riasztási típushoz. Ez a közös séma tartalmazza a CI-t az összes riasztási típushoz. Az összes riasztási típus képes lesz a CI és a CMDB kötésére.

## <a name="next-steps"></a>Következő lépések

* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-overview.md)