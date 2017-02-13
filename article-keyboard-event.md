Internationalize your game controls
===================================

Recently two lovely graphical demos were published, where controls wasn't
working on my french AZERTY keyboard.

There was the wonderful WebGL 2 technological demo [After The
Flood](https://playcanv.as/e/p/44MRmJRU/), and the very cute [Alpaca
Peck](http://codepen.io/shshaw/full/apwMwM/). Shaw was nice enough to fix the
latter when I told him about the issue. It turns out our lovely Web Browsers
actually expose a useful API for this.

Let's dig a little bit into the problem we have.


Keyboards layouts
-----------------
I'm sure you know this already, but maybe you never took the time to really
understand what it means: people around the world use different keyboard
layouts. You can read a lot on [Wikipedia's dedicated
page](https://en.wikipedia.org/wiki/Keyboard_layout), but I'll try to
summarise the important bits here.

The best-known layout is QWERTY, used in most of the world and AZERTY in some
french-speaking countries. But AZERTY is also used a lot in some french-speaking
country, as well as QWERTZ in Germany and other european countries, and DVORAK
as an alternative to QWERTY.

All these layouts also have variants, especially in the symbols in the top bar,
or the rightmost keys. Two QWERTY keyboards might not be exactly the same. For
example spanish keyboards have a special key for _ñ_, and german keyboards have
special keys for _ä_ and _ö_.

But what's important to understand is that the keyboards
have the same structure for all layouts. The keys are mostly at the same
place, although can be slightly arranged. This is called the _mechanical
layout_.

So what we'll call a regional layout is:
* what's physically printed on the physical key; this is called the _visual
    layout_.
* software (driver) mapping the hardware keys to characters; this is called the
    _functional layout_.

This also means that we can actually _change the layout used in the operating
system without changing the physical keyboard_. They are two different things.
Some users will install improved layout drivers to be able to type faster or
more easily some characters. This is especially useful when some useful
characters are not normally available on the layout; to type in French I can
very easily reach _É_, _È_, _Ç_ or the french quotes _«_ and _»_.

But it comes also handy when you need to write text in several languages: I
don't have the _ø_ character anywhere on my keyboard but my driver allows me to
type it easily.

What happens on the Web?
------------------------
Well, to be completely honest with you, [it used to be a complete
mess](http://unixpapa.com/js/key.html). Then we converged to a cross-browser
behavior appropriate for QWERTY keyboards.


The new API, part of UI Events
------------------------------
UI Events, formerly known as DOM Level 3 Events, is a W3C specification in
discussion since 2000. It's still being discussed as a Working Draft, but
because most browsers seem to agree today, we can hope that the specification
will move forward to a recommendation. [Latest working draft is available on
line](https://w3c.github.io/uievents/#events-keyboardevents) as is [latest published
version](https://www.w3.org/TR/uievents/#https://www.w3.org/TR/uievents/#events-keyboardevents).

The new API brings two new very useful properties to a `KeyboardEvent` event:
_key_ and _code_. They replace the previously existing (and _still_ existing)
`charCode`, `keyCode`, and `which`.

Let's see why they are so useful, especially to do cross-keyboard websites (if
you allow me this neologism).



Other useful things to know about this new API
----------------------------------------------
Without entering into too much detail, let's fly over some other significant
functionalities in the API:

* The `keypress` event is deprecated. Now you should always use `keydown`
    instead. The event `beforeinput` is also planned but isn't supported by any
    stable version of a browser (Chrome Canary has an implementation). The event
    `input` is a higher-level event that is also useful in some situations.
* With the `location` property on `KeyboardEvent`, if a pressed key exists in
    several locations — eg the Shift or Ctrl keys, or the digits —, then you can
    know which one was really used. For example you can know whether the pressed
    key is in the Numlock Keyboard or on the digit top bar.
    Note that this information is also contained
    in the `code` property, as every physical key gets its own `code`.
* The `repeat` property is set to `true` if the user kept a key depressed and a
    event was sent repeatidly as a result.
* If you want to know if a modifier key is depressed while handling another key's
    `KeyboardEvent`, you don't need to keep track of the state yourself. The
    boolean properties `altKey`, `ctrlKey`, `metaKey`, `shiftKey` as well as the
    method [getModifierState](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState)
    can give you the state of various modifier keys when the event was
    triggered.


