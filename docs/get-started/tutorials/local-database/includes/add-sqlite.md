---
ms.openlocfilehash: 8ad5ca60a074cbdc6ff91134cc9c1276ed653b91
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "67277292"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **LocalDatabaseTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **LocalDatabaseTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. In **Esplora soluzioni** selezionare il progetto **LocalDatabaseTutorial**, fare clic con il pulsante destro del mouse e selezionare **Gestisci pacchetti NuGet...**:

    ![Screenshot della voce di menu Gestisci pacchetti NuGet selezionata](../images/vs/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. In **Gestione pacchetti NuGet** selezionare la scheda **Sfoglia**, cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Installa** per aggiungerlo al progetto:

    ![Screenshot del pacchetto NuGet SQLite.NET in Gestione pacchetti NuGet](../images/vs/add-package.png "Pacchetto NuGet SQLite.NET")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore/i:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **LocalDatabaseTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **LocalDatabaseTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. Nel **riquadro della soluzione** selezionare il progetto **LocalDatabaseTutorial**, fare clic con il pulsante destro del mouse e selezionare **Aggungi > Gestisci pacchetti NuGet...**:

    ![Screenshot della voce di menu Aggiungi pacchetti NuGet selezionata](../images/vsmac/add-nuget-packages.png "Voce di menu Aggiungi pacchetti NuGet")

1. Nella finestra **Aggiungi pacchetti** cercare il pacchetto NuGet **sqlite-net-pcl**, selezionarlo e fare clic sul pulsante **Aggiungi pacchetto** per aggiungerlo al progetto:

    ![Screenshot del pacchetto NuGet SQLite.NET in Gestione pacchetti NuGet](../images/vsmac/add-package.png "Pacchetto NuGet SQLite.NET")

    > [!NOTE]
    > Esiste una serie di pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:
    > - **Autore:** Frank A. Krueger
    > - **ID:** sqlite-net-pcl
    > - **Collegamento a NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Nonostante il nome del pacchetto, questo pacchetto NuGet può essere usato anche nei progetti .NET Standard.

    Il pacchetto verrà usato per integrare le operazioni di database nell'applicazione.

1. Compilare la soluzione per assicurarsi che non siano presenti errori.
