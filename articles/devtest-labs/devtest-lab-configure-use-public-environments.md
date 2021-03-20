---
title: Nyilvános környezetek konfigurálása és használata Azure DevTest Labsban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat és használhat nyilvános környezeteket (Azure Resource Manager-sablonokat git-tárházban) a Azure DevTest Labsban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482666"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Nyilvános környezetek konfigurálása és használata Azure DevTest Labs
Azure DevTest Labs rendelkezik olyan [Azure Resource Manager sablonok nyilvános tárházával](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , amelyekkel környezetek hozhatók létre anélkül, hogy saját magának kellene csatlakozniuk a külső GitHub-forráshoz. Ez a tárház olyan gyakran használt sablonokat tartalmaz, mint például az Azure Web Apps, Service Fabric a fürt és a fejlesztői SharePoint-farmok környezete. Ez a szolgáltatás hasonló az összes létrehozott laborhoz tartozó összetevők nyilvános tárházához. A környezeti tárház lehetővé teszi, hogy gyorsan megkezdse az előre elkészített környezeti sablonokat, amelyek minimális bemeneti paraméterekkel rendelkeznek, hogy zökkenőmentes, első lépéseket biztosítson a további, a laborokon belüli Pásti-erőforrásokhoz. 

## <a name="configuring-public-environments"></a>Nyilvános környezetek konfigurálása
A labor tulajdonosaként engedélyezheti a tesztkörnyezet nyilvános környezetének tárházát a labor létrehozása során. A tesztkörnyezet nyilvános környezetének engedélyezéséhez **válassza a be lehetőséget** a **nyilvános környezetek** mezőben a tesztkörnyezet létrehozásakor. 

![Nyilvános környezet engedélyezése új laborhoz](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


A meglévő Labs esetében a nyilvános környezet tárháza nincs engedélyezve. Manuálisan engedélyezheti a sablonok használatát a tárházban. A Resource Manager-sablonok használatával létrehozott laborok esetében a tárház alapértelmezés szerint le van tiltva.

A tesztkörnyezetben engedélyezheti vagy letilthatja a nyilvános környezeteket, és a következő lépésekkel csak meghatározott környezeteket lehet elérhetővé tenni a tesztkörnyezet felhasználói számára: 

1. Válassza ki a laborhoz tartozó **konfigurációt és szabályzatokat** . 
2. A **virtuális gépek alapjai** szakaszban válassza a **nyilvános környezetek** lehetőséget.
3. A tesztkörnyezet nyilvános környezetének engedélyezéséhez válassza az **Igen** lehetőséget. Egyéb esetben a **Nem** választógombot jelölje be. 
4. Ha engedélyezte a nyilvános környezeteket, a tárház összes környezete alapértelmezés szerint engedélyezve van. Kiválaszthat egy környezetet, hogy az ne legyen elérhető a labor felhasználói számára. 

![Nyilvános környezetek lap](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Környezeti sablonok használata labor-felhasználóként
Labor-felhasználóként létrehozhat egy új környezetet az engedélyezett környezeti sablonok listájából. Ehhez egyszerűen válassza a **+ Hozzáadás** lehetőséget az eszköztáron a tesztkörnyezet lapon. Az alapok listája tartalmazza a nyilvános környezetű sablonokat, amelyeket a tesztkörnyezet rendszergazdája engedélyez a lista tetején.

![Nyilvános környezeti sablonok](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Következő lépések
Ez a tárház egy nyílt forráskódú tárház, amellyel a saját gyakran használt és hasznos Resource Manager-sablonokat is felveheti. A hozzájáruláshoz egyszerűen küldje el a lekéréses kérelmet az adattárhoz.  
