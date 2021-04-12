---
title: Az Azure Bastion hibáinak megoldása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan oldhatja meg az Azure Bastion-t.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 23b7a66afcc91cf1cf4a5dd9f720aad24ad40071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133990"
---
# <a name="troubleshoot-azure-bastion"></a>Az Azure Bastion hibaelhárítása

Ez a cikk bemutatja, hogyan lehet elhárítani az Azure Bastion-t.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Nem hozható létre NSG a AzureBastionSubnet

**K:** Amikor megpróbálok létrehozni egy NSG az Azure-beli megerősített alhálózaton, a következő hibaüzenet jelenik meg: *"a hálózati biztonsági csoport <NSG name> nem rendelkezik az Azure-beli megerősített alhálózat AzureBastionSubnet szükséges szabályokkal"*.

**A:** Ha NSG hoz létre és alkalmaz *AzureBastionSubnet*, győződjön meg arról, hogy a szükséges szabályokat felvette a NSG. A szükséges szabályok listáját lásd: [a NSG-hozzáférés és az Azure Bastion használata](./bastion-nsg.md). Ha nem adja hozzá ezeket a szabályokat, a NSG létrehozása/frissítése sikertelen lesz.

A NSG-szabályokra például a gyors útmutató [sablonjában](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)van lehetőség.
További információ: [NSG útmutató az Azure Bastion-hez](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Az SSH-kulcs nem használható az Azure Bastion használatával

**K:** Amikor megpróbálom böngészni az SSH-kulcsfájl, a következő hibaüzenet jelenik meg: *"a titkos SSH-kulcsnak-----BEGIN RSA titkos kulccsal-----kell kezdődnie, és a-----end RSA titkos kulccsal-----" végződik*.

**A:** Az Azure Bastion ezen a ponton csak RSA SSH-kulcsokat támogat. Győződjön meg arról, hogy egy, az SSH-hoz készült RSA titkos kulcsú kulcsfájl, a célként megadott virtuális gépen kiosztott nyilvános kulccsal. 

Például a következő paranccsal hozhat létre egy új RSA SSH-kulcsot:

**ssh-keygen-t RSA-b 4096-C " email@domain.com "**

Kimenet:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Nem lehet bejelentkezni a Windows-tartományhoz csatlakoztatott virtuális gépre

**K:** Nem tudok csatlakozni a Windows rendszerű virtuális géphez, amely a tartományhoz csatlakozik.

**A:** Az Azure Bastion támogatja a tartományhoz csatlakoztatott virtuális gépek bejelentkezését a Felhasználónév-jelszó alapú tartományba való bejelentkezéshez. A tartományi hitelesítő adatoknak a Azure Portal való megadásakor a username@domain bejelentkezéshez a tartomány *\ Felhasználónév* formátuma helyett az UPN () formátumot használja. Ez a tartományhoz csatlakoztatott vagy a hibrid csatlakozású (tartományhoz csatlakoztatott, valamint az Azure AD-hez csatlakoztatott) virtuális gépek esetében támogatott. Az Azure AD-hez csatlakoztatott virtuális gépek nem támogatottak.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Fájlátviteli problémák

**K:** Támogatja az Azure Bastion a fájlátvitelt?

**A:** A fájlátviteli szolgáltatás jelenleg nem támogatott. Dolgozunk a támogatás hozzáadásán.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Fekete képernyő a Azure Portal

**K:** Ha az Azure Bastion használatával próbálok csatlakozni, nem kösse be újra a célként megadott virtuális gépre, és egy fekete képernyőt kapok a Azure Portal.

**A:** Ez akkor fordulhat elő, ha hálózati kapcsolati probléma van a webböngésző és az Azure Bastion között (az ügyfél internetes tűzfala blokkolja a WebSocket forgalmat vagy hasonlót), illetve az Azure-alapú és a célként megadott virtuális gép között. A legtöbb esetben a AzureBastionSubnet vagy a cél virtuálisgép-alhálózaton alkalmazott NSG, amely blokkolja a virtuális hálózat RDP/SSH-forgalmát. Engedélyezze a WebSockets-forgalmat az ügyfél internetes tűzfalán, és keresse meg a NSG a cél virtuálisgép-alhálózaton.

## <a name="next-steps"></a>Következő lépések

További információ: a [megerősített GYIK](bastion-faq.md).
