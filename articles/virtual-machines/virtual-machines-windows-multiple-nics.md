---
title: "Créer une machine virtuelle Windows avec plusieurs cartes réseau | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Windows dotée de plusieurs cartes réseau à l’aide d’Azure PowerShell ou de modèles Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 95b2820d2f68be34cca7b8d414c581ba44a29804
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-windows-vm-with-multiple-nics"></a>Créer une machine virtuelle Windows avec plusieurs cartes d’interface réseau (NIC)
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consisterait à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes d’interface réseau. Pour plus d’informations, notamment sur la création de plusieurs cartes réseau dans vos propres scripts PowerShell, consultez la page consacrée au [déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , pensez à dimensionner la vôtre en conséquence.

## <a name="create-core-resources"></a>Créer les ressources de base
Vérifiez que la [dernière version d’Azure PowerShell est installée et configurée](/powershell/azureps-cmdlets-docs). Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

Créez d’abord un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUs` :

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Créez un compte de stockage qui contiendra vos machines virtuelles. L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Créer un réseau virtuel et des sous-réseaux
Définissez deux sous-réseaux de réseau virtuel : l’un pour le trafic frontal, l’autre pour le trafic principal. L'exemple suivant est une définition de deux sous-réseaux, nommés `mySubnetFrontEnd` et `mySubnetBackEnd` :

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Créez votre réseau virtuel et vos sous-réseaux. L’exemple suivant crée un réseau virtuel nommé `myVnet` :

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Créer plusieurs cartes réseau
Créez deux cartes réseau en attachant l’une au sous-réseau frontal et l’autre au sous-réseau principal. L’exemple qui suit permet de créer deux cartes réseau, nommées `myNic1` et `myNic2` :

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

En général, vous créez également un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. L’article [plus détaillé consacré aux machines virtuelles dotées de plusieurs cartes réseau](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) explique comment créer un groupe de sécurité réseau et affecter des cartes réseau.

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle
Maintenant, commencez à élaborer la configuration de votre machine virtuelle. La taille d’une machine virtuelle détermine le nombre maximal de cartes réseau qu’elle peut accueillir. En savoir plus sur les [tailles des machines virtuelles Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Tout d’abord, définissez vos informations d’identification de machine virtuelle dans la variable `$cred`, comme suit :

```powershell
$cred = Get-Credential
```

L’exemple suivant définit une machine virtuelle nommée `myVM` qui prend en charge jusqu’à deux cartes réseau (`Standard_DS2_v2`) :

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Créez le reste de la configuration de votre machine virtuelle. L’exemple suivant crée une machine virtuelle Windows Server 2012 R2 :

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Attachez les deux cartes réseau que vous avez créées :

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurez le stockage et le disque virtuel de votre nouvelle machine virtuelle :

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Enfin, créez une machine virtuelle :

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="add-a-nic-to-an-existing-vm"></a>Ajouter une carte réseau à une machine virtuelle existante

Il est désormais possible d’ajouter une carte réseau à une machine virtuelle existante. Pour utiliser cette fonctionnalité, vous devez tout d’abord libérer la machine virtuelle à l’aide de l’applet de commande Stop-AzureRmVM ci-dessous.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Ensuite, récupérez la configuration existante de la machine virtuelle à l’aide de l’applet de commande Get-AzureRmVM

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Vous pouvez créer une carte réseau dans le **même réseau virtuel que celui de la machine virtuelle** comme indiqué au début de cet article, ou attacher une carte réseau existante. Nous allons supposer que vous joignez une carte réseau existante `MyNic3` dans le réseau virtuel. 

```powershell
$nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId -Primary | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

> [!NOTE]
> Sur une machine virtuelle à plusieurs NIC, l’une d’entre elles doit être définie comme carte principale. Nous définissons donc la nouvelle carte en tant que carte principale. Si la carte réseau précédente sur la machine virtuelle était la carte principale, vous n’avez pas besoin de spécifier de commutateur principal. Si vous souhaitez modifier la carte réseau principale sur la machine virtuelle, procédez comme suit

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"

# Find out all the NICs on the VM and find which one is Primary
$vm.NetworkProfile.NetworkInterfaces

# Set the NIC 0 to be primary
$vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
$vm.NetworkProfile.NetworkInterfaces[1].Primary = $false

# Update the VM state in Azure
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## <a name="remove-a-nic-from-an-existing-vm"></a>Supprimer une carte réseau d’une machine virtuelle existante

Une carte réseau peut également être supprimée d’une machine virtuelle. Pour utiliser cette fonctionnalité, vous devez tout d’abord libérer la machine virtuelle à l’aide de l’applet de commande Stop-AzureRmVM ci-dessous.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Ensuite, récupérez la configuration existante de la machine virtuelle à l’aide de l’applet de commande Get-AzureRmVM

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Affichez maintenant toutes les cartes réseau sur la machine virtuelle et copiez le nom de celle que vous souhaitez supprimer

```powershell
$vm.NetworkProfile.NetworkInterfaces

Remove-AzureRmNetworkInterface -Name "myNic3" -ResourceGroupName "myResourceGroup"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Création de plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Grâce aux modèles Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Utilisez *copy* pour spécifier le nombre d’instances à créer :

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../azure-resource-manager/resource-group-create-multiple.md). 

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource permettant de créer `myNic1`, `MyNic2`, etc. Voici un exemple d’ajout de la valeur d’index :

```json
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Veillez à consulter les [tailles des machines virtuelles Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle. 



