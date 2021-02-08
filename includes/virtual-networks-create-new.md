---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808469"
---
## <a name="create-the-virtual-network-and-subnet"></a>A virtuális hálózat és az alhálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | Válassza az **új létrehozása** elemet, írja be **\<resource-group-name>** , majd kattintson az OK gombra, vagy válasszon ki egy meglévőt **\<resource-group-name>** paraméterek alapján. |
    | **Példány adatai** |                                                                 |
    | Name             | Be **\<virtual-network-name>**                                    |
    | Region           | Válassza **\<region-name>** |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Be **\<IPv4-address-space>** |

5. Az **alhálózat neve** alatt válassza ki az **alapértelmezett** szót.

6. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | Be **\<subnet-name>** |
    | Alhálózati címtartomány | Be **\<subnet-address-range>**

7. Kattintson a **Mentés** gombra.

8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.