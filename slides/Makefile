.PHONY=live
THEME=white
REVEALJS_URL=https://cdnjs.cloudflare.com/ajax/libs/reveal.js/3.2.0

all: docker-2016-02-29.html

live: docker-2016-02-29.live

%.live: %.md
	reveal-md -t $(THEME) $^

clean:
	rm -f *.html

%.html: %.md
	pandoc -t revealjs -V theme:$(THEME) -V revealjs-url:$(REVEALJS_URL) -s $^ -o $@
