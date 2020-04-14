---
ms.openlocfilehash: cef0b8f56639e7bc8571ab01b820dfd54b074472
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277297"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **WebServiceTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **WebServiceTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. In **Esplora soluzioni** selezionare il progetto **WebServiceTutorial**, fare clic con il pulsante destro del mouse e selezionare **Gestisci pacchetti NuGet...** :

    ![Screenshot della voce di menu Aggiungi pacchetti NuGet selezionata](../images/vs/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. In **Gestione pacchetti NuGet** selezionare la scheda **Sfoglia**, cercare il pacchetto NuGet **Newtonsoft.Json**, selezionarlo e fare clic sul pulsante **Installa**per aggiungerlo al progetto:

    ![Screenshot del pacchetto NuGet Newtonsoft.JSON in Gestione pacchetti NuGet](../images/vs/add-package.png "Pacchetto NuGet Newtonsoft.Json")

    Il pacchetto verrà usato per integrare la deserializzazione JSON nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **WebServiceTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **WebServiceTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Nel **riquadro della soluzione** selezionare il progetto **WebServiceTutorial**, fare clic con il pulsante destro del mouse e selezionare **Aggiungi > Gestisci pacchetti NuGet...** :

    ![Screenshot della voce di menu Aggiungi pacchetti NuGet selezionata](../images/vsmac/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. Nella finestra **Aggiungi pacchetti** cercare il pacchetto NuGet **Newtonsoft.Json**, selezionarlo e fare clic sul pulsante **Aggiungi pacchetto** per aggiungerlo al progetto:

    ![Screenshot del pacchetto NuGet Newtonsoft.JSON in Gestione pacchetti NuGet](../images/vsmac/add-package.png "Pacchetto NuGet Newtonsoft.Json")

    Il pacchetto verrà usato per integrare la deserializzazione JSON nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.
