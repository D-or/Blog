---
title: Sass VS Less
---

>   摘自 [CSS-TRICKS](https://css-tricks.com/sass-vs-less/)

"Which CSS preprocessor language should I choose?"* is a hot topic lately. I've been asked in person several times and an online debate has been popping up every few days it seems. It's nice that the conversation has largely turned from whether or not preprocessing is a good idea to which one language is best. Let's do this thing.

Really short answer: Sass

Slightly longer answer: Sass is better on a whole bunch of different fronts, but if you are already happy in LESS, that's cool, at least you are doing yourself a favor by preprocessing.

Much longer answer: Read on.

### [#](https://css-tricks.com/sass-vs-less/#article-header-id-0)The Much Longer Answer

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-1)The Learning Curve with Ruby and Command Line and Whatever

The only learning curve is the syntax. You should use an app like [CodeKit](http://incident57.com/codekit/), [LiveReload](http://livereload.com/), or [Mixture](http://mixture.io/) to watch and compile your authored files. You need to know jack squat about Ruby or the Command Line or whatever else. Maybe you should, but you don't have to, so it's not a factor here. The fact that Sass is in Ruby and LESS is in JavaScript is of little consequence to most potential users.

Winner: Nobody

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-2)Helping with CSS3

With either language, you can write your own mixins to help with vendor prefixes. No winner there. But you know how you don't go back and update the prefixes you use on all your projects? (You don't.) You also won't update your handcrafted mixins file. (Probably.)

In Sass, you can use [Compass](http://compass-style.org/), and Compass **will** keep itself updated, and thus the prefix situation is handled for you. [Bourbon](http://bourbon.io/) is also good. There will be some back and forth on which of these project is "ahead."

In LESS, there are also some mixin libraries [battling to be the best](http://designshack.net/articles/css/battle-of-the-less-mixin-libraries-less-elements-vs-less-hat-vs-bootstrap/). They are looking a lot better these days than they have in the past. Despite their marketing-y support charts, I don't think they are quite as robust as the Sass versions. I've been led to understand in the past that the language of LESS itself doesn't make it possible to build as robust of libraries on top of it. We'll get to some of that next.

In both cases, the onus is on you to keep the preprocessor software itself up to date as well as these libraries. I also find that easier in Sass in general. For instance, Compass updates will just come automatically in CodeKit, or you use a Gem which is easy to update, while LESS mixins you'll have to manually update a file yourself.

Winner: Narrowly Sass

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-3)Language Ability: Logic / Loops

LESS has an ability to do "guarded mixins." These are mixins that only take affect `when` a certain condition is true. Perhaps you want to set a background color based on the current text color in a module. If the text color is "pretty light" you'll probably want a dark background. If it's "pretty dark" you'll want a light background. So you have a single mixin broke into two parts with these guards that ensure that only one of them takes effect.

```less
.set-bg-color (@text-color) when (lightness(@text-color) >= 50%) { 
  background: black;
}
.set-bg-color (@text-color) when (lightness(@text-color) < 50%) { 
  background: #ccc;
}
```

So then when you use it, you'll get the correct background:

```less
.box-1 {
  color: #BADA55;
  .set-bg-color(#BADA55);
}
```

That is overly simplified, but you likely get the idea. You can do some fancy stuff with it. LESS can also do self-referencing recursion where a mixin can call itself with an updated value creating a loop.

```less
.loop (@index) when (@index > 0) {
  .myclass {
    z-index: @index;
  }
  // Call itself
  .loopingClass(@index - 1);
}
// Stop loop
.loopingClass (0) {}

// Outputs stuff
.loopingClass (10);
```

But thats where the logic/looping abilities of LESS end. Sass has actual logical and looping operators in the language. if/then/else statements, for loops, while loops, and each loops. No tricks, just proper programming. While guarded mixins are a pretty cool, natural concept, language robustness goes to Sass. This language robustness is what makes Compass possible.

For example, Compass has a mixin called `background`. It's so robust, that you can pass just about whatever you want to that thing that it will output what you need. Images, gradients, and any combination of them comma-separated, and you'll get what you need (vendor prefixes and all).

This succinct and intelligible code:

```scss
.bam {
  @include background(
    image-url("foo.png"),
    linear-gradient(top left, #333, #0c0),
    radial-gradient(#c00, #fff 100px)
  );
}
```

Turns into this monster (which is unfortunately what we need for it to work with as good of browser support as we can get):

```css
.bam {
  background: url('https://cdn.css-tricks.com/foo.png'), -webkit-gradient(linear, 0% 0%, 100% 100%, color-stop(0%, #333333), color-stop(100%, #00cc00)), -webkit-gradient(radial, 50% 50%, 0, 50% 50%, 100, color-stop(0%, #cc0000), color-stop(100%, #ffffff));
  background: url('https://cdn.css-tricks.com/foo.png'), -webkit-linear-gradient(top left, #333333, #00cc00), -webkit-radial-gradient(#cc0000, #ffffff 100px);
  background: url('https://cdn.css-tricks.com/foo.png'), -moz-linear-gradient(top left, #333333, #00cc00), -moz-radial-gradient(#cc0000, #ffffff 100px);
  background: url('https://cdn.css-tricks.com/foo.png'), -o-linear-gradient(top left, #333333, #00cc00), -o-radial-gradient(#cc0000, #ffffff 100px);
  background: url('https://cdn.css-tricks.com/foo.png'), -ms-linear-gradient(top left, #333333, #00cc00), -ms-radial-gradient(#cc0000, #ffffff 100px);
  background: url('https://cdn.css-tricks.com/foo.png'), linear-gradient(top left, #333333, #00cc00), radial-gradient(#cc0000, #ffffff 100px);
}
```

Winner: Sass

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-4)Website Niceitude

LESS has a nicer, [more usable website](http://lesscss.org/). The [Sass documentation](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html) isn't awful. It's complete and you can find what you need. But when competing for attention from front end people, LESS has the edge. I don't doubt this plays a large role in LESS currently winning the popularity race.

I know that the Sass website is undergoing a major overhaul and lots of awesome people are working on it. It seems to me that it's going very slowly though.

Winner: LESS

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-5)The @extend Concept

Say you declare a class which has a bit of styling. Then you want another class which you want to do just about the same thing, only a few additional things. In LESS you'd likely:

```less
.module-b {
   .module-a(); /* Copies everything from .module-a down here */
   border: 1px solid red;
}
```

That's an "include" essentially. A mixin, in both languages. You could use an include to do that Sass as well, but you're better off using `@extend`. With `@extend`, the styles from `.module-a` aren't just duplicated down in .mobule-b (what could be considered bloat), the selector for .module-a is altered to `.module-a, .module-b` in the compiled CSS (which is more efficient).

```scss
.module-a {
   /* A bunch of stuff */
}
.module-b {
   /* Some unique styling */
   @extend .module-a;
}
```

Compiles into

```scss
.module-a, .module-b {
  /* A bunch of stuff */
}
.module-b {
  /* Some unique styling */
}
```

See that? It rewrites selectors, which is way more efficient.

In LESS, every single class is also a mixin, programmatically muddies the waters, but is easier to understand at first.

As of LESS 1.4, it also supports extend. You can see some examples [when we upgrade to it](http://blog.codepen.io/2013/06/25/less-1-4-and-more/) on CodePen. It's a bit funky in that it doesn't extend selectors nested in the original class unless you use an additional `all` keyword. Having the option to go either way seems like it's actually more powerful to me, but also wary of what's going on under the covers.

Sass also has the power to extend "placeholder" classes. Essentially invisible classes, in the format of `%placeholder { }`. This is useful for using internal naming that makes sense there but wouldn't as actual class names.

Winner: Sass

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-6)Variable Handling

LESS uses @, Sass uses $. The dollar sign has no inherit meaning in CSS, while the @ sign does. It's for things like declaring `@keyframes` or blocks of `@media`queries. You could chalk this one up to personal preference and not a big deal, but I think the edge here is for Sass which doesn't confuse standing concepts.

Sass has some weirdness with scope in variables though. If you overwrite a "global" variable "locally", the global variable takes on the local value. This just feels kind of weird.

```css
$color: black;           
.scoped { 
  $color: white;
  color: $color;        
}                        
.unscoped {     
  // LESS = black (global)
  // Sass = white (overwritten by local)
  color: $color;          
}
```

I've heard it can be useful but it's not intuitive, especially if you write JavaScript.

Winner: Tossup

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-7)Working with Media Queries

The way most of us started working with `@media` queries was adding blocks of them at the bottom of your main stylesheet. That works, but it leads to mental disconnect between the original styling and the responsive styles. Like:

```css
.some-class {
   /* Default styling */
}

/* Hundreds of lines of CSS */

@media (max-width: 800px) {
  .some-class {
    /* Responsive styles */
  }
}
```

With Sass or LESS, we can bring those styles together through nesting.

```scss
.some-class {
  /* Default styling */
  @media (max-width: 800px) {
    /* Responsive styles */
  }
}
```

You can get even sexier with Sass. There is a really cool "respond-to" technique (see code by [Chris Eppstein](https://gist.github.com/1215856), [Ben Schwarz](http://theint.ro/blogs/outro/4686992-responsive-design-with-sass), and [Jeff Croft](http://jeffcroft.com/blog/2012/mar/02/implementing-responsive-design/)) for naming/using breakpoints.

```scss
=respond-to($name)

  @if $name == small-screen
    @media only screen and (min-width: 320px)
      @content

  @if $name == large-screen
    @media only screen and (min-width: 800px)
      @content
```

The you can use them succinctly and semantically:

```scss
.column
    width: 25%
    +respond-to(small-screen)
      width: 100%
```

Nested media queries are a fantastic way to work. Rumor has it Sass 3.3 will have more features to make this even more useful, including a way to make extend work within media queries which is currently impossible in both LESS and Sass.

Winner: Sass

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-8)Math

For the most part, the math is similar, but there are some weirdnesses with how units are handled. For instance, LESS will assume the first unit you use is what you want out, ignoring further units.

```less
div {
   width: 100px + 2em; // == 102px (weird)
}
```

In Sass, you get a clear error: Incompatible units: 'em' and 'px'. I guess it's debatable if it's better to error or be wrong, but I'd personally rather have the error. Especially if you're dealing with variables rather than straight units and it's harder to track down.

Sass will also let you perform math on "unknown" units, making it a bit more futureproof should some new unit come along before they are able to update. LESS does not. There is some more weird differences like how Sass handles multiplying values that both have units, but it's esoteric enough to not be worth mentioning.

Winner: Narrowly Sass

#### [#](https://css-tricks.com/sass-vs-less/#article-header-id-9)Active Development

I'm going to update these numbers since it's been enough time since the original writing of this article.

|                                          | 05/16/12 | 01/12/13 | 06/25/13 |
| ---------------------------------------- | -------- | -------- | -------- |
| [Number of open issues](https://github.com/cloudhead/less.js/issues) on LESS | 392      | 112      | 142      |
| [Number of open issues](https://github.com/nex3/sass/issues) on Sass | 84       | 83       | 110      |
| [Pending pull requests](https://github.com/cloudhead/less.js/pulls) on LESS | 86       | 10       | 5        |
| [Pending pull requests](https://github.com/nex3/sass/pulls) on Sass | 3        | 7        | 11       |
| [Number of commits](https://github.com/cloudhead/less.js/commits/master/) in the last month in LESS | 11       | 84       | 2        |
| [Number of commits](https://github.com/nex3/sass/commits/stable/) in the last month in Sass | 35       | 14       | 14       |

None of that stuff is any definitive proof that one project is more active than the other, but it is interesting to look at stats. As I understand it, both of the leads work on the languages in whatever little free time they have, as they both have other major new projects they are working on.

LESS has been pretty actively lately, but now Sass has gotten much more active too, due to a core member getting to focus on it directly.

Winner: Tossup

### [#](https://css-tricks.com/sass-vs-less/#article-header-id-10)More Reading

-   Chris Eppstein: [Sass/LESS Comparison](https://gist.github.com/674726)
-   Jeremy Hixon: [An Introduction To LESS, And Comparison To Sass](http://coding.smashingmagazine.com/2011/09/09/an-introduction-to-less-and-comparison-to-sass/)
-   Ken Collins: [Too LESS? Should You Be Using Sass?](http://metaskills.net/2012/02/27/too-less-should-you-be-using-sass/)
-   Johnathan Croom: [Sass vs. LESS vs. Stylus: Preprocessor Shootout](http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/)