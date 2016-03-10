Slides in Markdown format for Docker Hackday orientation.

Slides can be converted to [Reveal.js](http://lab.hakim.se/reveal-js/#/) format if  [pandoc](http://pandoc.org) or [reveal-md](https://github.com/webpro/reveal-md) are installed. See [Makefile](Makefile) for details.

Build slides in static HTML format:

    $ make html

Run a Node.js server, which transforms Markdown slides into a Reveal.js presentation on-the-fly:

    $ make live

