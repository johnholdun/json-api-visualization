# JSON API Visualizations

Visualizations for understanding the structure of a JSON API document.

## Docco source

A first-draft attempt using the stock output of [Docco] against an extended
version of the JSON API example, as seen on [the JSON API home page][home],
written in Literate Javascript.

To modify this example, edit index.js.md, then run this:

    docco --output . index.js.md
    mv index.js.html index.html

Docco will then re-generate the output.

[Docco]: http://jashkenas.github.io/docco/
[home]: http://jsonapi.org/