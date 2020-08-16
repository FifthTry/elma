# elma

Secure Elm Components (from Github, for FifthTry).

We will have 4 types of components: helma, nelma, welma and celma. 

## helma

These are most effecient, no JS dependency when viewing. HTML is cached along with ftd in an "side-channel" cache. Good enough for most landing page.

They may need a string formatting support if they are to construct untrusted URLs for iframe/img-src etc.

## nemla

These are "normal" elm stuff, they can have functionality (eg update etc), so richer than helma, but they have JS so slightly less optimal.

## welma

These are nelma that also provide a writer UI.

## celma

These provide a combined reader-writer UI. They seem best ("most user friendly"), but risk zarring the end user as end users may not be familiar with end UI.

These will need write api in "read" UI.

# how will it work?

A sample helma is at amitu/team: these are called component repos.

This repo is the compiler. Compiler will get the name of component (eg "amitu/team"). Compiler will download the github repo by that name, and extract it in the "work" folder.

The component repo will have following structure:

```
amitu/team:
- elm.json
- App.elm
- schema.link
```

`schema.link` file must point an existing schema. Schemas are managed by fifthtry for now.

Compiler will inject `Main.elm` and `Fifthtry.elm` (part of this repo) next to "elm.json", and run elm-make on it.

`Main.elm` imports `App` and calls `App.app` which must return a `Fifthtry.App`:

```elm

type alias Helma config =
    { config : JD.Decoder config
    , view : In -> config -> Fifthtry.Html msg
    }

type alias Nelma config model msg =
    { config : JD.Decoder config
    , init : In -> config -> ( model, Fifthtry.Cmd msg )
    , update : In -> msg -> model -> ( model, Fifthtry.Cmd Msg msg )
    , subscriptions : In -> model -> Fifthtry.Sub msg
    , view : In -> model -> Fifthtry.Html msg
    }
```

`Fifthtry.Cmd`, `Fifthtry.Sub` and `Fifthtry.Html` are "safe" commands, subscription and html that is allowed (no network request allowed, no port stuff etc).

WIP.
