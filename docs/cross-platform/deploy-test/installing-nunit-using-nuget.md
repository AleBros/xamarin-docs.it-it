---
title: Installazione di NUnit 2.6.4 tramite NuGet
description: Questo documento descrive come effettuare il downgrade di NUnit 3.0 a NUnit 2.6.4 tramite NuGet. Questa operazione è necessaria quando si lavora con Xamarin.UITest, che non supporta NUnit 3.x.
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: c43b3f3204cba890cb2fa9c4cfc33a23250e0550
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112052"
---
# <a name="installing-nunit-264-using-nuget"></a>Installazione di NUnit 2.6.4 tramite NuGet

_Questa guida illustra come effettuare il downgrade di NUnit 3.0 a NUnit 2.6.4 tramite NuGet._

Gli sviluppatori che scrivono test in Visual Studio per Mac o tramite Xamarin.UITest devono usare [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4) perché NUnit 3.0 o le versioni successive non sono compatibili con Visual Studio per Mac o Xamarin.UITest. I tentativi di eseguire unit test in Visual Studio per Mac o Xamarin.UITest con NUnit 3.0 avranno esito negativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questa guida illustra l'installazione di NUnit 2.6.4 tramite NuGet per Visual Studio per Mac. Questi passaggi consentono anche di disinstallare NUnit 3.0, se necessario.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questa guida illustra come effettuare il downgrade di NUnit 3.0 a NUnit 2.6.4 tramite NuGet in Visual Studio 2015.

-----

## <a name="requirements"></a>Requisiti

In questa guida si presuppone che sia disponibile una soluzione esistente con un progetto di app per dispositivi mobili e un progetto di test.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>Installazione di NUnit 2.6.4 in Visual Studio per Mac

Nei passaggi seguenti viene descritto come installare NUnit 2.6.4.


1. **Aprire Gestione pacchetti**, fare clic con il pulsante destro del mouse su **Pacchetti** e selezionare **Aggiungi pacchetti** dal menu popup:

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "Fare clic con il pulsante destro del mouse su Pacchetti e selezionare Aggiungi pacchetti dal menu popup")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **Cercare `NUnit version:2.6.4`**, Visual Studio per Mac disinstallerà NUnit 3.0, se necessario, quindi scaricare e installare NUnit 2.6.4. Nella finestra di dialogo **Aggiungi pacchetti** immettere il testo `nunit version:2.6.4` nel campo di **ricerca** nell'angolo superiore destro. Selezionare **NUnit** dai risultati della ricerca e fare clic sul pulsante **Aggiungi pacchetto**:

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "Selezionare NUnit dai risultati della ricerca e fare clic sul pulsante Aggiungi pacchetto")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)


È possibile verificare che NUnit 2.6.4 sia stato installato controllando il numero di versione del pacchetto NUnit nel riquadro della soluzione:

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "Controllare il numero di versione del pacchetto NUnit nel riquadro della soluzione")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>Riepilogo

In questa guida è stato illustrato come effettuare il downgrade di NUnit 3.0 a NUnit 2.6.4 in Visual Studio per Mac tramite la console di Gestione pacchetti.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="installing-nunit-264-in-visual-studio"></a>Installazione di NUnit 2.6.4 in Visual Studio

In questa sezione l'accento verrà posto sull'uso della _console di Gestione pacchetti NuGet_ in Visual Studio 2015 per disinstallare NUnit 3.0 e installare NUnit 2.6.4.


1. **Avviare la console di Gestione pacchetti NuGet**, selezionare **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**:

    [![](installing-nunit-using-nuget-images/package-manager-console.png "Avviare la console di Gestione pacchetti NuGet, selezionare Strumenti,  Gestione pacchetti NuGet, Console di Gestione pacchetti")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **Verificare la versione di NUnit**. In alternativa, è possibile verificare la versione installata di NUnit anche eseguendo il comando `Get-Package -Project <UITEST PROJECT>`:

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

Se viene visualizzato NUnit 3.0 o versione successiva sarà necessario effettuare il downgrade a NUnit 2.6.4.

1. **Disinstallare NUnit 3.0**. Usare il cmdlet `Uninstall-Package` per disinstallare NUnit 3.0:

        <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Resolving actions to uninstall package 'NUnit.3.0.1'
        Resolved actions to uninstall package 'NUnit.3.0.1'
        Removed package 'NUnit.3.0.1' from 'packages.config'
        Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>

1. **Installare NUnit 2.6.4**. Installare NUnit 2.6.4 con il cmdlet `Install-Package` come illustrato nel frammento di codice seguente:

        <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
        Resolving actions to install package 'NUnit.2.6.4'
        Resolved actions to install package 'NUnit.2.6.4'
        Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to 'packages.config'
        Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    
## <a name="summary"></a>Riepilogo

In questa guida è stato illustrato come effettuare il downgrade di NUnit 3.0 a NUnit 2.6.4 in Visual Studio 2015 tramite la console di Gestione pacchetti.

-----

## <a name="related-links"></a>Collegamenti correlati

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)
- [Pacchetto NuGet di NUnit 2.6.4](https://www.nuget.org/packages/NUnit/2.6.4)
