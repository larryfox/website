---
title: Footnotes & Highlights in Siteleaf
date: 2013-12-18 00:00:00 Z
hidden: true
---

Today we added support for footnotes and highlights to our bag of markdown tricks. Footnotes are great for citing and referencing sources, or for expanding on ideas. They are created like this:

~~~ json
A cool sentence[^1].

[^1]: A cool footnote.
~~~

And get rendered like this:

~~~ html
<p>A cool sentence<sup id="fnref1">
    <a href="#fn1" rel="footnote">1</a>
</sup>.</p>
<div class="footnotes">
    <hr>
    <ol>
        <li id="fn1">
            <p>A cool footnote. <a href="#fnref1" rev="footnote">↩</a></p>
        </li>
    </ol>
</div>
~~~

Highlighting is useful when you'd like to direct the readers attention somewhere specific. To use it, wrap a phrase in `==double equalls signs==`. Highlights simply get wrapped in `<mark>` tags. And remember, “with great power comes great responsibility.”
