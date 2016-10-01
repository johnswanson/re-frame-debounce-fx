
[![GitHub license](https://img.shields.io/github/license/johnswanson/re-frame-debounce-fx.svg)](license.txt)

This library (and frankly this README too) was shamelessly cribbed from the [re-frame-http-fx](https://github.com/Day8/re-frame-http-fx) library.

## Debounce Effects Handler for re-frame

This re-frame library contains a debounce [Effect Handler](https://github.com/Day8/re-frame/tree/develop/docs).


## Quick Start Guide

### Step 1. Add Dependency

Add the following project dependency: <br>
[![Clojars Project](https://img.shields.io/clojars/v/org.clojars.jds02006/debounce-fx.svg)](https://clojars.org/org.colojars.jds02006/debounce-fx)

Requires re-frame >= 0.8.0

### Step 2. Registration And Use

In the namespace where you register your event handlers, perhaps called `events.cljs`, you have 2 things to do.

**First**, add this "require" to the `ns`:
```clj
(ns app.core
  (:require
    ...
    [johnswanson.re-frame.debounce-fx]   ;; <-- add this
    ...))
```

Because we never subsequently use this `require`, it
appears redundant.  But its existence will cause the `:dispatch-debounce` effect
handler to self-register with re-frame, which is important
to everything that follows.

**Second**, write a an event handler which uses this effect:
```clj
(reg-event-fx                           ;; note the trailing -fx
  :handler-with-debounce                ;; usage:  (dispatch [:handler-with-debounce])
  (fn [fx [_ value]]                    ;; the first param will be "world"
    {:dispatch-debounce {:key :search
                         :event [:search value]
                         :delay 250}}))
```

Look at the `:handler-with-debounce` line above. This library defines the "effects handler"
which implements `:handler-with-debounce`.

The supplied value is a simple options map with three required keys: `:key`, `:event`, and `:delay`.

When the effect fires, we will wait for `:delay` ms. At that point, we'll check to see whether another `:handler-with-debounce` effect has fired with the same `:key` since our effect was fired.

- If not, we will dispatch our event.
- If another event fired, we will do nothing (another event is now pending)

