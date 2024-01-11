This repo was forked to update the javascript libraries and embed the custom JS instead of linking to the writers webserver.

Full attribution goes to cs905s!!


# Embedding Markdown in a Blogger HTML page.
This originally was on the blog at https://js-react.blogspot.com/2017/01/using-markdown-in-blogger.html.

[Markdown](http://daringfireball.net/projects/markdown/) has become very popular due to its simplicity and its wide adoption in Github and other developer websites. So when I was looking at Blogger to start a blog, I wanted the same easy syntax. I had expected that Blogger would support Markdown natively. Alas it does not. 

I found [Francis's blog](http://blog.chukhang.com/2011/09/markdown-in-blogger.html) that explained on how to do this on the client side (no server needed!). Unfortunately it was written a few years ago and there was no updated docs on the steps to do it. So I rolled up my sleeves and took the same approach but using the latest versions of CDN hosted [jQuery](http://www.jquery.com), highlightJS (https://highlightjs.org) and [showDown](https://github.com/showdownjs) libraries. 

jQuery had the nice bonus of cleaning up the previous code by abstracting away the DOM manipulation.

**Caveat - your post shows up as Markdown in RSS feeds.**

## Adding markdown to your Blogger

   1. Open your template, by clicking the "Template" menu item. This displays the current template. Click Edit to modify the template. At the top of your template, add the following tags just before the &lt;/head&gt; tag.
```html
    <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.9.0/styles/default.min.css"/>
```

   2. At the bottom of the template, add the following tags just before the </html> tag.
```html
	<link href='https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/default.min.css' rel='stylesheet'/>
	<script src='https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js' type='text/javascript'/>
	<script src='https://cdnjs.cloudflare.com/ajax/libs/showdown/2.1.0/showdown.min.js' type='text/javascript'/>
	<script src='https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js' type='text/javascript'/>
	<script type='text/javascript'>
      //
      // markdown-highlight-in-blogger.js -- javascript for using Markdown in Blogger
      // Based on Francis Tang&#39;s http://blog.chukhang.com/2011/09/markdown-in-blogger.html
      // Copyright (c) 2017 Divya van Mahajan
      //
      // Redistributable under a BSD-style open source license.
      // Documentation: https://github.com/cs905s/md-in-blogger
      //

      // namespace
      var MarkdownHighlightInBlogger = {};
      // From http://erlend.oftedal.no/blog/?blogid=14
      MarkdownHighlightInBlogger.unescapeHTML = function (html) {
        var htmlNode = document.createElement(&quot;DIV&quot;);
        htmlNode.innerHTML = html;
        if(htmlNode.innerText !== undefined)
          return htmlNode.innerText; // IE
        return htmlNode.textContent; // FF
      };

      MarkdownHighlightInBlogger.convertMD = function () {
        try {

          console.info(&#39;Converting markdown using jQuery&#39;);

          // showdown renderer
          var converter = new showdown.Converter({});
          converter.setFlavor(&#39;github&#39;);
          $(&#39;pre.markdown&#39;).each(function (i, block) {
            //var rawtext = MarkdownHighlightInBlogger.unescapeHTML(block.innerText);
            var rawtext = block.innerText;
            var md_html = converter.makeHtml(rawtext);
            var md = $(md_html); //.css(&#39;border&#39;,&#39;3px solid blue&#39;);
            md.insertBefore(block);
            block.hidden = true;
          });
          $(&#39;pre code&#39;).each(function (i, block) {
            hljs.highlightElement(block);
          });
        } catch (exc) {
          console.error(exc);
        }
      };

      $(document).ready(MarkdownHighlightInBlogger.convertMD);
    </script>
```

   3. Save your template. The first three steps need to be done only once. If you have a mobile template, remember to adjust that template as well.

   4. Edit your post and switch from compose to HTML mode (buttons above the editor). Try out this sample.
```html
      <pre class="markdown">
      #Woo hoo!
      [Markdown](http://daringfireball.net/projects/markdown/) is cool!
      ```javascript
          // Verify code highlighting
          if (today==rainy) {
            return false;
          }
      ```
      </pre>
```
   5. Any HTML **pre** tags with the class **markdown** are converted by showdown. After this any HTML code tags in the generated HTML are highlighted by highlightJS. 

   6. Remember to escape your &amp; with &amp;amp;, < with &amp;lt; and > with &amp;gt;.

   6. Save your post. Preview or Publish the post.
   7. Now go and start [mastering Markdown at Github](https://guides.github.com/features/mastering-markdown/)
   8. If you don't have a Markdown editor, use [StackEdit](https://stackedit.io/editor) for a browser Markdown editor.
## How it works.

   1. Finds `pre` elements marked with `class='markdown'` with jQuery.  
   2. Showdown converts the markdown text content into html and injects
      that back into the post before the pre (and hides the pre).
   3. Each code section in the converted HTML is color highlighted by HighlightJS.


I have set the showdown convertor to to GitHub flavor markdown. 
The libraries are hosted by the kind folks at [cdnjs](http://cdnjs.com). The core javascript is hosted on the [surge.sh](http://surge.sh) CDN.

