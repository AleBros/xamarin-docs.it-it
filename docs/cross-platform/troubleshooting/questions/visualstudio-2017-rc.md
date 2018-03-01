---
title: "È possibile utilizzare Visual Studio 2017 versione finale candidata con Xamarin?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 745e532a9543029f13e5fdffcc15153d780278ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>È possibile utilizzare Visual Studio 2017 versione finale candidata con Xamarin?

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>È possibile utilizzare Visual Studio 2017 versione finale candidata con Xamarin?

Sì. Si è in grado di usare Xamarin tramite Visual Studio 2017 Release Candidate. Tuttavia, tenere presente che attualmente, l'installazione di Xamarin per Visual Studio 2017 RC verrà disinstallare eventuali versioni precedenti di Xamarin in Visual Studio 2015/2013 installato. Xamarin per Visual Studio 2017 non possono essere installati nello stesso momento come Xamarin per Visual Studio 2015/2013 in seguito a Visual Studio 2017, passaggio da pacchetti MSI per l'utilizzo del sistema programma di installazione di Visual Studio.

Mentre il team esegue attualmente la ricerca in modi per evitare questo comportamento previsto, è consigliabile agli utenti di scegliere l'ambiente di sviluppo in base alle proprie esigenze. 

> [!IMPORTANT]
> Se si compilano progetti di xamarin. IOS, assicurarsi che Visual Studio per Mac nel sistema Mac associato è la stessa versione di xamarin. IOS canale.

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>Come si installa Xamarin per Visual Studio 2017 Release Candidate?

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>L'installazione durante l'installazione di RC di Visual Studio 2017

* Selezionare il **Xamarin** componente come parte del nuovo **programma di installazione di Visual Studio**

  [ ![](visualstudio-2017-rc-images/install1-sml.png "Schermata di installazione RC di Visual Studio 2017")](visualstudio-2017-rc-images/install1-orig.png)

Verrà installato estensione di Visual Studio per lo sviluppo di xamarin. IOS e xamarin.

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>L'installazione o reinstallare Xamarin in un'installazione esistente di Visual Studio 2017 RC

#### <a name="using-the-visual-studio-installer"></a>Tramite il programma di installazione di Visual Studio:

1. Cercare l'applicazione del programma di installazione di Visual Studio

  [ ![](visualstudio-2017-rc-images/reinstall1-sml.png "Risultati della ricerca per l'applicazione di installazione di Visual Studio")](visualstudio-2017-rc-images/reinstall1-orig.png)

2. Selezionare: una. **Sviluppo per dispositivi mobile con .NET (anteprima)** nella scheda carichi di lavoro, o

  [ ![](visualstudio-2017-rc-images/reinstall2-sml.png "Scheda carichi di lavoro di Visual Studio Installer") ](visualstudio-2017-rc-images/reinstall2-orig.png) b. **Xamarin** nel **singoli componenti** scheda

  [ ![](visualstudio-2017-rc-images/reinstall3-sml.png "Scheda componenti del programma di installazione di Visual Studio")](visualstudio-2017-rc-images/reinstall3-orig.png)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>Tramite il programma di installazione di Visual Studio in Visual Studio:
1. Passare alla pagina iniziale di Visual Studio 2017
2. Fare clic su **più modelli di progetto** sotto il **nuovo progetto** sezione

    [ ![](visualstudio-2017-rc-images/reinstall4-sml.png "Pagina iniziale di Visual Studio")](visualstudio-2017-rc-images/reinstall4-orig.png)
3. Fare clic su `Open Visual Studio Installer` nel riquadro a sinistra

    [ ![](visualstudio-2017-rc-images/reinstall5-sml.png "Schermata del nuovo progetto")](visualstudio-2017-rc-images/reinstall5-orig.png)
4. Selezionare:
    
    a. **Sviluppo per dispositivi mobile con .NET (anteprima)** nella scheda carichi di lavoro, o

    [ ![](visualstudio-2017-rc-images/reinstall2-sml.png "Scheda carichi di lavoro di Visual Studio Installer") ](visualstudio-2017-rc-images/reinstall2-orig.png) b. **Xamarin** nel **singoli componenti** scheda

    [ ![](visualstudio-2017-rc-images/reinstall3-sml.png "Scheda componenti del programma di installazione di Visual Studio")](visualstudio-2017-rc-images/reinstall3-orig.png)
