---
title: "Errore di compilazione: Impossibile codificare l'offset X nella rilocazione distribuita risultante"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876230"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>Errore di compilazione: Impossibile codificare l'offset X nella rilocazione distribuita risultante

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

Questo problema si verifica quando si compila per le architetture a 32 bit, ad esempio ARMv7, quando il file binario finale è troppo grande per la piattaforma nativa.

Per risolvere il problema:

- Drop Building per tale architettura nel riquadro Build iOS delle opzioni del progetto.
- Abilitare il collegamento o rimuovere manualmente il codice per ridurre le dimensioni del file eseguibile finale
