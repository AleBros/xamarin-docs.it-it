---
title: Perché la finestra di progettazione XAML di Visual Studio non funziona per Xamarin.Forms i file XAML?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9f9cf570da0d8e078d1d05e74dc0f65994080c6c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135889"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Perché la finestra di progettazione XAML di Visual Studio non funziona per Xamarin.Forms i file XAML?

Xamarin.FormsAttualmente non supporta le finestre di progettazione visiva per i file XAML. Per questo motivo, quando si tenta di aprire un file XAML di form nella finestra di *progettazione* dell'interfaccia utente XAML di Visual Studio o nella *finestra di progettazione dell'interfaccia utente XAML con codifica*, viene generato il messaggio di errore seguente:

> "Impossibile aprire il file con l'editor selezionato. Scegliere un altro editor. "

Questa limitazione è descritta nella Guida alle [ Xamarin.Forms nozioni di base di XAML](~/xamarin-forms/xaml/xaml-basics/index.md) :

> "Non esiste ancora una finestra di progettazione visiva per la generazione di XAML nelle Xamarin.Forms applicazioni, quindi tutto il codice XAML deve essere scritto manualmente".

Tuttavia, il visualizzatore Xamarin.Forms Anteprima XAML può essere visualizzato selezionando la vista > altre opzioni di menu di **Windows > Xamarin.Forms ** Visualizzatore anteprima.
