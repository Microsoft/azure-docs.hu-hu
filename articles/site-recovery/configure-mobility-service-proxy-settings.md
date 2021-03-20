---
title: Mobilitási szolgáltatás proxybeállításait konfigurálja az Azure-hoz az Azure vész-helyreállításhoz | Microsoft Docs
description: Részletesen ismerteti, hogyan kell konfigurálni a mobilitási szolgáltatást, ha az ügyfelek proxyt használnak a forrás környezetében.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133220"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Mobilitási szolgáltatás proxybeállításait konfigurálhatja az Azure-hoz az Azure vész-helyreállításhoz

Ez a cikk útmutatást nyújt a hálózati konfigurációk testreszabásához a cél Azure-beli virtuális gépen (VM), ha az Azure-beli virtuális gépeket az egyik régióból a másikba, [Azure site Recovery](site-recovery-overview.md)használatával replikálja és helyreállítja.

Ennek a dokumentumnak a célja, hogy az Azure-ban Azure Site Recovery mobilitási szolgáltatás proxybeállítások konfigurálásához szükséges lépéseket adja meg az Azure-beli vész-helyreállítási forgatókönyvhöz. 

A proxyk olyan hálózati átjárók, amelyek engedélyezik vagy letiltják a hálózati kapcsolatokat a végpontokhoz. Általában a proxy az ügyfélszámítógépen kívüli gép, amely megpróbál hozzáférni a hálózati végpontokhoz. A megkerülési lista lehetővé teszi, hogy az ügyfél közvetlenül a végpontokhoz csatlakoztassa a kapcsolatot anélkül, hogy a proxyn kellene haladnia. Előfordulhat, hogy a hálózati rendszergazdák csak a hitelesített ügyfelek használhatják a proxyt a Felhasználónév és a jelszó beállításához. 

## <a name="before-you-start"></a>Előkészületek

[Ebből a forgatókönyvből](azure-to-azure-architecture.md)megtudhatja, hogyan biztosít a site Recovery vész-helyreállítást.
Ismerje meg a [hálózatkezelési útmutatót](azure-to-azure-about-networking.md) , ha az Azure-beli virtuális gépeket az egyik régióból a másikba, [Azure site Recovery](site-recovery-overview.md)használatával replikálja és helyreállítja.
Győződjön meg arról, hogy a proxy megfelelően van beállítva a szervezet igényei alapján.

## <a name="configure-the-mobility-service"></a>A mobilitási szolgáltatás konfigurálása

A mobilitási szolgáltatás csak a nem hitelesített proxykat támogatja. Kétféle módon adhatja meg Site Recovery-végpontokkal folytatott kommunikációhoz szükséges proxy adatait. 

### <a name="method-1-auto-detection"></a>1. módszer: automatikus észlelés

A mobilitási szolgáltatás automatikusan észleli a proxybeállításokat a környezeti beállítások vagy az IE beállításai közül (csak Windows esetén) a replikáció engedélyezésekor. 

- Windows operációs rendszer: a replikáció engedélyezése során a mobilitási szolgáltatás észleli az Internet Explorerben konfigurált proxybeállításokat a helyi rendszerfelhasználó számára. Ha a proxyt a helyi rendszerfiókhoz szeretné beállítani, a rendszergazda a PsExec használatával elindíthat egy parancssort, majd az Internet Explorert. 
- Windows operációs rendszer: a proxybeállítások környezeti változókként vannak konfigurálva http_proxy és no_proxy. 
- Linux operációs rendszer: a proxybeállítások a/etc/Profile vagy a/etc/Environment-ben vannak konfigurálva környezeti változókként http_proxy no_proxy. 
- Az automatikusan észlelt proxybeállítások a mobilitási szolgáltatás proxyjának ProxyInfo. conf fájlba kerülnek. 
- A ProxyInfo. conf alapértelmezett helye 
    - Windows: C:\ProgramData\Microsoft Azure site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>2. módszer: egyéni alkalmazásproxy-beállítások megadása

Ebben az esetben az ügyfél egyéni alkalmazásproxy-beállításokat biztosít a mobilitási szolgáltatás konfigurációs fájljában a ProxyInfo. conf fájlban. Ez a módszer lehetővé teszi, hogy az ügyfelek csak a mobilitási szolgáltatáshoz vagy egy másik Azure Site Recovery proxyhoz szolgáltassanak proxyt, mint egy proxy (vagy nem proxy) a gépen lévő alkalmazások többi részének.

## <a name="proxy-template"></a>Proxy sablon
A ProxyInfo. conf fájl a következő sablont tartalmazza [proxy] címe = http://1.2.3.4 port = 5678 BypassList = hypervrecoverymanager. windowsazure. com, login. microsoftonline. com, blob. Core. Windows. net. A BypassList nem támogatja a helyettesítő karaktereket (például "*. windows.net"), de a windows.net megadása elég jó az megkerüléshez. 

## <a name="next-steps"></a>Következő lépések:
- Olvassa el az Azure-beli virtuális gépek replikálásához szükséges [hálózatkezelési útmutatót](./azure-to-azure-about-networking.md)  .
- A vész-helyreállítás üzembe helyezése az Azure-beli [virtuális gépek replikálásával](./azure-to-azure-quickstart.md).
