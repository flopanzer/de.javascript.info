# Debugging in Chrome

Bevor wir komplexeren Code schreiben, lassen uns das Debuggen genauer anschauen.

[Debugging](https://en.wikipedia.org/wiki/Debugging) ist der Prozess des Findens und Behebens von Fehlern innerhalb eines Skripts. Alle modernen Browser und die meisten anderen Entwicklungsumgebungen unterstützen Debugging-Werkzeuge - eine spezielle UI in den Entwicklertools, die das Debugging wesentlich erleichtert. Es erlaubt auch, den Code Schritt für Schritt zurückzuverfolgen, um zu sehen, was genau vor sich geht.

Wir werden hier Chrome verwenden, weil es genügend Funktionen hat. Die meisten anderen Browser sind ähnlich aufgebaut.

## Das "Sources" Panel

Deine Version von Chrome sieht vielleicht ein wenig anders aus, aber es sollte trotzdem zu erkennen sein, was hier gemeint ist.

- Öffne die [Beispielseite](debugging/index.html) in Chrome.
- Aktiviere die Entwicklertools mit `key:F12` (Mac: `key:Cmd+Opt+I`).
- Wähle das `Sources` Panel an.

So sollte es aussehen, wenn du es zum ersten Mal öffnest:

![](chrome-open-sources.svg)

Die Umschaltfläche <span class="devtools" style="background-position:-172px -98px"></span> öffnet das Tab mit den Dateien.

Klicken wir es an und wählen `hello.js` in der Baumansicht. Das sollte jetzt angezeigt werden:

![](chrome-tabs.svg)

Das Source Panel besteht aus drei Teilen:

1. Der Bereich **Datei-Navigator** listet HTML-, JavaScript-, CSS- und andere Dateien auf, einschließlich der Bilder, die an die Seite angehängt sind. Hier können auch Chrome-Erweiterungen erscheinen.
2. Der Bereich **Code Editor** zeigt den Quellcode an.
3. Der Bereich **JavaScript Debugging** dient zum Debuggen. Den werden wir uns bald näher anschauen.

Nun kannst Du die gleiche Umschaltfläche <span class="devtools" style="background-position:-172px -122px"></span> erneut anklicken, um die Ressourcenliste auszublenden und dem Code etwas mehr Platz zu geben.

## Die Konsole

Wenn wir `key:Esc` drücken, dann öffnet sich unten eine Konsole. Dort können wir Befehle eingeben und mit `key:Enter` ausführen.

Nachdem eine Anweisung ausgeführt wurde, wird ihr Ergebnis unten angezeigt.

Zum Beispiel ergibt hier `1+2` `3`, und `hello("Debugger")` gibt nichts zurück, so dass das Ergebnis `undefined` ist:

![](chrome-sources-console.svg)

## Breakpoints

Untersuchen wir zunächst, was im Code der [Beispielseite](debugging/index.html) vor sich geht. Klicke in `hello.js` auf die Zeilennummer `4`. Ja, direkt auf die Ziffer `4`, nicht auf den Code.

Herzlichen Glückwunsch! Wir haben einen Haltepunkt gesetzt. Bitte klick danach auch auf die Nummer für die Zeile `8`.

Es sollte so aussehen (blau ist die Stelle, auf die wir klicken sollten):

![](chrome-sources-breakpoint.svg)

Ein *Breakpoint* ist ein Codepunkt, an dem der Debugger die Ausführung des JavaScript-Codes automatisch unterbricht.

Während der Code pausiert wird, können wir aktuelle Variablen untersuchen, Befehle in der Konsole ausführen usw. Mit anderen Worten, wir können ihn debuggen.

Eine Liste der Breakpoints finden wir immer im rechten Panel. Das ist zum Beispiel nützlich, wenn wir viele Haltepunkte in verschiedenen Dateien haben. Es erlaubt uns dann:
- Schnell zu dem Haltepunkt im Code zu springen (durch Anklicken im rechten Panel).
- Den Haltepunkt vorübergehend zu deaktivieren, indem wir das Häkchen entfernen.
- Den Haltepunkt zu entfernen, indem wir rechtsklicken und Entfernen wählen.
- ...und so weiter.

````smart header="Conditional breakpoints"
*Rechtsklick* auf die Zeilennummer erlaubt es, einen *bedingten*('conditional') Haltepunkt zu erzeugen. Er wird nur ausgelöst, wenn der angegebene Ausdruck wahr ist.

Das ist praktisch, wenn wir nur für einen bestimmten Variablenwert oder für bestimmte Funktionsparameter die Ausführung unterbrechen wollen.
```

## Debugger command

Wir können den Code auch pausieren, indem wir den `debugger`-Befehl darin verwenden, etwa so:

```js
function hello(name) {
  let phrase = `Hello, ${name}!`;

*!*
  debugger;  // <-- the debugger stops here
*/!*

  say(phrase);
}
```

That's very convenient when we are in a code editor and don't want to switch to the browser and look up the script in developer tools to set the breakpoint.


## Pause and look around

In our example, `hello()` is called during the page load, so the easiest way to activate the debugger (after we've set the breakpoints) is to reload the page. So let's press `key:F5` (Windows, Linux) or `key:Cmd+R` (Mac).

As the breakpoint is set, the execution pauses at the 4th line:

![](chrome-sources-debugger-pause.svg)

Please open the informational dropdowns to the right (labeled with arrows). They allow you to examine the current code state:

1. **`Watch` -- shows current values for any expressions.**

    You can click the plus `+` and input an expression. The debugger will show its value at any moment, automatically recalculating it in the process of execution.

2. **`Call Stack` -- shows the nested calls chain.**

    At the current moment the debugger is inside `hello()` call, called by a script in `index.html` (no function there, so it's called "anonymous").

    If you click on a stack item (e.g. "anonymous"), the debugger jumps to the corresponding code, and all its variables can be examined as well.
3. **`Scope` -- current variables.**

    `Local` shows local function variables. You can also see their values highlighted right over the source.

    `Global` has global variables (out of any functions).

    There's also `this` keyword there that we didn't study yet, but we'll do that soon.

## Tracing the execution

Now it's time to *trace* the script.

There are buttons for it at the top of the right panel. Let's engage them.
<!-- https://github.com/ChromeDevTools/devtools-frontend/blob/master/front_end/Images/src/largeIcons.svg -->
<span class="devtools" style="background-position:-146px -168px"></span> -- "Resume": continue the execution, hotkey `key:F8`.
: Resumes the execution. If there are no additional breakpoints, then the execution just continues and the debugger loses control.

    Here's what we can see after a click on it:

    ![](chrome-sources-debugger-trace-1.svg)

    The execution has resumed, reached another breakpoint inside `say()` and paused there. Take a look at the "Call Stack" at the right. It has increased by one more call. We're inside `say()` now.

<span class="devtools" style="background-position:-200px -190px"></span> -- "Step": run the next command, hotkey `key:F9`.
: Run the next statement. If we click it now, `alert` will be shown.

    Clicking this again and again will step through all script statements one by one.

<span class="devtools" style="background-position:-62px -192px"></span> -- "Step over": run the next command, but *don't go into a function*, hotkey `key:F10`.
: Similar to the previous the "Step" command, but behaves differently if the next statement is a function call. That is: not a built-in, like `alert`, but a function of our own.

    The "Step" command goes into it and pauses the execution at its first line, while "Step over" executes the nested function call invisibly, skipping the function internals.

    The execution is then paused immediately after that function.

    That's good if we're not interested to see what happens inside the function call.

<span class="devtools" style="background-position:-4px -194px"></span> -- "Step into", hotkey `key:F11`.
: That's similar to "Step", but behaves differently in case of asynchronous function calls. If you're only starting to learn JavaScript, then you can ignore the difference, as we don't have asynchronous calls yet.

    For the future, just note that "Step" command ignores async actions, such as `setTimeout` (scheduled function call), that execute later. The "Step into" goes into their code, waiting for them if necessary. See [DevTools manual](https://developers.google.com/web/updates/2018/01/devtools#async) for more details.

<span class="devtools" style="background-position:-32px -194px"></span> -- "Step out": continue the execution till the end of the current function, hotkey `key:Shift+F11`.
: Continue the execution and stop it at the very last line of the current function. That's handy when we accidentally entered a nested call using <span class="devtools" style="background-position:-200px -190px"></span>, but it does not interest us, and we want to continue to its end as soon as possible.

<span class="devtools" style="background-position:-61px -74px"></span> -- enable/disable all breakpoints.
: That button does not move the execution. Just a mass on/off for breakpoints.

<span class="devtools" style="background-position:-90px -146px"></span> -- enable/disable automatic pause in case of an error.
: When enabled, and the developer tools is open, a script error automatically pauses the execution. Then we can analyze variables to see what went wrong. So if our script dies with an error, we can open debugger, enable this option and reload the page to see where it dies and what's the context at that moment.

```smart header="Continue to here"
Right click on a line of code opens the context menu with a great option called "Continue to here".

That's handy when we want to move multiple steps forward to the line, but we're too lazy to set a breakpoint.
```

## Logging

To output something to console from our code, there's `console.log` function.

For instance, this outputs values from `0` to `4` to console:

```js run
// open console to see
for (let i = 0; i < 5; i++) {
  console.log("value,", i);
}
```

Regular users don't see that output, it is in the console. To see it, either open the Console panel of developer tools or press `key:Esc` while in another panel: that opens the console at the bottom.

If we have enough logging in our code, then we can see what's going on from the records, without the debugger.

## Summary

As we can see, there are three main ways to pause a script:
1. A breakpoint.
2. The `debugger` statements.
3. An error (if dev tools are open and the button <span class="devtools" style="background-position:-90px -146px"></span> is "on").

When paused, we can debug - examine variables and trace the code to see where the execution goes wrong.

There are many more options in developer tools than covered here. The full manual is at <https://developers.google.com/web/tools/chrome-devtools>.

The information from this chapter is enough to begin debugging, but later, especially if you do a lot of browser stuff, please go there and look through more advanced capabilities of developer tools.

Oh, and also you can click at various places of dev tools and just see what's showing up. That's probably the fastest route to learn dev tools. Don't forget about the right click and context menus!
