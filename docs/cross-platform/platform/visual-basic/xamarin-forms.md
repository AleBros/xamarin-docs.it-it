---
title: Xamarin. Forms con Visual Basic.NET
description: Modello di progetto libreria di classi Portabile xamarin. Forms può essere modificato per usare Visual Basic per l'assembly principale, in modo efficace consente di compilare App per dispositivi mobili multipiattaforma con Visual Basic.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f397cf595a9ae151c5f105341733b2c57023fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282312"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms con Visual Basic.NET

Xamarin non supportano direttamente Visual Basic, seguire le istruzioni in questa pagina per creare una soluzione c# xamarin. Forms libreria di classi Portabile e quindi sostituire il progetto di libreria di classi Portabile codice comune con Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Creare una soluzione di libreria di classi Portabile xamarin. Forms e quindi sostituire il progetto di libreria di classi Portabile codice comune con Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> È necessario usare Visual Studio su Windows al programma Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms con Visual Basic questa procedura dettagliata

Seguire questi passaggi per creare un progetto xamarin. Forms semplice che Usa Visual Basic:

1. Creare una nuova *xamarin. Forms c#* soluzione che usa le librerie di classi portabile (PCL).
Passare a **File > Nuovo progetto** e nel **nuovo progetto** finestra passare a **installati > Modelli > Visual C# > multipiattaforma** quindi scegliere **Tra App multipiattaforma (xamarin. Forms o Native) > xamarin. Forms**.

2. Pulsante destro del mouse sulla soluzione e **Aggiungi > Nuovo progetto**.

3. Scegliere il **Visual Basic > libreria di classi (portabile)** tipo di progetto:

   [![](xamarin-forms-images/add-vb-2-sml.png "Aggiungi nuovo progetto di libreria di classi portabile")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Selezionare le piattaforme, come illustrato per configurare il profilo di libreria di classi Portabile corretto (assicurarsi di includere xamarin. IOS e xamarin. Android):

   ![](xamarin-forms-images/add-vb-3-sml.png "Scegliere le piattaforme da supportare")

5. Pulsante destro del mouse sul progetto di Visual Basic e scegliere **delle proprietà**, quindi modificare il **spazio dei nomi predefinito** in modo che corrisponda il codice esistente c# progetti:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Verificare che lo spazio dei nomi radice di Visual Basic corrispondente all'app xamarin. Forms")

6. Fare clic sul nuovo progetto Visual Basic e scegliere **Gestisci pacchetti Nuget**, quindi installare **xamarin. Forms** e chiudere la finestra di gestione pacchetti.

   [![](xamarin-forms-images/add-vb-4-sml.png "Moduli e chiudere la finestra di gestione pacchetti")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Rinominare il valore predefinito **Class1** file *e* classe `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Rinominare il file Class1 predefinito e una classe all'App")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Incollare il codice seguente nel **App.vb** file, che diventerà il punto di partenza della tua app xamarin. Forms. Ricordare di includere `Imports Xamarin.Forms` e aggiungere `Inherits Application` alla classe:

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

9. A questo punto è necessario in modo da puntare il nuovo progetto di Visual Basic i progetti iOS e Android.
Fare clic sui **riferimenti** nodo i progetti iOS e Android per aprire il **gestione riferimenti**. Annullamento apice il C# libreria portabile e segni di graduazione nella libreria portabile di Visual Basic (non dimenticare, eseguire questa operazione per entrambi i progetti Android e iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Rimuovere il riferimento di progetto precedente, aggiungere riferimenti di Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Eliminare il progetto portabile di c#. Aggiungi nuova **vb** out di file da compilare l'applicazione xamarin. Forms. Un modello per il nuovo `ContentPage`s in Visual Basic è illustrato di seguito:

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

Come indicato nel [portabile Visual Basic.NET pagina](~/cross-platform/platform/visual-basic/index.md), Xamarin non supporta il linguaggio Visual Basic. Ciò significa che esistono alcune limitazioni in cui è possibile usare Visual Basic:

 - Renderer personalizzati non possono essere scritti in Visual Basic, in cui devono essere scritti in c# i progetti di piattaforma nativa.

 - Le implementazioni del servizio di dipendenza non possono essere scritta in Visual Basic, in cui devono essere scritti in c# i progetti di piattaforma nativa.

 - Le pagine XAML non possono essere incluso nel progetto di Visual Basic: il generatore di codice può essere create solo c#. È possibile includere XAML in una separata e utilizzati come riferimento c# libreria di classi portabile e usare il data binding per popolare i file XAML tramite modelli di Visual Basic (un esempio è incluso nel [esempio](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin non supporta il linguaggio Visual Basic.NET.

## <a name="related-links"></a>Collegamenti correlati

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Sviluppo multipiattaforma con .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
