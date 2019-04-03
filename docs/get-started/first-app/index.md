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
ms.date: 04/02/2019
ms.openlocfilehash: 0031cb7fb46cf5ad35872963fd3c3def0a2ae9a6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855302"
---
# <a name="build-your-first-xamarinforms-app"></a>Creare la prima app Xamarin.Forms

_Guardare questo video e seguire la procedura per creare la prima app per dispositivi mobili con xamarin. Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Istruzioni dettagliate per Windows

[![Download esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > Nuovo > progetto...**  oppure premere la **Crea nuovo progetto...**  pulsante:

    [![CCrea un nuovo progetto](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Cercare "Xamarin" oppure scegliere **Mobile** dalle **tipo di progetto** menu. Selezionare il **App per dispositivi mobili (xamarin. Forms)** tipo di progetto:

    [![FFiltra per i progetti Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Scegliere un nome di progetto &ndash; nell'esempio viene usato "AwesomeApp":

    [![CScegliere un nome di progetto](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Fare clic sui **Black** tipo di progetto e assicurarsi che **Android** e **iOS** sono selezionate:

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

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Istruzioni dettagliate per Windows

[![Download esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > Nuovo > Progetto** oppure premere il pulsante **Crea nuovo progetto...**  e quindi selezionare **Visual C# > Multipiattaforma > App per dispositivi mobili (Xamarin.Forms)**:

    [![Mportatili App (xamarin. Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

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

[![Download esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Seguire questi passaggi e il video precedente:

1. Scegliere **File > Nuova soluzione...** oppure premere il pulsante **Nuovo progetto...**  e quindi selezionare **Multipiattaforma > App > App Forms vuota**:

    [![BApp Forms vuoto](images/01-sml.png)](images/01.png#lightbox)

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

    [![Sil progetto di avvio per iOS et](images/08-sml.png)](images/08.png#lightbox)

9. Debug dell'app su iOS:

    ![App iOS](images/09-sml.png)

::: zone-end

È possibile scaricare il codice completo dalla [Raccolta esempi](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/) o visualizzarlo su [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Passaggi successivi

- [L'accesso Single Page Quickstart](~/get-started/quickstarts/single-page.md) &ndash; compilare un'app più funzionale.
- [Esempi di Xamarin.Forms](~/xamarin-forms/samples/index.yml) &ndash; Scaricare ed eseguire gli esempi di codice e app di esempio.
- [Ebook Creazione di app per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; Capitoli approfonditi che illustrano lo sviluppo di Xamarin.Forms. Disponibile in formato PDF, include centinaia di altri esempi.
