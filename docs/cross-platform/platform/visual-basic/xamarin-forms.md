---
title: Novell. Forms con Visual Basic.NET
description: Il modello di progetto Novell. Forms può essere modificato in modo da usare Visual Basic per l'assembly principale, consentendo di creare app per dispositivi mobili multipiattaforma usando VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: e1a540eef2a4d54ead68ae4a9427b0622b668182
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014554"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Novell. Forms con Visual Basic.NET

Novell non supporta direttamente Visual Basic. seguire le istruzioni disponibili in questa pagina per creare una C# soluzione Novell. Forms e quindi sostituire il C# progetto .NET standard con Visual Basic.

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![creare una soluzione Novell. Forms e quindi sostituire il progetto .NET Standard con Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> È necessario usare Visual Studio in Windows per programmare con Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Novell. Forms con Visual Basic procedura dettagliata

Seguire questa procedura per creare un progetto Novell. Forms semplice che usi Visual Basic:

1. Da Visual Studio 2019 scegliere **Crea un nuovo progetto**.

2. Nella finestra **Crea un nuovo progetto** digitare **Novell. Forms** per filtrare l'elenco e selezionare **app per dispositivi mobili (Novell. Forms)** , quindi fare clic su **Avanti**.

    [Filtro![per le app Novell. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. Nella schermata successiva, digitare un nome per il progetto e premere **Crea**.

4. Scegliere il modello **vuoto** e fare clic su **OK**:

    [![modello Novell. Forms vuoto](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Viene creata una soluzione Novell. Forms in Visual Studio usando C#. Nei passaggi successivi viene modificata la soluzione per utilizzare Visual Basic.

5. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **aggiungi > nuovo progetto...**

6. Digitare **Visual Basic libreria** per filtrare le opzioni del progetto e scegliere l'opzione **libreria di classi (.NET standard)** con l'icona Visual Basic:

    [Filtro![per la libreria Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. Nella schermata successiva, digitare un nome per il progetto e premere **Crea**.

8. Fare clic con il pulsante destro del mouse sul progetto Visual Basic e scegliere **Proprietà**, quindi modificare lo **spazio dei nomi predefinito** in modo che corrisponda ai progetti esistenti C# :

    [![assicurarsi che lo spazio dei nomi della Visual Basic radice corrisponda all'app Novell. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Fare clic con il pulsante destro del mouse sul nuovo progetto Visual Basic e scegliere **Gestisci pacchetti NuGet**, quindi installare **Novell. Forms** e chiudere la finestra di gestione pacchetti.

    [![form e chiudere la finestra di gestione pacchetti](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Rinominare il file **Class1. vb** predefinito in **app. vb**:

    [![rinominare il file Class1 e la classe predefiniti in app](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. Incollare il codice seguente nel file **app. vb** , che diventerà il punto di partenza dell'app Novell. Forms:

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
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

12. Aggiornare i progetti Android e iOS in modo che facciano riferimento al nuovo progetto Visual Basic e C# non al progetto creato dal modello.
Fare clic con il pulsante destro del mouse sul nodo **riferimenti** nei progetti Android e iOS per aprire **Gestione riferimenti**. Deselezionare la C# libreria e fare il segno di Visual Basic libreria (non dimenticare, eseguire questa operazione per entrambi i progetti Android e iOS).

    [![rimuovere il riferimento al progetto precedente, aggiungere Visual Basic riferimento](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Eliminare il C# progetto. Aggiungere nuovi file **. vb** per compilare l'applicazione Novell. Forms. Di seguito è riportato un modello per i nuovi `ContentPage`in Visual Basic:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
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

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitazioni dei Visual Basic in Novell. Forms

Come indicato nella [pagina Portable Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md), Novell non supporta la lingua Visual Basic. Ciò significa che esistono alcune limitazioni in cui è possibile usare Visual Basic:

- Non è possibile includere le pagine XAML nel progetto Visual Basic: il generatore code-behind può compilare C#solo. È possibile includere XAML in una libreria di classi portabile separata e C# a cui viene fatto riferimento e utilizzare l'associazione dati per popolare i file xaml tramite Visual Basic modelli. un esempio è incluso nell' [esempio](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages).

- I renderer personalizzati non possono essere scritti in Visual Basic, ma devono essere scritti C# in nei progetti della piattaforma nativa.

- Le implementazioni del servizio di dipendenza non possono essere scritte in Visual Basic, ma C# devono essere scritte in nei progetti della piattaforma nativa.

## <a name="related-links"></a>Collegamenti correlati

- [XamarinFormsVB (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Sviluppo multipiattaforma con il .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
