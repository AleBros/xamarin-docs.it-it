---
title: .NET Standard
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/12/2017
ms.openlocfilehash: 20bbb6386de3fb83df22de5df6e9ee99e3913eaa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="net-standard"></a>.NET Standard

## <a name="using-net-standard-library-projects-to-share-code"></a>Utilizzo di progetti di libreria Standard di .NET per condividere il codice

La libreria .NET Standard è una specifica formale delle API .NET che devono essere disponibili in tutti i runtime .NET. La motivazione alla base della libreria Standard è l'esigenza di creare maggiore uniformità nell'ecosistema .NET.
Lo standard [ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md) continua ad assicurare uniformità riguardo al comportamento del runtime .NET, ma non esiste una specifica simile per le BCL (Base Class Library) .NET per le implementazioni delle librerie .NET.

Può considerare come una rappresentazione semplificata di nuova generazione [libreria di classi portabile](https://msdn.microsoft.com/library/gg597391.aspx).
È una raccolta con un'API uniforme per tutte le piattaforme .NET incluso .NET Core singola. Sufficiente creare un'unica libreria Standard di .NET e usarlo da qualsiasi runtime che supporta la piattaforma Standard .NET.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

In questa sezione illustra in dettaglio come creare e usare una libreria Standard di .NET in Visual Studio per Mac. Vedere la sezione di esempio della libreria Standard di .NET per un'implementazione completa.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria Standard di .NET

Aggiunta di una libreria Standard di .NET per la soluzione è piuttosto semplice.

1. Nella finestra di dialogo Aggiungi nuovo progetto, selezionare il `.NET Core` categoria e quindi selezionare `Class Library(.NET Core)`.

  **Nota:** questo modello verrà rinominato in `.NET Standard` in una versione futura di Visual Studio per Mac.

  ![Creare una libreria di classi .NET Core](net-standard-images/vsm01.png)

2. Il progetto di libreria Standard di .NET verrà visualizzati come mostrato in Esplora soluzioni. Il nodo dipendenze indicherà che utilizza la libreria di [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![Nodo dipendenze nella soluzione indica .NET Standard](net-standard-images/vsm02.png)

#### <a name="editing-net-standard-library-settings"></a>Modifica delle impostazioni della libreria Standard di .NET

Le impostazioni della libreria Standard di .NET possono essere visualizzate e modificate facendo clic sul progetto e selezionare `Options` come illustrato in questo screenshot:

![Modifica Standard di .NET framework di destinazione nelle opzioni di progetto](net-standard-images/vsm03.png)

In cui è possibile modificare la versione di `netstandard` modificando il `Target Framework` il valore di elenco a discesa.

**Inoltre:** è possibile modificare il `.csproj` direttamente per modificare questo valore.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

In questa sezione illustra in dettaglio come creare e usare una libreria Standard di .NET in Visual Studio. Vedere la sezione di esempio della libreria Standard di .NET per un'implementazione completa.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria Standard di .NET

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Aggiunta di una libreria Standard di .NET per la soluzione è piuttosto semplice.

1. Nella finestra di dialogo Aggiungi nuovo progetto, selezionare il `.NET Standard` categoria e quindi selezionare `Class Library(.NET Standard)`.

  ![](net-standard-images/vs01.png "Creare una nuova libreria di classi .NET Standard")

2. Il progetto di libreria Standard di .NET verrà visualizzati come mostrato in Esplora soluzioni. Il nodo dipendenze indicherà che utilizza la libreria di [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![](net-standard-images/vs02.png ".NET standard progetto nella soluzione")

#### <a name="editing-net-standard-library-settings"></a>Modifica delle impostazioni della libreria Standard di .NET

Le impostazioni della libreria Standard di .NET possono essere visualizzate e modificate facendo clic sul progetto e selezionare `Properties` come illustrato in questo screenshot:

![](net-standard-images/vs03.png "Fare riferimento a una libreria .NET Standard nello stesso modo di altri progetti")

In cui è possibile modificare la versione di `netstandard` modificando il `Target Framework` il valore di elenco a discesa.

**Inoltre:** è possibile modificare il `.csproj` direttamente per modificare questo valore.

#### <a name="using-net-standard-library"></a>Con la libreria Standard di .NET

Dopo aver creata una libreria Standard di .NET, è possibile aggiungere un riferimento da qualsiasi progetto di applicazioni o librerie compatibile nello stesso modo, che si aggiungono in genere i riferimenti. In Visual Studio, fare clic sul nodo Riferimenti e scegliere `Add Reference...` quindi passare il `Solution : Projects` scheda come illustrato:

![](net-standard-images/vs04.png "In Visual Studio, fare doppio clic sul nodo Riferimenti e scegliere Aggiungi riferimento … quindi passare alla scheda Esplora progetti, come illustrato")

-----

