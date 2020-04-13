---
ms.topic: include
ms.openlocfilehash: b82ebeaeb28195e3ec0f5a0200c0448b6b76196a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259476"
---
## <a name="sensor-speed"></a>[Velocità del sensore](xref:Xamarin.Essentials.SensorSpeed)

- **Fastest** - Ottenere i dati del sensore il più velocemente possibile (non è garantita la restituzione sul thread dell'interfaccia utente).
- **Game** - Velocità adatta ai giochi (non è garantita la restituzione sul thread dell'interfaccia utente).
- **Predefinito:** frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **UI** - Velocità adatta per l'interfaccia utente generale.

Se non è garantito che il gestore eventi venga eseguito nel thread dell'interfaccia [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) utente e se il gestore eventi deve accedere agli elementi dell'interfaccia utente, usare il metodo per eseguire tale codice nel thread dell'interfaccia utente.
