---
layout: post
title: How I use Anki to learn languages
category: languages
date: 2021-08-14
published: true
---

## Why I use Anki

The [spaced-repetition](https://en.wikipedia.org/wiki/Spaced_repetition) flashcard software Anki does not have the most modern interface or engaging design. Its mobile app on iOS is paid, costing $25, and if you would rather not pay that price, [AnkiWeb](https://ankiweb.net/) is free but far from a snappy native experience. The desktop app is clunkier still. I feel almost a little embarassed recommending it to casual learners, because it has a learning curve to it, and I suspect non-technical users have a hard time sticking with it compared to out-of-the-box learning sites/apps such as Memrise, Duolingo, and LingoDeer.

However, it does have one advantage that sets it leagues above everything else for me, which is that I trust it. Anki has never tried to upsell me to a paid package; it has never served me an advertisement; it has never tried to get me addicted to it; I am confident that it is not busy attempting to optimize some spurious engagement metrics at my expense; if I need to solve a problem or contort the program to serve my needs, I can pop open the hood and look at its [source code](https://github.com/ankitects/anki), its database schemas are [well-documented](https://www.juliensobczak.com/write/2016/12/26/anki-scripting.html) and can be manipulated either with sqlite3 or [Anki's add-on API](https://addon-docs.ankiweb.net/), and there is a [community](https://www.reddit.com/r/Anki) of users from all walks of life discussing Anki at all levels of complexity. The [privacy policy](https://ankiweb.net/account/privacy) and [terms of use](https://ankiweb.net/account/terms) are both mercifully short, largely free of legalese, unambiguous, and reasonable.

Not only this, but I expect almost all of these things to be true in perpetuity. AnkiWeb's free provision of storage, synchronization, and reviewing might one day come with a price tag due to the cost of providing the service, but I am otherwise confident that Anki will remain free as in beer and free as in speech. Contrast this with Duolingo, which recently [IPOed on the Nasdaq stock exchange](https://blog.duolingo.com/duolingo-ipo/). Being a VC-funded company and now a publicly traded one, Duolingo must seek growth and revenue to satisfy its investors, which can put them at odds with their users on subjects like privacy, cost, user experience, or learning quality.

I consolidate as much of my SRS review into Anki as I can. While I may use sites like Memrise for my initial acquitision of vocab and grammar in a language, I build an Anki deck in parallel while doing so.

## Storing and structuring cards

Any note that you create will generate one or more corresponding cards. Cloze-type notes, for instance, will generate as many cards as there are distinctly-numbered cloze fields.

Cards are stored in decks, which may be organized in hierarchies so that one deck is a subdeck of another. They may also be tagged, for easier searching, or for the creation of temporary study decks.

I use a flat deck hierarchy without subdecks. Rather, I use tags to organize subdecks.

## Querying effectively

Beyond its functionality for spaced repetition, I also use Anki as a rapidly searchable database for grammar, vocabulary, and sample sentences. One advantage of this is that it's essentially a dictionary I create myself, meaning that I can add any words that are missing from other dictionary services, as well as my own notes, usage examples, and so on.

## Keeping cards clean

When copying and pasting into Anki, particularly in AnkiWeb (which I use on mobile devices), undesirable elements of the source text's formatting may be preserved. Sometimes this means particularly large or small font sizes, or fixed text colors that are unreadable when the desktop app is set to dark mode, or no-break spaces that may have made sense in their original format but appear awkward in a flashcard.

Manually preserving a consistent and desirable card appearance is difficult. In the past, I used to manually search for cards that had custom styling in the Anki browser using the query `"style="`, and then manually use the shortcut `CTRL+R` on each of them to remove the formatting.

There is a better way to do this, with Anki hooks. I have written a Python script that now runs every time I sync my desktop Anki state with AnkiWeb that performs various text replacements on cards that match a given SQL query.

The source of that script is hosted [here](https://github.com/evanbubniak/anki-plugins/blob/main/keep_cards_tidy/__init__.py). Let's take note of the imports I use from Anki:

```python
from aqt import gui_hooks, mw
from aqt.utils import qconnect
from aqt.qt import QAction
```

and the end, after my declaration of `clean_deck`, where I set up the hook and add the function as a menu item:

```python
gui_hooks.sync_will_start.append(clean_deck)

if mw is not None:
    action = QAction("Reformat all cards", mw)
    qconnect(action.triggered, clean_deck)
    mw.form.menuTools.addAction(action)
```