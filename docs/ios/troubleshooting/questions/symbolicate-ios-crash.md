---
title: Dove trovare il file .dSYM symbolicate i registri di arresto anomalo iOS
ms.topic: article
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 223e1977bb6229760d6428fdca2f5372b1e25c23
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Dove trovare il file .dSYM symbolicate i registri di arresto anomalo iOS

Quando si compila le app iOS da visual studio, il file .dSYM che può essere usato per symbolicate segnalazioni di arresti anomali finisce nell'host di compilazione nel percorso:
```
    /Users/<username>/Library/Caches/Xamarin/mtbs/builds/<appname>/<guid>/bin/iPhone/<configuration>
```

Si noti che il `~/Library` cartella è nascosto per impostazione predefinita in Finder, pertanto, se necessario utilizzare del Finder **passare > passare alla cartella** menu e immettere: `~/Library/Caches/Xamarin/mtbs/builds/` per aprire la cartella.  

In alternativa è possibile scoprire il `~/Library` cartella utilizzando il **Mostra opzioni di visualizzazione** pannello per la cartella principale. Se si seleziona la cartella principale nella barra laterale in Finder e utilizzare il menu Finder **Vista > Mostra opzioni di visualizzazione** (o cmd + j), verrà visualizzata una casella di controllo **Mostra cartella libreria**.


### <a name="see-also"></a>Vedere anche
- Procedura estesa per symbolicating iOS segnalazioni di arresto anomalo: [http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demistificazione iOS i registri di arresto anomalo applicazioni](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
