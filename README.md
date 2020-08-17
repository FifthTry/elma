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
- elma.json
```

`elma.json`:

```json
{
    "schema": "schema-for-this-elma",
    "kind": "helma | nelma etc"
}
```

Schemas are managed by fifthtry for now.

Compiler will inject `Main.elm` and `Fifthtry.elm` (part of this repo) next to "elm.json", and run elm-make on it.

For "helma" and "nelma", `Main.elm` imports `App` and calls `App.app` which must return a `Fifthtry.App`:

```elm
-- helma:


type alias App =
    { view : Fifthtry.In -> Fifthtry.Config -> Fifthtry.Html
    }


-- nelma:


type alias App model msg =
    { init : Fifthtry.In -> Fifthtry.Config -> ( model, Fifthtry.Cmd msg )
    , update : Fifthtry.In -> msg -> model -> ( model, Fifthtry.Cmd Msg msg )
    , subscriptions : Fifthtr.In -> model -> Fifthtry.Sub msg
    , view : Fifthtry.In -> model -> Fifthtry.Html msg
    }
```

The `Fifthtry.Config` type will depend on `schema` key on `elma.json`.

`Fifthtry.Cmd`, `Fifthtry.Sub` and `Fifthtry.Html` are "safe" commands, subscription and html that is allowed (no network request allowed, no port stuff etc).

In case of `welma`, instead of `App.elm`, app must provide two files: `Reader.elm` and `Writer.elm`. Reader and Writer uses the same `Fifthtry.App` as `nelma`. Writer gets access to following `Fifthtry.Cmd` constructor: `Fifthtry.Content : Config -> Fifthtry.Cmd msg`. This must be called by writer whenever the content changes.

In case of `celma`, the same `App` as in case of `Nelma` will be used.

