---
title: Visual Basic e .NET Standard
description: Questa guida illustra come usare Visual Basic per scrivere .NET Standard progetti che possono essere usati nelle soluzioni destinate a Novell. iOS e Novell. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: 1e58dd4d7310c7f903433ce0b84123c39fbb5c8e
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959110"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic e .NET Standard

I progetti Novell Android e iOS non supportano in modo nativo Visual Basic; Tuttavia, gli sviluppatori possono usare le librerie di [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) per eseguire la migrazione del codice Visual Basic esistente ad Android e iOS o per scrivere parte significativa della logica dell'applicazione in Visual Basic. Le applicazioni Novell. Forms possono essere create interamente in Visual Basic (esclusi i renderer personalizzati, i servizi di dipendenza e il code-behind XAML).

## <a name="requirements"></a>Requisiti

Per creare e compilare Visual Basic librerie .NET Standard è necessario usare Visual Studio in Windows (Visual Studio 2017 o versione successiva).

> [!NOTE]
> Le librerie di Visual Basic possono essere create e compilate solo con Visual Studio. Novell. Android e Novell. iOS non supportano la lingua Visual Basic.
>
> Se si lavora esclusivamente in Visual Studio, è possibile fare riferimento al progetto Visual Basic dai progetti Novell. Android e Novell. iOS.
>
> Se i progetti Android e iOS devono essere caricati anche in Visual Studio per Mac è necessario fare riferimento all'assembly di output dall'assembly Visual Basic.

## <a name="creating-a-visual-basicnet-net-standard-library"></a>Creazione di una libreria di .NET Standard Visual Basic.NET

Questa sezione illustra come creare una libreria di .NET Standard Visual Basic usando Visual Studio 2019.
È quindi possibile fare riferimento alla libreria in altri progetti, incluse le app Novell. Android, Novell. iOS e Novell. Forms.

Quando si aggiunge una libreria di .NET Standard Visual Basic in Visual Studio, è necessario prestare attenzione a scegliere il tipo di progetto corretto:

1. Da Visual Studio 2019 scegliere **Crea un nuovo progetto**.

2. Digitare **Visual Basic libreria** per filtrare le opzioni del progetto e scegliere l'opzione **libreria di classi (.NET standard)** con l'icona Visual Basic:

    [Filtro![per la libreria Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. Nella schermata successiva, digitare un nome per il progetto e premere **Crea**.

4. Il progetto Visual Basic verrà visualizzato come illustrato nel **Esplora soluzioni** simile al seguente:

    [![progetto Visual Basic vuoto](images/new-library-sml.png)](images/new-library.png#lightbox)

Il progetto è ora pronto per l'aggiunta del codice Visual Basic. È possibile fare riferimento ai progetti .NET Standard da altri progetti (progetti di applicazioni o progetti di libreria).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come utilizzare Visual Basic codice nelle applicazioni Novell con Visual Studio. Anche se Novell non supporta direttamente Visual Basic, la compilazione di Visual Basic in una libreria .NET Standard consente l'inclusione di codice scritto con Visual Basic nelle app Android e iOS.

Le pagine seguenti descrivono come usare le librerie di .NET Standard di Visual Basic.NET nelle app native o Novell. Forms:

- [Compilazione di app Novell. iOS e Novell. Android native che usano VB](native-apps.md)
- [Compilazione di app Novell. Forms con VB](xamarin-forms.md)

## <a name="related-links"></a>Collegamenti correlati

- [TaskyVB (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard e Novell](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)
