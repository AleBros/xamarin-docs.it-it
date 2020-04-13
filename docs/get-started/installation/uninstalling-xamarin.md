---
title: Disinstallare Xamarin
description: Questo documento descrive come disinstallare Xamarin da Visual Studio in Windows.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
ms.openlocfilehash: 4c9096edddeb00070aaabc3e93b283f2d55c1bfa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304317"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>Disinstallare Xamarin da Visual Studio

Questa guida descrive come rimuovere Xamarin da Visual Studio in Windows.

<a name="uninstallvs2017" />

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 e Visual Studio 2017

Xamarin può essere disinstallato da Visual Studio 2019 e da Visual Studio 2017 usando l'app del programma di installazione:

1. Usare il **menu Start** per aprire il **programma di installazione di Visual Studio**.

2. Fare clic sul pulsante **Modifica** per l'istanza che si vuole modificare.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Nella scheda **Carichi di lavoro** deselezionare l'opzione **Sviluppo di applicazioni per dispositivi mobili con .NET** (nella sezione **Dispositivi mobili e giochi**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Fare clic sul pulsante **Modifica** in basso a destra nella finestra.

5. Il programma di installazione rimuoverà i componenti deselezionati (Visual Studio 2017 deve essere chiuso prima che il programma di installazione possa apportare modifiche).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

I singoli componenti Xamarin (ad esempio profiler o cartelle di lavoro) possono essere disinstallati passando alla scheda **Singoli componenti** nel passaggio 3 e deselezionando componenti specifici:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Per disinstallare completamente Visual Studio 2017, scegliere **Disinstalla** dal menu con le tre barre accanto al pulsante **Avvia**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Se si dispone di due (o più) istanze di Visual Studio installate side-by-side (SxS), ad esempio una versione di rilascio e una di anteprima, la disinstallazione di un'istanza potrebbe rimuovere alcune funzionalità di Xamarin dalle altre istanze di Visual Studio, tra cui:
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remoted iOS Simulator
> - Apple Bonjour SDK
>
> In determinate condizioni la disinstallazione di una delle istanze SxS può comportare la rimozione non corretta di queste funzionalità. Ciò può peggiorare le prestazioni della piattaforma Xamarin nelle istanze di Visual Studio che rimangono nel sistema dopo la disinstallazione dell'istanza SxS.
>
>Questo problema si risolve eseguendo l'opzione **Ripristina** nel programma di installazione di Visual Studio, che consente di reinstallare i componenti mancanti.

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e versioni precedenti

Per disinstallare completamente Visual Studio 2015, usare [la risposta del supporto in visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Xamarin può essere disinstallato da un computer Windows tramite il **Pannello di controllo**. Passare a **Programmi e funzionalità** o **Programmi > Disinstalla un programma** come illustrato di seguito:

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox)

Disinstallare dal Pannello di controllo i seguenti elementi, se presenti:

- Xamarin
- Xamarin per Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin for Visual Studio

In Explorer eliminare eventuali file rimanenti dalle cartelle dell'estensione Xamarin Visual Studio (tutte le versioni, incluse le cartelle Programmi e Programmi (x86)):

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Eliminare la directory cache del componente MEF di Visual Studio, che dovrebbe trovarsi nel percorso seguente:

```
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Verificare nella directory **VirtualStore** se Windows ha archiviato eventuali file sovrimpressione per la directory **Extensions\Xamarin** o **ComponentModelCache**:

```
%LOCALAPPDATA%\VirtualStore
```

Aprire l'editor del Registro di sistema (regedit) e cercare la chiave seguente:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Individuare ed eliminare eventuali voci che corrispondono a questo modello:

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Cercare questa chiave:

```
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin. Ad esempio, qualsiasi elemento che contiene i termini `mono` o `xamarin`.

Aprire un prompt dei comandi cmd.exe di amministratore ed eseguire i comandi `devenv /setup` e `devenv /updateconfiguration` per ogni versione installata di Visual Studio. Ad esempio, per Visual Studio 2015:

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```
