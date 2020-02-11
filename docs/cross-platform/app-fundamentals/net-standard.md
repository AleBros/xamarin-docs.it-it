---
title: Usare librerie di .NET Standard per condividere il codice
description: In questo documento viene descritto come utilizzare le librerie di .NET Standard per condividere il codice. Viene illustrato come creare una libreria di .NET Standard, modificarne le impostazioni e usarla in un'applicazione.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: cae59053374f673a56d02e86cd59fb85f313c41b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016806"
---
# <a name="net-standard-library-code-sharing"></a>Condivisione del codice della libreria .NET Standard

Le librerie .NET Standard hanno un'API uniforme per tutte le piattaforme .NET, tra cui Xamarin e .NET Core. Creare una singola libreria di .NET Standard e usarla da qualsiasi runtime che supporta la piattaforma di .NET Standard. Per informazioni dettagliate sulle piattaforme supportate, fare riferimento a [questo grafico](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) .

Sebbene .NET Standard versioni da 1,0 a 1,6 forniscano subset incrementali di .NET Framework, .NET Standard 2,0 fornisce il livello migliore di supporto per le applicazioni Xamarin e per il porting di librerie di classi portabili esistenti.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

Questa sezione illustra come creare e usare una libreria di .NET Standard usando Visual Studio per Mac.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria di .NET Standard

È possibile aggiungere una libreria di .NET Standard alla soluzione attenendosi alla procedura seguente:

1. Nella finestra di dialogo **Aggiungi nuovo progetto** selezionare la categoria **.NET Core** e quindi selezionare **.NET standard library**:

    ![Creare una libreria di .NET Standard](net-standard-images/vsm01-m157.png "Creazione di una nuova libreria .NET Standard")

2. Nella schermata successiva scegliere il Framework di destinazione- **.NET Standard 2,0** è consigliato:

    [![scegliere .NET Standard 2,0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. Nella schermata finale digitare il nome del progetto e fare clic su **Crea**.

4. Il progetto di libreria .NET Standard verrà visualizzato come illustrato nella Esplora soluzioni. Il nodo dipendenze indicherà che la libreria USA [NETStandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Il nodo dipendenze nella soluzione indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Modifica delle impostazioni della libreria .NET Standard

Le impostazioni della libreria .NET Standard possono essere visualizzate e modificate facendo clic con il pulsante destro del mouse sul progetto e selezionando `Options`, come illustrato nello screenshot seguente:

![Modificare .NET Standard Framework di destinazione nelle opzioni del progetto](net-standard-images/vsm03-m157.png "Modificare la versione del Framework di destinazione .NET Standard nelle opzioni del progetto")

All'interno di è possibile modificare la versione di `netstandard` modificando il valore di `Target Framework` a discesa.

**Inoltre:** È possibile modificare direttamente il `.csproj` per modificare questo valore.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Questa sezione illustra come creare e usare una libreria di .NET Standard usando Visual Studio.

### <a name="creating-a-net-standard-library"></a>Creazione di una libreria di .NET Standard

L'aggiunta di una libreria di .NET Standard alla soluzione è piuttosto semplice.

1. Nella finestra di dialogo **nuovo progetto** selezionare la categoria **.NET standard** , quindi selezionare **libreria di classi (.NET standard)** .

    ![Creazione di una nuova libreria di classi .NET Standard](net-standard-images/vs01-w157.png "Crea nuova libreria di classi .NET Standard")

2. Il progetto di libreria .NET Standard verrà visualizzato come illustrato nella Esplora soluzioni. Il nodo dipendenze indicherà che la libreria USA [NETStandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![NETStandard. Library nella cartella del progetto](net-standard-images/vs02-w157.png "Progetto .NET Standard nella soluzione")

### <a name="editing-net-standard-library-settings"></a>Modifica delle impostazioni della libreria .NET Standard

Le impostazioni della libreria .NET Standard possono essere visualizzate e modificate facendo clic con il pulsante destro del mouse sul progetto e selezionando **Proprietà** , come illustrato nello screenshot seguente:

![Modificare i Framework di destinazione .NET standard nelle proprietà del progetto](net-standard-images/vs03-w157.png "Fare riferimento a una libreria di .NET Standard allo stesso modo di altri progetti")

**Inoltre:** È possibile modificare direttamente il `.csproj` per modificare l'elemento `TargetFramework` e modificare la versione di destinazione, ad esempio `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Uso di un progetto di libreria .NET Standard

Una volta creata una libreria di .NET Standard, è possibile aggiungervi un riferimento da qualsiasi applicazione o progetto di libreria compatibile nello stesso modo in cui si aggiungono normalmente i riferimenti. In Visual Studio fare clic con il pulsante destro del mouse sul nodo riferimenti e scegliere **Aggiungi riferimento** , quindi passare alla scheda **soluzioni > soluzione** , come illustrato di seguito:

![Riferimento a una libreria di .NET Standard](net-standard-images/vs04.png "In Visual Studio fare clic con il pulsante destro del mouse sul nodo riferimenti e scegliere Aggiungi riferimento. passare quindi alla scheda progetti soluzione come illustrato")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET Standard e Xamarin.Forms per lo sviluppatore .NET (video)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Collegamenti correlati

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -informazioni dettagliate e confronto con PCL.
