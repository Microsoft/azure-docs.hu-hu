---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95554220"
---
## <a name="deleting-personal-information"></a>Személyes adatok törlése

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

A személyes adatok az importálási/exportálási szolgáltatáshoz (a Portálon és az API-n keresztül) kapcsolódnak az importálási és exportálási műveletek során. Az alábbi folyamatokban használt adatműveletek a következők:

- Kapcsolattartó neve
- Telefonszám
- E-mail
- Utca, házszám
- City
- Irányítószám
- Állapot
- Ország/tartomány/régió
- Meghajtóazonosító
- Szállítmányozó fiókszáma
- Szállítmány nyomkövetési száma

Importálási/exportálási feladatok létrehozásakor a felhasználók kapcsolattartási adatokat és szállítási címet biztosítanak. A személyes adatokat a rendszer akár két különböző helyen tárolja: a feladatban és opcionálisan a portál beállításaiban. A személyes adatok csak akkor tárolódnak a portál beállításaiban, ha bejelöli ezt a jelölőnégyzetet, a **szállító és a visszaadott cím mentése alapértelmezettként** a *Return Shipping info* szakaszban az exportálási folyamat során.

A személyes kapcsolattartási adatokat a következő módokon lehet törölni:

- A feladatokkal mentett adatok törlődnek a feladatokkal. A felhasználók manuálisan törölhetik a feladatokat, a befejezett feladatokat pedig 90 nap után automatikusan törli a rendszer. A feladatokat manuálisan törölheti a REST API vagy a Azure Portal használatával. Ha törölni szeretné a feladatot a Azure Portalban, lépjen az importálási/exportálási feladatokhoz, majd kattintson a *Törlés* elemre a parancssorból. Az importálási/exportálási feladatok REST API használatával történő törlésével kapcsolatos részletekért tekintse meg az [importálási/exportálási feladatok törlését](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs)ismertető témakört.

- A portál beállításaiban mentett kapcsolattartási adatokat a portál beállításainak törlésével lehet eltávolítani. A portál beállításait a következő lépésekkel törölheti:
  - Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  - Kattintson a *Beállítások* ikonra az ![ Azure beállítások ikon](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kattintson az *összes beállítás exportálása* (az aktuális beállítások fájlba mentéséhez) elemre `.json` .
  - Kattintson az *összes beállítás és privát irányítópultok törlése* lehetőségre az összes beállítás, beleértve a mentett kapcsolattartási adatok törléséhez.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a következő helyen: [megbízhatósági központ](https://www.microsoft.com/trustcenter)