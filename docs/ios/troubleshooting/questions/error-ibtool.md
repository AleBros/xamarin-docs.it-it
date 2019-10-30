---
title: "Errore IBTool: non è stato possibile completare l'operazione."
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 4ff7b88ad63870246acbf2b29d01775f6711a8ce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031181"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Errore IBTool: non è stato possibile completare l'operazione.

## <a name="fixed-in-xcode-611"></a>Correzione in Xcode 6.1.1

Apple ha [corretto](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) questo `ibtool` bug in Xcode 6.1.1, quindi l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

* * *

## <a name="description-of-the-problem"></a>Descrizione del problema

Il comando `ibtool` in Xcode 6,0 presenta un bug in OS X 10,10 Yosemite. Novell. iOS USA `ibtool` di Xcode per compilare gli storyboard e i file di `XIB`.

Altre informazioni sul bug in relazione a Xcode sono disponibili nel post di Stack Overflow seguente: [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Messaggio di errore

> Non è stato possibile aprire il documento "file mainstoryboard. Storyboard". Non è stato possibile completare l'operazione. (errore com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Soluzioni alternative (per Xcode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opzione 1: gestire tutte `UIImageView.Image` proprietà nel codice

Anziché impostare la proprietà `Image` di un `UIImageView` nello storyboard o nel file `.xib`, è possibile impostare la proprietà in uno dei metodi di override della visualizzazione del ciclo di vita nel controller di visualizzazione (ad esempio, in `ViewDidLoad()`). Vedere anche uso delle immagini per suggerimenti sull'uso di `UIImage.FromBundle()` [e](~/ios/app-fundamentals/images-icons/index.md) `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opzione 2: spostare tutte le risorse dell'immagine nella cartella `Resources` di primo livello

Dopo aver spostato le immagini nella cartella `Resources` di primo livello, sarà necessario aggiornare lo storyboard e i file `.xib` per usare i nuovi percorsi delle immagini.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opzione 3: impostare l'`LogicalName` per gli asset di immagine problematici in modo che vengano copiati nel primo livello del bundle di`.app`

Si immagini, ad esempio, che il file di `.csproj` originale includa la voce seguente:

`<BundleResource Include="Resources\Images\image.png" />`

È possibile modificare questo elemento e aggiungere un `LogicalName` in modo che l'immagine venga copiata al livello superiore del bundle `.app`:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

In Visual Studio per Mac la `LogicalName` può essere impostata anche usando il campo `Resource ID` per l'immagine in **visualizza > rilievi > proprietà**. (Vedere anche: [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Dopo questa modifica, sarà necessario aggiornare lo storyboard e i file di `.xib` per usare i nuovi percorsi immagine di primo livello. Visual Studio per Mac aggiornerà automaticamente l'elenco dei completamenti automatici per la proprietà `Image` in iOS designer. In Visual Studio è necessario modificare il percorso manualmente. In iOS Designer verrà visualizzato come immagine mancante, ma il progetto verrà compilato ed eseguito correttamente.

### <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario . 
