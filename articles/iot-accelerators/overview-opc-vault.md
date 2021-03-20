---
title: Mi az OPC-tár – Azure | Microsoft Docs
description: Ez a cikk az OPC-tároló áttekintését tartalmazza. Az OPC UA-alkalmazások számára a felhőben konfigurálhatja, regisztrálhatja és kezelheti a tanúsítványok életciklusát.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281541"
---
# <a name="what-is-opc-vault"></a>Mi az az OPC Vault?

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az OPC-tár egy olyan szolgáltatás, amely a tanúsítványok életciklusát konfigurálhatja, regisztrálhatja és kezelheti a felhőben lévő OPC UA-kiszolgáló és ügyfélalkalmazások számára. Ez a cikk az OPC-tár egyszerű felhasználási eseteit ismerteti.

## <a name="certificate-management"></a>Tanúsítványkezelés

Egy gyártó vállalatnak például csatlakoznia kell az OPC UA-kiszolgáló számítógépéhez az újonnan létrehozott ügyfélalkalmazás számára. Ha a gyártó a kiszolgáló első hozzáférését végzi, az OPC UA-kiszolgáló alkalmazásban egy hibaüzenet jelenik meg, amely jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus az OPC UA-kiszolgáló számítógépén található, hogy megakadályozza a jogosulatlan hozzáférések elérését, ami megakadályozza a gonosz hackelést az üzemi emeleten.

## <a name="application-security-management"></a>Alkalmazások biztonságának kezelése
Egy biztonsági szakember az OPC Vault-szolgáltatást használja az OPC UA-kiszolgáló bármely ügyfélalkalmazás általi kommunikációhoz való egyszerű engedélyezéséhez, mivel az OPC-tároló rendelkezik a tanúsítványok beállításjegyzékének, tárolásának és életciklus-kezelésének összes funkcióját. Az OPC UA-kiszolgáló most már biztonságosan csatlakozik, és képes kommunikálni az újonnan létrehozott ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>Az OPC-tár teljes architektúrája
Az alábbi ábra az OPC-tár architektúráját mutatja be.

![OPC-tár architektúrája](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-tárolót és annak felhasználási lehetőségeit, itt látható a következő lépés:

[OPC-tár architektúrája](overview-opc-vault-architecture.md)