---
title: Xamarin. Forms usando Visual Basic.NET
description: Modello di progetto xamarin. Forms PCL può essere modificata per l'utilizzo di Visual Basic per l'assembly principale, in modo efficace che consente di creare App per dispositivi mobili multipiattaforma con Visual Basic.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e26d330d62e6ffdfdb3f9b8eab59e57a6377a86c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms usando Visual Basic.NET

Xamarin non supporta direttamente Visual Basic, seguire le istruzioni in questa pagina per creare una soluzione xamarin. Forms PCL di c# e quindi sostituire il progetto PCL codice comune con Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Creare una soluzione xamarin. Forms PCL e quindi sostituire il progetto PCL codice comune con Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Programmare con Visual Basic, è necessario utilizzare Visual Studio in Windows.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms con questa procedura dettagliata di Visual Basic

Seguire questi passaggi per creare un progetto xamarin. Forms semplice che utilizza Visual Basic:

1. Creare un nuovo *xamarin. Forms c#* soluzione che Usa librerie di classe portabile (PCL).
Passare a **File > Nuovo progetto** e nel **nuovo progetto** finestra passare a **installato > Modelli > Visual c# > multipiattaforma** scegliere  **Cross Platform App (xamarin. Forms o nativo) > xamarin. Forms**.

2. Pulsante destro del mouse sulla soluzione e **Aggiungi > Nuovo progetto**.

3. Scegliere il **Visual Basic > libreria di classi (portabile)** tipo di progetto:

   [![](xamarin-forms-images/add-vb-2-sml.png "Aggiungi nuovo progetto di libreria di classi portabile")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Selezionare le piattaforme, come illustrato per configurare il profilo della libreria di classi Portabile corretto (assicurarsi di includere xamarin. IOS e xamarin):

   ![](xamarin-forms-images/add-vb-3-sml.png "Selezionare le piattaforme per il supporto")

5. Fare clic sul progetto di Visual Basic e scegliere **proprietà**, quindi modificare il **spazio dei nomi predefinito** in modo che corrisponda esistente c# progetti:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Assicurarsi che lo spazio dei nomi radice di Visual Basic corrisponda all'app xamarin. Forms")

6. Fare clic sul nuovo progetto di Visual Basic e scegliere **Gestisci pacchetti Nuget**, quindi installare **xamarin. Forms** e chiudere la finestra di gestione del pacchetto.

   [![](xamarin-forms-images/add-vb-4-sml.png "Form e chiudere la finestra di gestione pacchetti")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Rinominare il valore predefinito **Class1** file *e* classe `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Rinominare il file Class1 predefinito e la classe App")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Incollare il codice seguente nel **App.vb** file, che diventerà il punto iniziale dell'app xamarin. Forms. Ricordarsi di includere `Imports Xamarin.Forms` e aggiungere `Inherits Application` alla classe:

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. A questo punto è necessario in modo da indicare il nuovo progetto di Visual Basic i progetti iOS e Android.
Fare clic su di **riferimenti** nodo i progetti iOS e Android per aprire la **gestione riferimenti**. Segni di graduazione annullare la libreria portabile in c# e segni di graduazione libreria portabile VB (non dimenticare, eseguire questa operazione per entrambi i progetti Android e iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Rimuovere il riferimento di progetto precedente, aggiungere i riferimenti di Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Eliminare il progetto c# portabile. Aggiungere un nuovo **vb** out di file da compilare l'applicazione di xamarin. Forms. Un modello per il nuovo `ContentPage`s in Visual Basic è illustrato di seguito:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitazioni di Visual Basic in xamarin. Forms

Come menzionato nel [pagina portabile Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md), Xamarin non supporta il linguaggio Visual Basic. Ciò significa che vi sono alcuni limiti in cui è possibile utilizzare Visual Basic:

 - Un renderer personalizzato non può essere scritto in Visual Basic, in cui devono essere scritti in c# i progetti con piattaforma nativa.

 - Le implementazioni del servizio di dipendenza non possono essere scritto in Visual Basic, in cui devono essere scritti in c# i progetti con piattaforma nativa.

 - Pagine XAML non possono essere incluso nel progetto di Visual Basic - genera solo il generatore di codice c#. È possibile includere XAML in una separati, con cui viene fatto riferimento, in c# libreria di classi portabile e utilizzare l'associazione dati per popolare i file XAML mediante i modelli di Visual Basic (un esempio è incluso nel [esempio](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin non supporta il linguaggio Visual Basic.NET.

## <a name="related-links"></a>Collegamenti correlati

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Lo sviluppo multipiattaforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
