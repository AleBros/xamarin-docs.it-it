---
title: "Errore IBTool: Non è stato possibile completare l'operazione."
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 39b522af5bdc3587e3d5aa1451ed4879c6af65f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769330"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Errore IBTool: Non è stato possibile completare l'operazione.

## <a name="fixed-in-xcode-611"></a>Correzione in Xcode 6.1.1

Apple [ha risolto](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) questo bug di `ibtool` in Xcode 6.1.1, quindi l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

* * *

## <a name="description-of-the-problem"></a>Descrizione del problema

Il `ibtool` comando in Xcode 6,0 presenta un bug in OS X 10,10 Yosemite. Novell. iOS USA Xcode per `ibtool` compilare gli storyboard e `XIB` i file.

Altre informazioni sul bug in relazione a Xcode sono disponibili nel post di Stack Overflow seguente:[https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Messaggio di errore

> Non è stato possibile aprire il documento "file mainstoryboard. Storyboard". Non è stato possibile completare l'operazione. (errore com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Soluzioni alternative (per Xcode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opzione 1: Gestione di `UIImageView.Image` tutte le proprietà nel codice

Anziché impostare la `Image` proprietà di un oggetto `UIImageView` nello storyboard o `.xib` nel file, è possibile impostare la proprietà in uno dei metodi di override della visualizzazione del ciclo di vita nel controller di visualizzazione (ad `ViewDidLoad()`esempio, in). Vedere anche uso delle [Immagini](~/ios/app-fundamentals/images-icons/index.md) per suggerimenti sull'uso `UIImage.FromBundle()` di Visual `UIImage.FromFile()`Studio.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opzione 2: Spostare tutte le risorse dell'immagine nella cartella di primo livello `Resources`

Dopo aver spostato le immagini nella cartella di primo `Resources` livello, sarà necessario aggiornare lo storyboard e `.xib` i file in modo da usare i nuovi percorsi delle immagini.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opzione 3: Impostare per eventuali asset di immagine problematici in modo che vengano copiati nel primo livello`.app` del bundle `LogicalName`

Si immagini, ad esempio, `.csproj` che il file originale includa la voce seguente:

`<BundleResource Include="Resources\Images\image.png" />`

È possibile modificare questo elemento e aggiungere un `LogicalName` oggetto in modo che l'immagine venga copiata al livello superiore `.app` del bundle:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

In Visual Studio per Mac `LogicalName` è possibile impostare anche usando il `Resource ID` campo per l'immagine in **Visualizza > rilievi > Proprietà**. (Vedere anche: [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Dopo questa modifica, sarà necessario aggiornare lo storyboard e `.xib` i file in modo da usare i nuovi percorsi immagine di primo livello. Visual Studio per Mac aggiornerà automaticamente l'elenco dei completamenti automatici per la `Image` proprietà in iOS designer. In Visual Studio è necessario modificare il percorso manualmente. In iOS Designer verrà visualizzato come immagine mancante, ma il progetto verrà compilato ed eseguito correttamente.

### <a name="next-steps"></a>Fasi successive

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario . 
