---
title: Configurazione di Visual Studio per lo sviluppo iOS
description: Questo articolo descrive come configurare Visual Studio 2019 per lo sviluppo di Xamarin.iOS. In particolare, illustra come configurare la versione installata di Xamarin.iOS, la barra degli strumenti di iOS e il menu a discesa Piattaforme soluzione.
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: c5c819221bc08f1c39bde9b5900c2f9f9889764d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022514"
---
# <a name="configuring-visual-studio-for-ios-development"></a>Configurazione di Visual Studio per lo sviluppo iOS

_Questo articolo descrive le diverse opzioni di configurazione di Xamarin.iOS per Visual Studio._

## <a name="using-matching-xamarinios-versions"></a>Uso di versioni di Xamarin.iOS corrispondenti

Visual Studio 2019 o Visual Studio 2017 deve usare la stessa versione di Xamarin.iOS installata nell'host di compilazione Mac. Per verificare questa condizione:

- Se si usa Visual Studio 2019 o Visual Studio 2017 selezionare il canale aggiornamenti **Stabile** in Visual Studio per Mac.

- Se si usa Visual Studio 2019 Preview, selezionare il canale aggiornamenti **Alfa** in Visual Studio per Mac.

> [!NOTE]
> A partire da [Visual Studio 2017 versione 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2017 rileva automaticamente se l'host di compilazione Mac sta usando la stessa versione di Xamarin.iOS di Windows. In caso di mancata corrispondenza della versione, Visual Studio 2017 propone di installare remotamente la versione corretta nell'host di compilazione Mac. Per altre informazioni, rivedere la sezione [Provisioning automatico del Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) della guida [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="ios-toolbar"></a>Barra degli strumenti iOS

Quando un progetto iOS è aperto in Visual Studio 2019 o Visual Studio 2017, la barra degli strumenti iOS dovrebbe essere visibile.  Per impostazione predefinita, contiene quattro pulsanti utili per lo sviluppo con Xamarin.iOS:

![Barra degli strumenti iOS di Visual Studio 2019](config-options-images/ios-toolbar.png)

- **Associa a Mac**: apre la finestra di dialogo Associa a Mac. Abilitato quando un progetto iOS è aperto in Visual Studio 2019 o Visual Studio 2017.
- **Mostra il simulatore iOS**: nell'host di compilazione Mac visualizza il simulatore iOS in primo piano. Abilitato quando un progetto iOS è aperto in Visual Studio 2019 o Visual Studio 2017.
- **Log del dispositivo**: visualizza una finestra che consente di controllare i log del dispositivo. Abilitato quando un progetto iOS è aperto in Visual Studio 2019 o Visual Studio 2017.
- **Mostra il file IPA nel server di compilazione**: apre una finestra nell'host di compilazione Mac che mostra il percorso del file con estensione ipa per l'app. Abilitato dopo il completamento di una build per cui è stato creato il file con estensione ipa.

Se questa barra degli strumenti non viene visualizzata, aprire il menu **Visualizza** in Visual Studio 2019 o Visual Studio 2017 e scegliere **Barre degli strumenti > iOS**:

![Abilitazione della barra degli strumenti di iOS](config-options-images/ios-toolbar-enable.png "Abilitazione della barra degli strumenti di iOS")

## <a name="solution-platforms-drop-down-menu"></a>Menu a discesa Piattaforme soluzione

Il menu a discesa **Piattaforme soluzione** consente di scegliere se la build successiva verrà associata a un dispositivo fisico o a un simulatore.

Per assicurarsi che questo menu a discesa sia visibile nella barra degli strumenti Standard:

- In Visual Studio 2019 o Visual Studio 2017 fare clic sulla freccia giù all'estremità destra della barra degli strumenti Standard.
- Scegliere **Aggiungi o rimuovi pulsanti** 
- Assicurarsi che la voce **Piattaforme soluzione** sia selezionata:

![Attivazione del menu a discesa Piattaforme soluzione](config-options-images/solution-platforms-enable.png "Attivazione del menu a discesa Piattaforme soluzione")

Con un progetto iOS aperto, le barre degli strumenti **Standard** e **iOS** dovrebbero avere un aspetto simile alla schermata seguente:

![Barre degli strumenti standard e iOS](config-options-images/toolbars.png "Barre degli strumenti standard e iOS")
