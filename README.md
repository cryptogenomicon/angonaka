# angonaka: a pelican theme

This is the [Pelican](https://blog.getpelican.com/) theme that I use
for static web sites including my course
[mcb112.org](http://mcb112.org) and my
blog [cryptogenomicon.org](http://cryptogenomicon.org). I derived it
from an excellent theme called
[Astrochelys](https://out-of-cheese-error.netlify.app/astrochelys).
(_Astrochelys_ is a genus of Madagascar tortoises. The Angonaka tortoise
is an _Astrochelys_ species.)

Among the features I wanted were:

 * support for an all-static-Markdown-pages site, not only a blog
 * MathJax, because I use a lot of math notation in Markdown
 * automated table of contents for Markdown pages
 * figures with captions
 * marginal notes, a la Tufte
 * elegant code syntax highlighting
 * pages print well and easily, without sidebar 


## installing dependencies

The theme depends on `webassets` and `cssmin` optional Python modules,
and uses the Pelican `render_math`, `assets`, and `pelican-toc` plugins.

```
cd ~/alien-src
git clone --recursive https://github.com/getpelican/pelican-plugins
pip install webassets
pip install cssmin
```

(Looks like they've reorganized pelican-plugins, and now render-math
is at https://github.com/pelican-plugins/render-math. Consider
switching?)




## initial setup of a new site

```
mkdir mysite; cd mysite
pelican-quickstart            # when it asks for time zone: America/New_York
git clone https://github.com/cryptogenomicon/angonaka 
```

This creates `content/` and `output/` directories and the
`pelicanconf.py` config file, and clones the angonaka theme.

Add to `pelicanconf.py`:

```
THEME        = "./angonaka"
PLUGIN_PATHS = [ '/Users/seddy/alien-src/pelican-plugins' ]
PLUGINS      = [ 'assets', 'pelican-toc', 'render_math' ]    

TOC = { 'TOC_INCLUDE_TITLE': 'false' }

TAG_SAVE_AS = ''
CATEGORY_SAVE_AS = ''
```

The `render_math` plugin enables MathJax. Now $e=mc^2$ is an inline
equation, and $$e=mc^2$$ is a block equation.

### mirroring content hierarchy to output hierarchy

Pelican doesn't deal with subdirectory structure well by default. To
get it so you can organize Markdown pages in a directory hierarchy,
and have it maintain that hierarchy in the HTML output pages, add
something like this to `pelicanconf.py`:

```
   PAGE_PATHS   = [ 'pages',  'w00', 'w01' ]
   STATIC_PATHS = [ 'images', 'w00', 'w01' ]

   PATH_METADATA= '(?P<path_no_ext>.*)\..*'
   PAGE_SAVE_AS= '{path_no_ext}.html'
   PAGE_URL= '{path_no_ext}.html'
```

(If you also want articles to stay in their hierarchy, ditto for
`ARTICLE_SAVE_AS` and `ARTICLE_URL`.)

`PAGE_PATHS` is a list of directories that .md files will be found in.
In MCB112, it'll be a list of all the directories w00-w13.

`STATIC_PATHS` is a list of the same directories, where other files
will be found in, such as images. They'll be copied verbatim into the
appropriate output directories.

I don't understand how the `PATH_METADATA` trick works but it's commonly
used in pelican. 

* https://github.com/getpelican/pelican/issues/2673
* https://github.com/getpelican/pelican/issues/686#issuecomment-48603981



### main pages

For home page `content/pages/index.md`, add a `save_as` directive to
the Markdown metadata to get Pelican to install it in the root
directory `output/` instead of in `output/pages/`:

```
Title:    MCB112 Biological Data Analysis
save_as:  index.html
```

### sidebar customization symlinks

The base template in `templates/base.html` lays out the sidebar. It
includes two files that customize the sidebar for a particular site:
`extra-navbar.html` and `extra-sidebar.html`.  These two files are not
in the git repository. You need to create them, but they can be empty.

If you want to version your customizations, name your customized files
something site specific (like `mcb112-navbar.html` and
`mcb112-schedule.html`) and make `extra-navbar.html` and
`extra-sidebar.html` symlink to them. The ideas is to allow the git
repo to contain different site customizations, without making the repo
site-specific. If you don't need to version your customizations, then
just make `extra-navbar.html` and `extra-sidebar.html` files.



### running test site

```
pelican content  # compiles stuff in content/ to new site in output/
pelican -rl      # runs test server, with autoreloading, at  http://localhost:8000/ 
```   




## marginal notes: citation, aside

There are two types of marginal notes. A `citation` is numbered,
including a superscripted reference in the main text. An `aside` 
is unnumbered. The Astrochelys theme calls these `sidenote` and
`marginnote`. 

To insert a citation (with a number counter reference), we have to
use a little raw HTML in the Markdown:

```
<label for="unique-id" class="cite-toggle citation-number"></label>
<input type="checkbox" id="unique-id" class="citation-toggle"/>
<span class="citation">
This is a citation, with a counter.
</span>
```

The tag `"unique-id"` needs to be unique for each citation. This is
used on mobile screens to toggle an inline version of the citation on
or off (see below). 

To insert an aside:

```
<span class="aside">
And this is an aside.
</span>
```

### using asides as figure captions

These notes are aligned to the baseline of the text where they were
inserted. Sometimes you need to adjust this, like when you use a
marginnote as a caption for an inlined figure. You can use a
`margin-top` attribute to move the note down:

```
<span class="aside" style="margin-top:10em">
This is a caption.
</span>
<img src="fig1.png">
```



### mobile screens

On mobile screens, we don't have a margin, so both kinds of notes need
to be displayed in-line in the main text. 

Citations are clickable. Clicking on the cite superscript toggles the
citation, and causes it to appear or disappear in-line.

Asides are displayed in-line constitutively. 

### emacs customizations

In my `.emacs`, I have Emacs macros for inserting a `citation` and
`aside`, bound to `M-"` and `M-'`. The citation macro generates a
random 16-character id tag.

My `.emacs` also customizes `markdown-mode` to deemphasize this HTML
code, using font-lock to color this code grey.




## other CSS customizations


### fonts

To get fonts from Google Fonts, change line in `angonaka/template/base.html`:

```
    <link href="https://fonts.googleapis.com/css?family=Inconsolata:400,700|Open+Sans+Condensed|Open+Sans:400,400i,700&display=swap" rel="stylesheet">
```

The `700` gets bold; `400i` gets normal italic. Then in `angonaka.css`:

```
:root {
  --fixed-font: "Inconsolata";
  --variable-font: "Open Sans";
  --condensed-font: "Open Sans Condensed";
}
```


### colors

Some Tufteian color choices (https://urbaninstitute.github.io/graphics-styleguide/)

Dark mode:
```
#282c34  dark grey     # sidebar background
#f0f0ed  subtle grey   # sidebar text
#e8832b  orange        # sidebar links
```

Light mode:
```
#ffffff  white         # default background  
#f0f0ed  subtle grey   # alt background for code, blockquote
#000000  black         # most text
#12719e  blue          # anchors, horizontal rules
```

Not used:
```
#ca5800  orange
#a4201d  red
#55b748  green
#5c5859  dark gray
```


### block quotes

I simplified to:

```
blockquote{
  font-size: 1em;
  width: 95%;
  margin: 1em auto;
  font-family: inherit;
  color: var(--darkest-color);
  padding: 0.1em 0em 0.1em 2em;
  border-left: 2px solid var(--highlight-color-2) ;
  position: relative;
  background:var(--lighter-color);
}
```

### code highlighting

pygments wasn't working properly, at least on C code, until I
specified C language in my markdown: after the triple quote, add a
language keyword.

I made my own `pygments.css`.

To generate one of the standard pygments CSS files:

```
cd astrochelys/static/css
mv pygments.css pygments.css.orig
pygmentize -S native -f html -a .highlight > pygments.css 
```


### suppressing printing of sidebar/phone-header in PDF

Worked on making sure I can print PDFs of notes. Had to work on
getting rid of the sidebar (including a mobile header), while getting
the margin to print.

I added this to the CSS file:

```
@media print {
     .content {
        padding: 1em 3.5em 0 3.5em;
        margin-left: 0%;
        width: 70%;
     }
    .sidebar      { display: none  }
    .phone-header { display: none  }
    .citation     { display: block }
    .aside        { display: block }
}
```

I tweaked some other `@media screen` CSS too.

### disabled Hypothesis

upper right has:
  - toggle or resize sidebar
  - toggle highlight visibility
  - New Page Note
  - is this annotation? comments?

This is something called Hypothesis. I disabled it by removing a
couple of lines in the `base.html` template.


## See also

  * Tufte CSS   https://edwardtufte.github.io/tufte-css/  
  * Caches to Caches  http://cachestocaches.com/    has nice margin notes
  * Codepen    https://codepen.io/cliftwalker/pen/XJaEXY  example of clean blockquote
  * https://kennethfriedman.org/thoughts/2019/marginal-notes/ has
    clean CSS for marginal notes, including adaptation to mobile screens.
  * https://www.gimp.org/fr/about/meta/using-pelican/
  * https://www.gimp.org/fr/about/meta/wtf-pelican.html
  * https://dirtyhandscoding.github.io/posts/blog-migrated-to-pelican-and-github-pages.html
  * https://github.com/akhayyat/pelican-page-hierarchy


### my internal crossreferences

 * [2020/0422-pelican]
 * [2020/0428-astrochelys-theme]
 * [2020/0808-pelican]
 * [2020/1228-angonaka-marginnotes] Renamed sidenote and marginnote to
   citation, aside. Fixed bug in sidenote/citation numbering in
   pages. Added .emacs customizations with M-", M-' key bindings to
   macros for inserting citation, aside; and for using font-lock
   to color and deemphasize their HTML in markdown-mode.
