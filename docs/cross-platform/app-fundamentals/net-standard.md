---
title: Usare le librerie .NET Standard per condividere il codice
description: Questo documento descrive come usare le librerie .NET Standard per condividere il codice. Illustra la creazione di una libreria .NET Standard, modificandone le impostazioni e usarlo in un'applicazione.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: d07b248b36feee909db9c863eb17f1a900f58e60
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61191367"
---
# <a name="net-standard-library-code-sharing"></a>La condivisione del codice di libreria .NET standard

Librerie .NET standard hanno un'API uniforme per tutte le piattaforme .NET incluso .NET Core e Xamarin. Creare una singola libreria .NET Standard e usarlo da qualsiasi runtime che supporta la piattaforma Standard .NET. Fare riferimento a [questo grafico](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) per informazioni dettagliate sulle piattaforme supportate.

Versioni di .NET Standard 1.0-1.6 fornire in modo incrementale più grande subset di .NET Framework, .NET Standard 2.0 fornisce il migliore livello di supporto per le applicazioni Xamarin e per il porting di librerie di classi portabile esistente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

In questa sezione illustra in dettaglio come creare e usare una libreria .NET Standard con Visual Studio per Mac.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria .NET Standard

È possibile aggiungere una libreria .NET Standard per la soluzione con questi passaggi:

1. Nel **Aggiungi nuovo progetto** finestra di dialogo, seleziona la **.NET Core** categoria e quindi selezionare **libreria .NET Standard**:

    ![Creare una libreria .NET Standard](net-standard-images/vsm01-m157.png "creazione di una libreria nuovo .NET Standard")

2. Nella schermata successiva, scegliere il framework di destinazione - **.NET Standard 2.0** è consigliabile:

    [![Scegliere .NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. Nella schermata finale, digitare il nome del progetto e fare clic su **Create**.

4. Come mostrato in Esplora soluzioni, verrà visualizzato il progetto di libreria .NET Standard. Nel nodo ' Dependencies indicherà che la libreria Usa la [netstandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Nodo di dipendenze nella soluzione indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Modifica delle impostazioni di libreria .NET Standard

Le impostazioni della libreria .NET Standard possono essere visualizzate e modificate facendo clic sul progetto e selezionando `Options` come illustrato in questo screenshot:

![Modificare il framework di destinazione .NET Standard in Opzioni progetto](net-standard-images/vsm03-m157.png "modificare la versione del Framework di destinazione Standard .NET in Opzioni progetto")

All'interno è possibile modificare la versione di `netstandard` modificando il `Target Framework` valore di elenco a discesa.

**Inoltre:** È possibile modificare il `.csproj` direttamente per modificare questo valore.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Questa sezione illustra come creare e usare una libreria .NET Standard con Visual Studio.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria .NET Standard

Aggiunta di una libreria .NET Standard per la soluzione è abbastanza semplice.

1. Nel **nuovo progetto** finestra di dialogo, seleziona la **.NET Standard** categoria e quindi selezionare **libreria di classi (.NET Standard)**.

    ![Creazione di una nuova libreria di classi Standard .NET](net-standard-images/vs01-w157.png "Crea nuova libreria di classi .NET Standard")

2. Come mostrato in Esplora soluzioni, verrà visualizzato il progetto di libreria .NET Standard. Nel nodo ' Dependencies indicherà che la libreria Usa la [netstandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Netstandard. Library nella cartella del progetto](net-standard-images/vs02-w157.png "progetto .NET Standard nella soluzione")

### <a name="editing-net-standard-library-settings"></a>Modifica le impostazioni della libreria .NET Standard

Le impostazioni della libreria .NET Standard possono essere visualizzate e modificate facendo clic sul progetto e selezionando **proprietà** come illustrato in questo screenshot:

![Modificare il framework di destinazione standard .NET nelle proprietà del progetto](net-standard-images/vs03-w157.png "fanno riferimento a una libreria .NET Standard, esattamente come altri progetti")

**Inoltre:** È possibile modificare il `.csproj` direttamente per modificare il `TargetFramework` elemento e modificare la versione di destinazione (ad es. `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Usando un progetto di libreria .NET Standard

Dopo aver creata una libreria .NET Standard, è possibile aggiungere un riferimento al codice da qualsiasi progetto di applicazione o una libreria compatibile nello stesso modo che è in genere aggiungere riferimenti. In Visual Studio, fare clic sul nodo Riferimenti e scegliere **Aggiungi riferimento...**  passerà successivamente al **progetti > soluzione** scheda come illustrato:

![Riferimento a una libreria .NET Standard](net-standard-images/vs04.png "In Visual Studio, fare doppio clic sul nodo Riferimenti e scegliere Aggiungi riferimento... quindi passare alla scheda dei progetti della soluzione, come mostrato")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET standard e xamarin. Forms per gli sviluppatori .NET (video)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Collegamenti correlati

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -informazioni dettagliate e il confronto con libreria di classi Portabile.
