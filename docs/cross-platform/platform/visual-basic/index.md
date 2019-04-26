---
title: Portabile Visual Basic.NET
description: Questa Guida verrà descritto come utilizzare Visual Basic per scrivere i progetti libreria di classi portabile (PCL) che possono essere utilizzati nelle soluzioni destinate a xamarin. IOS e xamarin. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e4c8c43b4df1a7bfc5436f14564c6d0164216c46
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61167193"
---
# <a name="portable-visual-basicnet"></a>Portabile Visual Basic.NET

Progetti Xamarin iOS e Android non supportano in modo nativo Visual Basic. Tuttavia, gli sviluppatori possono utilizzare le librerie di classi portabile per eseguire la migrazione di codice Visual Basic esistente per iOS e Android, o scrivere una parte significativa della relativa logica dell'applicazione in Visual Basic. È possibile creare applicazioni xamarin. Forms interamente in Visual Basic (escluso renderer personalizzati e servizi di dipendenza XAML codebehind).

## <a name="requirements"></a>Requisiti

Supporto libreria di classi portabile è stato aggiunto in xamarin. Android 4.10.1, 7.0.4 di xamarin. IOS e Xamarin Studio 4.2, vale a dire tutti i progetti Xamarin creati con questi strumenti è possono incorporare gli assembly libreria di classi Portabile Visual Basic.

Per creare e compilare librerie di classi portabile di Visual Basic è necessario usare Visual Studio su Windows (Visual Studio 2012 o versioni successiva).

> [!NOTE]
> Librerie di Visual Basic possono essere create solo e compilati usando Visual Studio. Xamarin. IOS e xamarin. Android non supportano il linguaggio Visual Basic.
>
> Se si lavora esclusivamente in Visual Studio è possibile fare riferimento al progetto di Visual Basic da progetti xamarin. IOS e xamarin. Android.
>
> Se i progetti iOS e Android devono essere caricati in Visual Studio per Mac è necessario fare riferimento all'assembly di output dalla libreria di classi Portabile di Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Creazione di una libreria di classi Portabile Basic.NET Visual

Questa sezione illustra come creare una libreria di classi portabile di Visual Basic con Visual Studio.
La libreria può fare riferimento in altri progetti, incluse App xamarin. IOS, xamarin. Android e xamarin. Forms.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile

Quando si aggiunge una libreria di classi Portabile di Visual Basic in Visual Studio è necessario scegliere un profilo che descrive le piattaforme di libreria deve essere compatibile con. I profili sono illustrati nell'introduzione di documento di libreria di classi Portabile.

La procedura per creare una libreria di classi Portabile e scegliere il profilo è:

1.  Nel **nuovo progetto** schermata, seleziona la **Visual Basic > libreria di classi (portabile)** opzione:

    [![](images/image1-sml.png "Creare una nuova libreria portabile di Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio richiederà immediatamente con il codice seguente **Aggiungi libreria di classi portabile** finestra di dialogo in modo che il profilo può essere configurato. Selezionare le piattaforme da supportare e premere **OK**.

    [![](images/image2-sml.png "Selezionare il profilo di libreria di classi Portabile, scegliendo le piattaforme")](images/image2.png#lightbox)

1.  Il progetto libreria di classi Portabile Visual Basic verrà visualizzati come mostrato nel **Esplora soluzioni** simile al seguente:

    [![](images/image3-sml.png "Progetto vuoto di Visual Studio libreria di classi Portabile")](images/image3.png#lightbox)


Libreria di classi Portabile è ora pronto per il codice Visual Basic da aggiungere. I progetti libreria di classi Portabile possono farvi riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti PCL).

### <a name="editing-the-pcl-profile"></a>Modifica il profilo di libreria di classi Portabile

Il profilo di libreria di classi Portabile (che consente di controllare quali piattaforme è compatibile con la libreria di classi Portabile) possono essere visualizzato e modificato facendo clic sul progetto e scegliendo **proprietà > Libreria > Cambia...** . Finestra di dialogo risultante è illustrato in questo screenshot:

 [![](images/image4-sml.png "Modificare il profilo di libreria di classi Portabile nelle proprietà del progetto")](images/image4.png#lightbox)

Se il profilo viene modificato dopo che è già stato aggiunto codice alla libreria di classi Portabile, è possibile che la libreria non sarà più compilate se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionato.


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come utilizzare il codice Visual Basic nelle applicazioni Xamarin usando Visual Studio e librerie di classi portabili. Anche se Xamarin non supporta direttamente Visual Basic, la compilazione di Visual Basic in una libreria di classi Portabile consente il codice scritto con Visual Basic da includere nell'App per iOS e Android.

Le pagine seguenti descrivono come usare Visual Basic.NET librerie di classi portabili in modalità nativa o le app xamarin. Forms:

- [Compilazione di App xamarin. IOS e xamarin. Android native che usano Visual Basic](native-apps.md)
- [Creazione di App xamarin. Forms con Visual Basic](xamarin-forms.md)


## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortableVB (esempio)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Sviluppo multipiattaforma con .NET Framework (Microsoft)](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
