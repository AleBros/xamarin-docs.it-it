---
title: Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247796"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?

Xamarin. Forms attualmente non supporta finestre di progettazione visiva per i file XAML. Per questo motivo, quando si tenta di aprire un file XAML form in Visual Studio *finestra di progettazione dell'interfaccia utente XAML* o *progettazione interfaccia utente XAML con codifica*, viene generato il messaggio di errore seguente:

> "Impossibile aprire il file con l'editor selezionato. Scegliere un altro editor."

Questa limitazione è descritta nel [Overview](~/xamarin-forms/xaml/xaml-basics/index.md#Overview) sezione del [nozioni di base di xamarin. Forms XAML](~/xamarin-forms/xaml/xaml-basics/index.md) Guida:

> "Non esiste ancora una finestra di progettazione visiva per la generazione di XAML in applicazioni xamarin. Forms, in modo che tutte XAML deve essere scritta a mano".

Tuttavia, il Visualizzatore anteprima XAML di xamarin. Forms possono essere visualizzato selezionando il **View > Other Windows > Visualizzatore anteprima di xamarin. Forms** l'opzione di menu.
