---
title: Migrer des machines virtuelles AWS vers Azure | Microsoft Docs
description: "Migrez une instance EC2 Amazon Web Services (AWS) vers des machines virtuelles Azure. Ce scénario utilise des disques gérés pour simplifier votre stockage dans le cloud."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: 6854ebe781220dfdf957fa1d89ec19d32e6febea
ms.lasthandoff: 02/11/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>Migrer d’Amazon Web Services (AWS) vers Azure Managed Disks

Vous pouvez migrer une instance EC2 Amazon Web Services (AWS) vers Azure en téléchargeant le disque dur virtuel. Si vous souhaitez créer plusieurs machines virtuelles dans Azure à partir de la même image, vous devez tout d’abord généraliser la machine virtuelle, puis exporter le disque dur virtuel généralisé dans un répertoire local. Une fois le disque dur virtuel téléchargé, vous pouvez créer une nouvelle machine virtuelle Azure qui utilise [Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour le stockage. Avec Azure Managed Disks, il n’est plus nécessaire de gérer des comptes de stockage pour les machines virtuelles IaaS Azure. Vous spécifiez simplement le type (Premium ou Standard) et la taille de disque dont vous avez besoin, et Azure crée et gère le disque pour vous. 

Avant de démarrer ce processus, n’oubliez pas d’étudier la [planification de la migration vers des disques gérés](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

Avant de télécharger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows à charger sur Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

La généralisation d’une machine virtuelle à l’aide de Sysprep supprime du disque dur virtuel toutes les informations propres à la machine, ainsi que les informations du compte personnel, et prépare la machine à son utilisation en tant qu’image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep. 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.



## <a name="export-the-vhd-from-an-ec2-instance"></a>Exporter le disque dur virtuel à partir d’une instance EC2

1.    Si vous utilisez Amazon Web Services (AWS), exportez l’instance EC2 vers un disque dur virtuel dans un compartiment S3 Amazon. Suivez les étapes décrites dans la section Exportation des instances Amazon EC2 de la documentation Amazon pour installer l’outil d’interface de ligne de commande (CLI) Amazon EC2 et exécutez la commande create-instance-export-task pour exporter l’instance EC2 vers un fichier de disque dur virtuel. Veillez à utiliser VHD pour la variable DISK_IMAGE_FORMAT lors de l’exécution de la commande create-instance-export-task. Le fichier de disque dur virtuel exporté est enregistré dans le compartiment Amazon S3 que vous désignez au cours de ce processus.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    Téléchargez le fichier de disque dur virtuel depuis le compartiment S3. Sélectionnez le fichier de disque dur virtuel, puis **Actions** > **Télécharger**.



## <a name="log-in-to-azure"></a>Connexion à Azure
Si vous n’avez pas encore installé PowerShell, lisez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenez les ID d’abonnement de vos abonnements disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement. Remplacez `<subscriptionID>` par l’ID de l’abonnement approprié.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtention du compte de stockage
Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Si vous prévoyez d’utiliser le disque dur virtuel pour créer un disque géré pour une machine virtuelle, l’emplacement du compte de stockage doit être le même que celui où vous allez créer la machine virtuelle.

Pour afficher les comptes de stockage disponibles, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#upload-the-vm-vhd-to-your-storage-account).

Si vous devez créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources dans lequel doit être créé le compte de stockage. Pour rechercher tous les groupes de ressources dans votre abonnement, tapez :
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Pour créer un groupe de ressources nommé **MyResourceGroup** dans la région **Ouest des États-Unis**, tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créez un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources en utilisant l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Les valeurs valides pour -SkuName sont :
   
   * **Standard_LRS** - Stockage localement redondant. 
   * **Standard_ZRS** - Stockage redondant dans une zone.
   * **Standard_GRS** - Stockage géo-redondant. 
   * **Standard_RAGRS** - Stockage géo-redondant avec accès en lecture. 
   * **Premium_LRS** - Stockage Premium localement redondant. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage

Utilisez l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) pour charger le disque dur virtuel vers un conteneur de votre compte de stockage. Cet exemple télécharge le fichier **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` vers un compte de stockage nommé **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Le fichier est placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier est **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Selon votre connexion réseau et la taille de votre fichier de disque dur virtuel, l’exécution de cette commande peut prendre un certain temps.

Enregistrez le chemin d’accès **URI de destination** pour l’utiliser ultérieurement si vous prévoyez de créer un disque géré ou une nouvelle machine virtuelle à l’aide du disque dur virtuel téléchargé.

### <a name="other-options-for-uploading-a-vhd"></a>Autres options de téléchargement d’un disque dur virtuel

Vous pouvez également télécharger un disque dur virtuel sur votre compte de stockage en utilisant l’un des moyens suivants :

-   [API de copie d’un objet blob de stockage Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Téléchargement d’objets blob dans Storage Explorer](https://azurestorageexplorer.codeplex.com/)

-   [Référence sur l’API REST du service Import/Export Storage](https://msdn.microsoft.com/library/dn529096.aspx)

    Nous recommandons l’utilisation de du service d’importation/exportation si l’estimation du temps de téléchargement est de plus de 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de la taille des données et de l’unité de transfert. 

    Le service Import/Export permet de copier sur un compte de stockage Standard. Vous devez effectuer une copie d’un compte de stockage Standard vers un compte de stockage Premium à l’aide d’un outil tel qu’AzCopy.

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Créer une image gérée à partir du disque dur virtuel téléchargé 

Créez une image gérée à l’aide de votre disque dur virtuel de système d’exploitation généralisé.


1.  Tout d’abord, définissez les paramètres communs :

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  Créez l’image à l’aide de votre disque dur virtuel de système d’exploitation généralisé.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>Configurer certaines variables de l’image

Nous devons tout d’abord collecter des informations de base sur l’image et créer une variable pour celle-ci. Cet exemple utilise une image de machine virtuelle gérée nommée **myImage** qui se trouve dans le groupe de ressources **myResourceGroup** dans l’emplacement **Ouest-Centre des États-Unis**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. Cet exemple crée un sous-réseau nommé **mySubnet** avec le préfixe d’adresse **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Création du réseau virtuel. Cet exemple crée un réseau virtuel nommé **myVnet** avec le préfixe d’adresse **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez une adresse IP publique. Cet exemple crée une adresse IP publique nommée **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Créez la carte réseau. Cet exemple crée une carte réseau nommée **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP

Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité réseau (NSG) qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un groupe de sécurité réseau nommé **myNsg** qui contient une règle nommée **myRdpRule** autorisant le trafic RDP sur le port 3389. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Créer une variable pour le réseau virtuel

Créez une variable pour le réseau virtuel terminé. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Obtenir les informations d’identification pour la machine virtuelle

L’applet de commande suivante ouvre une fenêtre dans laquelle vous allez entrer un nouveau nom d’utilisateur et un mot de passe à utiliser comme compte d’administrateur local pour accéder à distance à la machine virtuelle. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Définir des variables pour le nom de la machine virtuelle, le nom de l’ordinateur et la taille de la machine virtuelle

1. Créez des variables pour le nom de la machine virtuelle et le nom de l’ordinateur. Cet exemple définit **myVM** en tant que nom de la machine virtuelle et **myComputer** en tant que nom d’ordinateur.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Définissez la taille de la machine virtuelle. Cet exemple crée une machine virtuelle de taille **Standard_DS1_v2**. Pour plus d’informations, consultez la documentation relative aux [tailles de machines virtuelles](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Ajoutez le nom et la taille de la machine virtuelle à sa configuration.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Définir l’image de machine virtuelle en tant qu’image source pour la nouvelle machine virtuelle

Définissez l’image source à l’aide de l’ID de l’image de machine virtuelle gérée.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Définissez la configuration du système d’exploitation et ajoutez la carte réseau.

Indiquez le type de stockage (PremiumLRS ou StandardLRS) et la taille du disque du système d’exploitation. Cet exemple définit le type de compte sur **PremiumLRS**, la taille du disque sur **128 Go** et la mise en cache du disque sur **Lecture/écriture**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez la nouvelle machine virtuelle à l’aide de la configuration que nous avons créée et stockée dans la variable **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
 

