---
title: Installazione di Xamarin in Visual Studio in Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 09/29/2017
ms.openlocfilehash: b68e03251b83192bdc5836af6ea54446ddaad24a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarin-in-visual-studio-on-windows"></a>Installazione di Xamarin in Visual Studio in Windows

Poiché Xamarin ora è incluso gratuitamente in tutte le edizioni di Visual Studio e non richiede una licenza separata, è possibile usare il programma di installazione di Visual Studio per scaricare e installare gli strumenti di Xamarin.

-   [Requirements](#requirements)
-   [Installazione](#installation)
-   [Aggiunta di Xamarin a Visual Studio 2017](#vs2017)
-   [Aggiunta di Xamarin a Visual Studio 2015](#vs2015)
-   [Verifica dell'installazione](#verifying)
-   [Passaggi successivi](#nextsteps)


<a name="requirements" />

# <a name="requirements"></a>Requisiti

Per installare Visual Studio Tools per Xamarin, è necessario quanto segue:

1. Windows 7 o versioni successive.

2. Visual Studio 2015 o 2017 (Community, Professional o Enterprise).

3. Xamarin per Visual Studio.

Tenere presente che Xamarin non può essere usato con le edizioni Express di Visual Studio a causa della mancanza del supporto per il plug-in.

Per altre informazioni sui prerequisiti per l'installazione e l'uso di Xamarin, vedere [Requisiti di sistema](~/cross-platform/get-started/requirements.md).


<a name="installation" />

# <a name="installation"></a>Installazione

Xamarin può essere installato durante una nuova installazione di Visual Studio.
A tale scopo, seguire questa procedura:

1. Scaricare Visual Studio Community, Visual Studio Professional o Visual Studio Enterprise dalla pagina di [Visual Studio](https://www.visualstudio.com/vs/). I collegamenti di download sono nella parte inferiore.

2. Fare doppio clic sul pacchetto scaricato per avviare l'installazione.

3. Selezionare il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET** dalla schermata di installazione: 

    [![Selezione di Sviluppo di applicazioni per dispositivi mobili con .NET nella schermata Carichi di lavoro](windows-images/01-mobile-dev-workload-sml.png)](windows-images/01-mobile-dev-workload.png)

4. Mentre **Sviluppo di applicazioni per dispositivi mobili con .NET** è selezionato, esaminare il pannello **Riepilogo** a destra. Qui è possibile deselezionare le opzioni di sviluppo di applicazioni per dispositivi mobili che non si vuole installare. Per impostazione predefinita, vengono installate tutte le opzioni visualizzate nello screenshot seguente (**Xamarin Workbooks**, **Xamarin Profiler**, **Xamarin Remoted Simulator**, **Android NDK**, **Android SDK**, **Java SE Development Kit**, **Emulatore Android di Google**, **Supporto per F#** e **Intel HAXM**):

    ![Pannello Riepilogo con l'elenco delle opzioni Xamarin da installare](windows-images/02-summary.png)

5. Quando si è pronti a iniziare l'installazione di Visual Studio, fare clic sul pulsante **Installa** nell'angolo in basso a destra:

    ![Posizione del pulsante di installazione](windows-images/03-click-install.png)

   A seconda dell'edizione di Visual Studio da installare, il processo di installazione può richiedere tempi prolungati. È possibile usare gli indicatori di stato per monitorare l'installazione:

    ![Screenshot di esempio degli indicatori di stato durante l'installazione](windows-images/04-progress-bars.png)

6. Al termine dell'installazione di Visual Studio, fare clic sul pulsante **Avvia** per avviare Visual Studio:

    ![Posizione del pulsante Avvia](windows-images/05-launch.png)


<a name="vs2017" />

## <a name="adding-xamarin-to-visual-studio-2017"></a>Aggiunta di Xamarin a Visual Studio 2017

Se Visual Studio 2017 è già installato, è possibile aggiungere Xamarin eseguendo di nuovo il programma di installazione di Visual Studio per modificare i carichi di lavoro. Per informazioni dettagliate, vedere [Modificare Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio). Seguire quindi i passaggi elencati sopra per installare Xamarin.

Per altre informazioni sul download e sull'installazione di Visual Studio 2017, vedere [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).


<a name="vs2015" />

## <a name="adding-xamarin-to-visual-studio-2015"></a>Aggiunta di Xamarin a Visual Studio 2015

Per aggiungere Xamarin.Android a un'installazione esistente di Visual Studio 2015, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul pulsante **Start** di Windows e scegliere **Programmi e funzionalità**.

2. Fare clic con il pulsante destro del mouse su **Microsoft Visual Studio** e scegliere **Cambia**.

3. Quando viene visualizzata la finestra di dialogo Programma di installazione di Visual Studio, fare clic sul pulsante **Modifica**.

4. Nella scheda **Funzionalità** scorrere verso il basso fino a **Sviluppo di app per dispositivi mobili multipiattaforma**. Fare clic sulla casella di controllo accanto a **C#/.NET (Xamarin)**:

    ![Aggiunta di C#/.NET Xamarin a Visual Studio 2015](windows-images/06-add-xamarin.png)

5. Fare clic sul pulsante **AGGIORNA** per aggiungere Xamarin a Visual Studio.


<a name="verifying" />

## <a name="verifying-installation"></a>Verifica dell'installazione

In Visual Studio 2017, per verificare che Xamarin sia installato, fare clic sul menu **?**. Se Xamarin è installato, verrà visualizzata una voce di menu **Xamarin**, come in questo screenshot:

![Voce di menu Xamarin visualizzata nel menu ?](windows-images/12-xamarin-menu-item.png)

Se si usa una versione precedente di Visual Studio, è possibile fare clic su **? > Informazioni su Microsoft Visual Studio** e scorrere l'elenco di prodotti installati per verificare se Xamarin è installato:

![Schermata dei prodotti installati in Visual Studio](windows-images/13-xamarin-is-installed.png)

Per altre informazioni sull'individuazione delle informazioni sulla versione, vedere [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (Dove è possibile trovare i log e le informazioni sulla versione).

<a name="nextsteps" />

# <a name="next-steps"></a>Passaggi successivi

L'installazione di Visual Studio Tools per Xamarin consente di iniziare a scrivere il codice per le app, ma sono necessarie operazioni di configurazione aggiuntive per compilare e distribuire le app nel simulatore, nell'emulatore e nel dispositivo. Vedere le guide seguenti per completare l'installazione e avviare la compilazione di app multipiattaforma.

## <a name="ios"></a>iOS

Per informazioni più dettagliate, vedere la guida [Installazione di Xamarin.iOS in Windows](~/ios/get-started/installation/windows/index.md). 

1. [Install Xamarin.iOS tools on your Mac (Installare gli strumenti per Xamarin.iOS nel Mac)](~/ios/get-started/installation/windows/index.md#installation)
2. [Configuring your Mac (Configurazione del Mac)](~/ios/get-started/installation/windows/index.md#configuration)
3. [iOS Developer Setup (Configurazione per sviluppatori iOS)](~/ios/get-started/installation/windows/index.md#developersetup) per eseguire l'applicazione nel dispositivo.
4. [Connecting Visual Studio to your Mac build host (Connessione di Visual Studio all'host di compilazione Mac)](~/ios/get-started/installation/windows/index.md#connectingtomac)
5. [Simulatore iOS remoto](~/tools/ios-simulator.md)
6. [Introduzione a Xamarin.iOS per Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

## <a name="android"></a>Android

Per informazioni più dettagliate, vedere la guida [Installazione di Xamarin.Android in Windows](~/android/get-started/installation/windows.md).

1. [Configurazione di Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Using the Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md?ide=vs) (Uso di Xamarin Android SDK Manager)
3. [Android SDK Emulator](~/android/get-started/installation/android-emulator/index.md) (Emulatore di Android SDK)
4. [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo)
