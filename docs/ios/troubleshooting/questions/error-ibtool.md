---
title: 'Errore IBTool: Impossibile completare l''operazione.'
ms.topic: article
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dd668859428da1abfa3a8e46a0810b2de6645fe2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Errore IBTool: Impossibile completare l'operazione.

## <a name="fixed-in-xcode-611"></a>Risolto in Xcode 6.1.1

Apple [fissa](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) questo `ibtool` bug in Xcode 6.1.1, pertanto l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

* * *

## <a name="description-of-the-problem"></a>Descrizione del problema

Il `ibtool` comando Xcode 6.0 ha un bug in OS X 10.10 Yosemite. Xamarin utilizza del Xcode `ibtool` per compilare gli storyboard e `XIB` file.

Ulteriori informazioni sul bug in relazione a Xcode sono disponibili nei seguenti post Overflow dello Stack: [http://stackoverflow.com/questions/25754763/cant-open-storyboard](http://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Messaggio di errore

> Impossibile aprire il documento "Mainstoryboard". Non è stato possibile completare l'operazione. (errore com.apple.InterfaceBuilder -1).

## <a name="workarounds-for-xcode-60"></a>Soluzioni alternative (per Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opzione 1: Gestire tutti `UIImageView.Image` proprietà nel codice

Anziché impostare la `Image` proprietà di un `UIImageView` dello storyboard o `.xib` file, è possibile impostare la proprietà in una vista del ciclo di vita di metodi di override nel controller di visualizzazione (ad esempio, in `ViewDidLoad()`). Vedere anche [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) per suggerimenti sull'utilizzo `UIImage.FromBundle()` e `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opzione 2: Spostare tutte le risorse dell'immagine di primo livello `Resources` cartella

Dopo avere spostato le immagini di primo livello `Resources` cartella, sarà necessario aggiornare lo storyboard e `.xib` file da utilizzare i nuovi percorsi di immagine.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opzione 3: Impostare il `LogicalName` per tutte le risorse immagine problematico in modo al livello superiore di questi elementi vengono copiati il`.app` bundle

Ad esempio originale `.csproj` file contiene la voce seguente:

`<BundleResource Include="Resources\Images\image.png" />`

È possibile modificare questo elemento e aggiungere un `LogicalName` in modo che l'immagine verrà copiata invece al livello superiore del `.app `bundle:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

In Visual Studio per Mac il `LogicalName` può essere impostato anche usando il `Resource ID` campo per l'immagine in **Vista > Pad > proprietà**. (Vedere anche: [http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Dopo questa modifica, sarà necessario aggiornare lo storyboard e `.xib` file da utilizzare i nuovi percorsi di primo livello immagine. Visual Studio per Mac aggiornerà automaticamente l'elenco di autocompletions per il `Image` proprietà nella finestra di progettazione iOS. In Visual Studio, è necessario modificare manualmente il percorso. La finestra di progettazione iOS verrà quindi visualizzare questo elemento come un'immagine manca, ma il progetto viene compilato ed eseguito correttamente.

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug. 

