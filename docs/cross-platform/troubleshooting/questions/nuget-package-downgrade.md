---
title: Come si effettua il downgrade di un pacchetto NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 206336cbcdc85e5e2f3f010e947981cb96e7cd1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344562"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Come si effettua il downgrade di un pacchetto NuGet?

Visual Studio per Mac e Visual Studio hanno caratteristiche per la selezione di versioni meno recenti dei pacchetti e installarli automaticamente. simile al modo in cui l'aggiornamento dei pacchetti works. Questi passaggi sono descritti di seguito.

## <a name="visual-studio"></a>Visual Studio

1. Passare a **strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti**
2. Impostare il progetto in **progetto predefinito**
3. Usare questa sintassi:

    > Install-Package [nome pacchetto]-versione [tab per menu versione]

È possibile anche copiare/incollare il comando esatto dalla pagina del pacchetto NuGet. Esempio di xamarin. Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Nel progetto, fare clic sulla cartella di pacchetti e selezionare **Aggiungi pacchetti**
2. In searchbar, è possibile usare la sintassi seguente per cercare i pacchetti necessari:

    `[PackageName] version:*`

### <a name="examples"></a>Esempi 
- Elenca tutti i pacchetti di xamarin. Forms: 

    `Xamarin.Forms version:`

- Elenca tutti i pacchetti di 1.4.x xamarin. Forms: 

    `Xamarin.Forms version:1.4`

*Nota: Se si aggiunge uno spazio tra `version:` & il numero di versione, la ricerca si comporterà come se non è stata specificata una versione.*
