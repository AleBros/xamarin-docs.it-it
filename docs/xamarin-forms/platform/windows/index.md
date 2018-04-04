---
title: Funzionalità della piattaforma Windows
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ab6b12738028b4f3439629f334ed5429244f4d5a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="windows-platform-features"></a>Funzionalità della piattaforma Windows

Sviluppo di applicazioni di xamarin. Forms per le piattaforme Windows richiede Visual Studio. Il [nella pagina requisiti](~/xamarin-forms/get-started/installation.md) contiene altre informazioni sui prerequisiti.

![](images/allhanselman.png "Xamarin. Forms applicazioni in esecuzione in Windows")

## <a name="platform-support"></a>Supporto per piattaforme

Per impostazione predefinita, i modelli di xamarin. Forms disponibili in Visual Studio contengono un progetto di Windows:

* **App di Windows universale** -App xamarin. Forms possono anche essere ottimizzate per Windows 10. Universale (UWP) è possono eseguire sul telefono, tablet e dispositivi desktop.

Se le opzioni di sviluppo corretto è stato installato in Visual Studio, è anche possibile [aggiungere](installation/index.md) questi tipi per supportare le versioni precedenti di Windows di progetti:

* **Windows 8.1** : È possibile distribuire le app xamarin. Forms tablet e fattori di forma desktop come un'app di Windows 8.1 progetto che utilizza controlli WinRT.
* **Windows Phone 8.1** -xamarin. Forms fornisce supporto completo per la piattaforma Windows Phone 8.1 usando WinRT. L'aspetto delle App con il supporto di Windows Phone 8.1 potrebbero essere diverso per le app di Windows Phone xamarin. Forms precedente basato su Silverlight.


> [!NOTE]
> Supporto di 1. x e 2. x di xamarin. Forms _Windows Phone 8 Silverlight_ lo sviluppo di applicazioni, tuttavia questo tipo di progetto è stato deprecato.


## <a name="getting-started"></a>Introduzione

Passare a **File > Nuovo > progetto** in Visual Studio e scegliere una del **multipiattaforma > applicazione vuota (xamarin. Forms)** modelli per iniziare.

Soluzioni xamarin. Forms meno recenti o quelli creati nel macOS, non tutti i progetti di Windows elencati in precedenza (ma è necessario essere aggiunti manualmente).
Se la piattaforma Windows, il sistema di destinazione non è già nella soluzione, visitare il [istruzioni di installazione](installation/index.md) per aggiungere Windows desiderato progetto tipo/s.


## <a name="samples"></a>Esempi

[Tutti gli esempi](https://github.com/xamarin/xamarin-forms-book-preview-2) per libro Petzolds [ *la creazione di App per dispositivi mobili con xamarin. Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) includono Windows Phone 8.1, Windows 8.1 e progetti di Universal Windows Platform (per Windows 10).

Il ["Scott Hanselman" demo app](https://github.com/jamesmontemagno/Hanselman.Forms) disponibile separatamente e include anche i progetti di Apple Watch e accenti Android (rispettivamente tramite xamarin. IOS e xamarin, xamarin. Forms non viene eseguita su tali piattaforme).


## <a name="related-links"></a>Collegamenti correlati

- [Progetti di installazione Windows](~/xamarin-forms/platform/windows/installation/index.md)
