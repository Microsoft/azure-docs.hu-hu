---
title: Kulcsok tárolása a kulcstartóban Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan tárolhatja a titkokat egy Azure Key Vault, és hogyan használhatja őket virtuális gépek, képletek vagy környezetek létrehozása során.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481646"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Titkos kulcsok tárolása Azure DevTest Labsban található kulcstartóban
Előfordulhat, hogy egy összetett titkot kell megadnia, ha Azure DevTest Labs: jelszót használ a Windows rendszerű virtuális géphez, a Linux rendszerű virtuális gép nyilvános SSH-kulcsához vagy a személyes hozzáférési tokenhez a git-tárház egy összetevőn keresztüli klónozásához. A titkok általában hosszúak, és véletlenszerű karakterekkel rendelkeznek. Ezért a megadásuk bonyolult és kényelmetlen lehet, különösen akkor, ha ugyanazt a titkos kódot többször használja.

A probléma megoldásához, valamint a titkos kulcsok biztonságának fenntartásához a DevTest Labs támogatja a titkos kódok [Azure Key vaultban](../key-vault/general/overview.md)való tárolását. Amikor a felhasználó első alkalommal ment egy titkos kulcsot, a DevTest Labs szolgáltatás automatikusan létrehoz egy kulcstárolót ugyanabban az erőforráscsoporthoz, amely tartalmazza a labort, és a kulcsot a kulcstartóban tárolja. A DevTest Labs külön kulcstartót hoz létre minden felhasználó számára. 

Vegye figyelembe, hogy a labor felhasználónak először létre kell hoznia egy Tesztkörnyezet virtuális gépet, mielőtt titkos kulcsot hozna létre a kulcstartóban. Ennek az az oka, hogy a DevTest Lab szolgáltatásnak hozzá kell rendelnie a labor felhasználót egy érvényes felhasználói dokumentumhoz, mielőtt a titkos kulcsokat a kulcstartóban szeretné létrehozni és tárolni. 


## <a name="save-a-secret-in-azure-key-vault"></a>Titkos kód mentése Azure Key Vault
Ha Azure Key Vault szeretné menteni a titkot, hajtsa végre a következő lépéseket:

1. Válassza a **saját titkok** lehetőséget a bal oldali menüben.
2. Adja meg a titok **nevét** . Ez a név jelenik meg a legördülő listában a virtuális gép, a képlet vagy a környezet létrehozásakor. 
3. Adja meg a titkot **értékként**.

    ![Titkos kód tárolása](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Titkos kód használata Azure Key Vault
Ha egy titkot kell megadnia egy virtuális gép, egy képlet vagy egy környezet létrehozásához, manuálisan is megadhat egy titkos kulcsot, vagy kijelölhet egy mentett titkot a kulcstartóból. A Key vaultban tárolt titkos kód használatához hajtsa végre a következő műveleteket:

1. Válassza **a mentett titkos kód használata** lehetőséget. 
2. A **titkos** kód kiválasztásához válassza ki a titkot a legördülő listából. 

    ![Titok használata a virtuális gépen](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Titkos kód használata Azure Resource Manager-sablonban
Megadhatja a titkos kulcs nevét egy olyan Azure Resource Manager sablonban, amely a virtuális gép létrehozásához használatos a következő példában látható módon:

![Titkos kód használata a képletben vagy a környezetben](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Következő lépések

- [Virtuális gép létrehozása a titok használatával](devtest-lab-add-vm.md) 
- [Képlet létrehozása a titkos kód használatával](devtest-lab-manage-formulas.md)
- [Környezet létrehozása a titok használatával](devtest-lab-create-environment-from-arm.md)
