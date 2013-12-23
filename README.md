haskell-style-guide
===================

A document describing how I like Haskell code to be written. This is
how I write my code and expect code submitted to my projects to be
in. I'll probably link this to you if I don't sound particularly happy
with a patch you sent me.

Tip: this guide is very easy to follow when you have an editor like
Emacs with
[structured-haskell-mode](https://github.com/chrisdone/structured-haskell-mode),
which opinionatedly applies this style guide automatically.

I may write an automatic formatter with HSE to formalize this style at
some point.

## Indentation

Indent two spaces. No tabs.

## Line length

Prefer 80 columns, but don't waste your time re-working code just to
fit within it. There, 120 is acceptable too. Don't try to shoehorn
code that shouldn't be broken up (like long strings) onto multiple
lines. Let them be.

## Modules

Always document the module header:

``` haskell
-- | What this module does.

module Foo where
```

Put the `where` on the same line unless there are exports.

## Exports

If there are exports then write them like this, with the `where`
coming on the line after.

``` haskell
module Foo
  (a
  ,b
  ,c
  ) where
```

## Imports

Put imports one empty line after the module header. Always put imports
on separate lines, and sort them alphabetically:

``` haskell
module Foo where

import X
import Y
```

Always group import lists starting with your own project-local imports
first, because they are more important and fewer in number:

``` haskell
module Foo.Bar where

import Foo.Zot
import Foo.Bob

import Control.Monad
import Data.Text
import Data.List
import Data.Maybe
import System.IO
import System.Process
```

Write import lists like this:

``` haskell
import X (foo,bar)
```

And if they don't fit on one line, like this:

``` haskell
import X (foo
         ,bar)
```

But prefer that if you have more than one, instead use separate import
lines:

``` haskell
import X (foo)
import X (bar)
```

If you have many imports, prefer explicit qualification:

``` haskell
import qualified X as Z
```

Prefer to import types unqualified:

``` haskell
import qualified Data.Text as T
import Data.Text (Text)
```

Unless they overlap, in which case it will be `T.Text`.

## Declarations

All declarations should be surrounded by a blank line.

Always order declarations in the order that they're used, top-down:

``` haskell
main = foo bar

foo = bob

bar = zot
```

Always add type-signatures to top-level functions once they are
written, and document them:

``` haskell
-- | Main entry point.
main :: IO ()
main = foo bar

-- | Pretty print a name.
foo :: String -> IO ()
foo = bob

-- | Default dummy string.
bar :: String
bar = zot
```

## Functions

Prefer shorter functions to longer functions:

``` haskell
main =
  do foo bar (z * zz) …
     zot bob (z * zz) …
```

(Imagine longer code.)

Is improved by:

``` haskell
main =
  do openConnection
     makeCake

  where openConnection = foo bar y …
        makeCake = zot bob y …
        y = z * zz
```

This better documents what the function is doing.

When the code is larger, this is further improved by:

``` haskell
main =
  do openConnection
     makeCake

  where y = z * zz

openConnection y = foo bar y …

makeCake y = zot bob y …
```

Because decoupling allows for more code-reuse and easier to understand
context.

## Data types

Always layout sum types so that the `=` and `|` line up. Always
document data types. Always put parentheses around derivings.

``` haskell
-- | Lorem ipsum amet patate.
data Foo
  = X
  | Y
  | Z
  deriving (A)
```

Always layout non-sum record types like this, and document the fields.

``` haskell
-- | Some record type.
data Foo = Foo
  { fooBar :: X -- ^ Bar stuff.
  , fooMu  :: Y -- ^ Mu stuff.
  , fooZot :: Z -- ^ Zot stuff.
  }
  deriving (B)
```

## Expressions

Always indent the parent before the children:

``` haskell
parent child1 child2
```

Or:

``` haskell
parent child1
       child2
```

Or, when preferred, due to line length constraints:

``` haskell
parent
  child1
  child2
```

Never mix and match single-line versus multi-line:

``` haskell
parent child1 child2
       child3
       child4
```

Never dangle children undearneath and behind the parent:

``` haskell
  parent
child1
child2
```

The `=`, `->` syntactic sugar are exceptions to the rule because they
denote separation from the actual parent, not a parent of their own:

``` haskell
foo =
  bar

case x of
  Foo bar mu ->
    a b c

case y of
  X a
    | p x ->
      bob foo
    | otherwise ->
      gogo gadget

\x y ->
  z y
```

## Do-notation

Always treat the `do` as the parent:

``` haskell
len = do foo
         bar
         mu
```

or

``` haskell
len =
  do foo
     bar
     mu
```

Never use `(>>)` where `do` will do:

``` haskell
main = do bar
          mu
```

Not

``` haskell
main = bar >> mu
```

## Operators

Never use operators when a simple English name is provided:

``` haskell
len = fmap length getLine
demo = over _1 (+2) (5,4)
```

Not:

``` haskell
len = length <$> getline
demo = _1 -- whatever the lens operator is to do `over'
```

Never use `($)` to avoid parentheses:

``` haskell
len = foo $ bar mu zot
len = foo (bar mu zot)

fork = forkIO $ do go go
fork = forkIO (do go go)
```

Always space out multi-operator expressions:

``` haskell
foo = x y * z * y
```

If you can't resist using `($)`, never mix it with `(.)` like this:

``` haskell
foo = foo . bar . mu $ zot bob
```

Use parens:

``` haskell
foo = (foo . bar . mu) zot bob
```

## Composition

Prefer to use composition where functions are expected, not
intermediate expressions:

``` haskell
foo = (foo . bar . mu) zot bob
```

This is okay.

``` haskell
foo = meaning zot bob

  where meaning = foo . bar . mu
```

This is better.

## Where clauses

Always indent where clauses two spaces with an empty line before them:

``` haskell
main = go

  where go = print "Hello!"
```

Sometimes it's okay to also put a newline after the where and indent:

``` haskell
main = go

  where
    go = print "Hello!"
```

## Let expressions

Never use let-expressions in a right-hand-side:

``` haskell
main = let x = y
       in x
```

Instead prefer a `where`:

``` haskell
main = x

  where x = y
```

Write `let` expressions inside a `do` in a bottom-up style:

``` haskell
main = do a <- return 1
          let x = 123
              y = x * a
          print y
```

## Salvaging space

First, write your code on the same line as the parent:

``` haskell
foobarMu = parent child1
                  child2
```

But prefer to bring right-hand-sides (of `=`, `->`) down. Bring the
whole expression down:

``` haskell
fooBarMu =
  parent child1
         child2
```

If you need to save space, bring the children down:

``` haskell
fooBarMu =
  parent
    child1
    child2
```

## Collections

Write tuples, lists and records without commas:

``` haskell
(a,b,c)

[a,b,c]

{x=a,y=b,z=c}
```

Layout multi-line tuples, lists and records with prefix comma:

``` haskell
(a
,b
,c)

[a
,b
,c]

{x=a
,y=b
,z=c}
```

# If

*Always* line up `then` and `else` with the condition when laying out `if` on multiple
lines:

``` haskell
if x
   then y
   else x
```
