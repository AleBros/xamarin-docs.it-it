---
title: Visual Basic.NET portabile
description: Questa guida viene illustrato come utilizzare Visual Basic per scrivere i progetti libreria di classe portabile (PCL) che possono essere usati nelle soluzioni destinate a xamarin. IOS e xamarin.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f37ff8a2d07681ba8e4ec3ed6ad7e5bbd85e6502
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="portable-visual-basicnet"></a>Visual Basic.NET portabile

Progetti Xamarin iOS e Android non supportano in modo nativo Visual Basic. Tuttavia, gli sviluppatori possono utilizzare le librerie di classi portabile per la migrazione di codice Visual Basic esistente per iOS e Android, o scrivere una parte significativa della relativa logica dell'applicazione in Visual Basic. È possibile creare applicazioni di xamarin. Forms in Visual Basic (esclusi i renderer personalizzati, servizi di dipendenza e il codebehind XAML).

## <a name="requirements"></a>Requisiti

Supporto libreria di classi portabile è stato aggiunto in xamarin 4.10.1, 7.0.4 di xamarin. IOS e Xamarin Studio 4.2, vale a dire qualsiasi Xamarin i progetti creati con questi strumenti è possono incorporare gli assembly di Visual Basic PCL.

Per creare e compilare librerie di classi portabili Visual Basic è necessario utilizzare Visual Studio in Windows (Visual Studio 2012 o versione successiva).

> [!NOTE]
> Librerie di Visual Basic possono essere create solo e compilato con Visual Studio. Xamarin. IOS e xamarin non supportano il linguaggio Visual Basic.
>
> Se si lavora esclusivamente in Visual Studio è possibile fare riferimento il progetto di Visual Basic dai progetti di xamarin. IOS e xamarin.
>
> Se i progetti Android e iOS è necessario caricare anche in Visual Studio per Mac è necessario fare riferimento all'assembly di output dalla libreria di classi Portabile Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Creazione di una libreria di classi Portabile Basic.NET Visual

In questa sezione illustra in dettaglio come creare una libreria di classi portabile di Visual Basic in Visual Studio.
La libreria può fare riferimento in altri progetti, incluse le app xamarin. IOS, xamarin e xamarin. Forms.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile

Quando si aggiunge una PCL Visual Basic in Visual Studio è necessario scegliere un profilo che descrive quali piattaforme libreria deve essere compatibile con. I profili sono illustrati nell'introduzione di documento PCL.

I passaggi per creare una libreria di classi Portabile e scegliere il profilo sono:

1.  Nel **nuovo progetto** selezionare il **Visual Basic > libreria di classi (portabile)** opzione:

    [![](images/image1-sml.png "Creare una nuova libreria portabile Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio richiederà immediatamente con il codice seguente **Aggiungi libreria di classi portabile** finestra di dialogo in modo che sia possibile configurare il profilo. Seleziona le piattaforme, è necessario per supportare e premere **OK**.

    [![](images/image2-sml.png "Selezionare il profilo PCL scegliendo piattaforme")](images/image2.png#lightbox)

1.  Il progetto PCL Visual Basic verrà visualizzati come mostrato nel **Esplora** come segue:

    [![](images/image3-sml.png "Progetto di Visual Studio PCL vuoto")](images/image3.png#lightbox)


Libreria di classi Portabile è ora pronto per il codice di Visual Basic da aggiungere. Progetti libreria di classi Portabile possono fare riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti libreria di classi Portabile).

### <a name="editing-the-pcl-profile"></a>Modificare il profilo della libreria di classi Portabile

Il profilo della libreria di classi Portabile (che controlla quali piattaforme è compatibile con la libreria di classi Portabile) possono essere visualizzato e modificato facendo clic sul progetto e scegliendo **proprietà > Libreria > Modifica...** . La finestra di dialogo risulta è illustrato in questo screenshot:

 [![](images/image4-sml.png "Modificare il profilo PCL nelle proprietà del progetto")](images/image4.png#lightbox)

Se il profilo viene modificato dopo il codice è già stato aggiunto a una libreria di classi Portabile, è possibile che la libreria non verrà più compilato se il codice fa riferimento a funzionalità che non fanno parte del profilo selezionato.


## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come utilizzare il codice di Visual Basic nelle applicazioni Xamarin con Visual Studio e librerie di classi portabili. Anche se Xamarin non supporta direttamente Visual Basic, la compilazione di Visual Basic in una PCL consente a codice scritto con Visual Basic da includere nell'App iOS e Android.

Le pagine seguenti viene descritto come utilizzare Visual Basic.NET PCLs in modalità nativa o le app xamarin. Forms:

- [Compilazione di App xamarin. IOS e xamarin native che utilizzano Visual Basic](native-apps.md)
- [Creazione di applicazioni di xamarin. Forms in Visual Basic](xamarin-forms.md)


## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortableVB (esempio)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (esempio)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Lo sviluppo multipiattaforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)