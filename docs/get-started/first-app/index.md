---
title: Creare la prima app Xamarin.Forms
description: Guida video che illustra come creare la prima applicazione Xamarin.Forms in Visual Studio.
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 05/23/2019
ms.openlocfilehash: 245b41eea556ef36c81b337b57ce58d922e4e8fd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653677"
---
# <a name="build-your-first-xamarinforms-app"></a>Creare la prima app Xamarin.Forms

_Guardare questo video e seguire la procedura per creare la prima app per dispositivi mobili con Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Istruzioni dettagliate per Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > nuovo > progetto..** . oppure fare clic sul pulsante **Crea nuovo progetto...** :

    [![Creazione di un nuovo progetto](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Cercare "Novell" o scegliere **mobile** dal menu **tipo progetto** . Selezionare il tipo di progetto **app per dispositivi mobili (Novell. Forms)** :

    [![Filtrare per progetti Novell](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Scegliere un nome &ndash; di progetto nell'esempio viene usato "AwesomeApp":

    [![Scegliere un nome di progetto](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Fare clic sul tipo di progetto **vuoto** e verificare che siano selezionati **Android** e **iOS** :

    [![Android e iOS, con .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Attendere finché non vengono ripristinati i pacchetti NuGet (nella barra di stato verrà visualizzato il messaggio "Ripristino completato").

6. Avviare l'emulatore Android premendo il pulsante di debug (o la voce di menu **Debug > Avvia debug**).

7. Modificare **MainPage.xaml** aggiungendo questo XAML prima della fine del `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

8. Modificare **MainPage.xaml.cs** aggiungendo questo codice alla fine della classe:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

9. Debug dell'app su Android:

    ![App Android](images/win/07-sml.png)

    > [!TIP]
    > È possibile compilare ed eseguire il debug dell'app iOS da Visual Studio con un computer Mac in rete. Vedere le [istruzioni di installazione](~/ios/get-started/installation/windows/index.md) per altre informazioni.

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Compilare un'app iOS in Visual Studio 2019

Questo video descrive il processo di compilazione e test di un'app iOS con Visual Studio 2019 in Windows:

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Istruzioni dettagliate per Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > Nuovo > Progetto** oppure premere il pulsante **Crea nuovo progetto...**  e quindi selezionare **Visual C# > Multipiattaforma > App per dispositivi mobili (Xamarin.Forms)** :

    [![App per dispositivi mobili (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Assicurarsi che le opzioni **Android** e **iOS** siano selezionate, con la condivisione codice **.NET Standard**:

    [![Android e iOS, con .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Attendere finché non vengono ripristinati i pacchetti NuGet (nella barra di stato verrà visualizzato il messaggio "Ripristino completato").

4. Avviare l'emulatore Android premendo il pulsante di debug (o la voce di menu **Debug > Avvia debug**).

5. Modificare **MainPage.xaml** aggiungendo questo XAML prima della fine del `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Modificare **MainPage.xaml.cs** aggiungendo questo codice alla fine della classe:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Debug dell'app su Android:

    ![App Android](images/win/07-sml.png)

    > [!TIP]
    > È possibile compilare ed eseguire il debug dell'app iOS da Visual Studio con un computer Mac in rete. Vedere le [istruzioni di installazione](~/ios/get-started/installation/windows/index.md) per altre informazioni.

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Istruzioni dettagliate per Mac

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > Nuova soluzione...** oppure premere il pulsante **Nuovo progetto...**  e quindi selezionare **Multipiattaforma > App > App Forms vuota**:

    [![App Forms vuota](images/01-sml.png)](images/01.png#lightbox)

2. Assicurarsi che le opzioni **Android** e **iOS** siano selezionate, con la condivisione codice **.NET Standard**:

    [![Android e iOS, con .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Ripristinare i pacchetti NuGet, facendo clic sulla soluzione:

    ![App Android](images/03-sml.png)

4. Avviare l'emulatore Android premendo il pulsante di debug (o **Debug > Avvia debug**).

5. Modificare **MainPage.xaml** aggiungendo questo XAML prima della fine del `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Modificare **MainPage.xaml.cs** aggiungendo questo codice alla fine della classe:

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Debug dell'app su Android:

    ![App Android](images/07-sml.png)

8. Fare clic con il pulsante destro del mouse per impostare iOS su **Progetto di avvio**:

    [![Impostare il progetto di avvio su iOS](images/08-sml.png)](images/08.png#lightbox)

9. Debug dell'app su iOS:

    ![App iOS](images/09-sml.png)

::: zone-end

È possibile scaricare il codice completo dalla [Raccolta esempi](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/) o visualizzarlo su [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Fasi successive

- [Guida introduttiva a pagina singola](~/get-started/quickstarts/single-page.md) &ndash; Creare un'app più funzionale.
- [Esempi di Xamarin.Forms](~/xamarin-forms/samples/index.yml) &ndash; Scaricare ed eseguire gli esempi di codice e app di esempio.
- [Ebook Creazione di app per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; Capitoli approfonditi che illustrano lo sviluppo di Xamarin.Forms. Disponibile in formato PDF, include centinaia di altri esempi.
