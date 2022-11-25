# Functionality of browser

 
The main function of a browser is to present the web resource you choose, by requesting it from the server and displaying it in the browser window. The resource is usually an HTML document, but may also be a PDF, image, or some other type of content. The location of the resource is specified by the user using a URI.
 

# High level components of a browser 

1.**user interface**  :  this includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser displays except the window where you see the requested page.

2. **The browser engine**: marshals actions between the UI and the rendering engine.

  
  

2. **The rendering engine** : responsible for displaying requested content. For example if the requested content is HTML, the rendering engine parses HTML and CSS, and displays the parsed content on the screen.

3.**Networking**: for network calls such as HTTP requests, using different implementations for different platforms behind a platform-independent interface.

4.**UI backend**: used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform specific. Underneath it uses operating system user interface methods.

5.**JavaScript interpreter**: Used to parse and execute JavaScript code.

 6. **Data storage**: This is a persistence layer. The browser may need to save all sorts of data locally, such as cookies. Browsers also support storage mechanisms such as localStorage, IndexedDB, WebSQL and FileSystem.

  
  
  
  

![](https://lh6.googleusercontent.com/_VclS-1WUMRJJPbtBlXkpTeBQB-wYrjHOqF7jpwIdGfu9MiIrMvPeCgjCj4gELewDJtmS8PHO7YoIxcEFuzYmIVT3a_zAvFp4-U3-mTsChddod49n8njbJE0pw4ApSaDsuN5z0Md-_NuFE3_r7F52um6CXjnPHHKJoeeVau8BsBzApHAThj8_BWpdCcL8Q)

  
# Rendering engine and its use.

The responsibility of the rendering engine is well… Rendering, that is display of the requested contents on the browser screen. By default the rendering engine can display HTML and XML documents and images. It can display other types of data via plug-ins or extension; for example, displaying PDF documents using a PDF viewer plug-in

The rendering engine will start getting the contents of the requested document from the networking layer. This will usually be done in 8kB chunks.


## Uses of rendering engine.
  
1. rendering engine can display HTML and XML documents and images

2. It can display other types of data via plug-ins or extension

3. The rendering engine will start parsing the HTML document and convert elements to DOM nodes in a tree

![](https://lh4.googleusercontent.com/T4lS8D0T46yO8i_GBC5Gy5aV6Mz4FffRm5xyk9jLjGWrBYs4LpQbJ3JYvSraAqTytbiDxoG7jc6P87uU8Aeaow8BdV5Ht31GxjeyG1zQmwgo-y7GqYAjc2Q4cDrnCfFtuRMwqSBfiqIqs5K-GJrB1rqC-W_0WhHmCbiTd1lPSo7cF5lK-aE6D_YRkItpUQ)


# Parsing.

Parsing a document means translating it to a structure the code can use. The result of parsing is usually a tree of nodes that represent the structure of the document.

Parsing is based on the syntax rules the document obeys: the language or format it was written in. Every format you can parse must have deterministic grammar consisting of vocabulary and syntax rules.

Parsing can be separated into two sub processes: lexical analysis and syntax analysis.

The engine will parse the style data, both in external CSS files and in style elements. Styling information together with visual instructions in the HTML will be used to create another tree that is the render tree.

There are two types of parsers: top down parsers and bottom up parsers. An intuitive explanation is that top down parsers examine the high level structure of the syntax and try to find a rule match. Bottom up parsers start with the input and gradually transform it into the syntax rules, starting from the low level rules until high level rules are met.

![](https://lh3.googleusercontent.com/LbfGVb3JKGj5XGZ39z2ZZslQRkozgpeXAj_oiU-wQKf8_P_5empDRLw3nvIq6Y6XRdTyxzYD8oiwE2MwkTf5KimzCh3goJc1w8_6kt_ZuOR-ey9K4BR8nSEKn8MPqny8eyd19JFH0DgN4asao85Pt7UY9sdIHIjsAB7vePrsIRM0L9XbDDNK10Ku5dV1yw)

  
  

# Script.
 
The model of the web is synchronous. Authors expect scripts to be parsed and executed immediately when the parser reaches a <script> tag. The parsing of the document halts until the script has been executed. If the script is external then the resource must first be fetched from the network - this is also done synchronously, and parsing halts until the resource is fetched. This was the model for many years and is also specified in HTML4 and 5 specifications. Authors can add the "defer" attribute to a script, in which case it will not halt document parsing and will execute after the document is parsed. HTML5 adds an option to mark the script as asynchronous so it will be parsed and executed by a different thread.  
  

# Tree construction.

While the DOM tree is being constructed, the browser constructs another tree, the render tree. This tree is of visual elements in the order in which they will be displayed. It is the visual representation of the document. The purpose of this tree is to enable painting the contents in their correct order.


# Layout.

Layout is a recursive process. It begins at the root renderer, which corresponds to the <html> element of the HTML document. Layout continues recursively through some or all of the frame hierarchy, computing geometric information for each renderer that requires it.

When the renderer is created and added to the tree, it does not have a position and size. Calculating these values is called layout or reflow.

HTML uses a flow based layout model, meaning that most of the time it is possible to compute the geometry in a single pass. Elements later "in the flow" typically do not affect the geometry of elements that are earlier "in the flow", so layout can proceed left-to-right, top-to-bottom through the document.


## Global and incremental layout.

Layout can be triggered on the entire render tree - this is "global" layout. This can happen as a result of:

1.  A global style change that affects all renderers, like a font size change.
2.  As a result of a screen being resized
   
Layout can be incremental, only the dirty renderers will be laid out

## Asynchronous and Synchronous layout.

  

Incremental layout is done asynchronously. Firefox queues "reflow commands" for incremental layouts and a scheduler triggers batch execution of these commands.

## The layout process

The layout usually has the following pattern:

1.  Parent renderer determines its own width.
   2.  Parent goes over children and:
-  Place the child renderer (sets its x and y).
    
-  Call the child layout if needed - they are dirty or we are in a global layout, or for some other reason - which calculates the child's height.
    
4.  Parent uses children's accumulative heights and the heights of margins and padding to set its own height - this will be used by the parent renderer's parent.
    
5.  Sets its dirty bit to false.

# Painting.

In the painting stage, the render tree is traversed and the renderer's "paint()" method is called to display content on the screen. Painting uses the UI infrastructure component.
 
## Global and Incremental

Like layout, painting can also be global - the entire tree is painted - or incremental. In incremental painting, some of the renderers change in a way that does not affect the entire tree. The changed renderer invalidates its rectangle on the screen. This causes the OS to see it as a "dirty region" and generate a "paint" event. The OS does it cleverly and coalesces several regions into one. In Chrome it is more complicated because the renderer is in a different process then the main process. Chrome simulates the OS behavior to some extent. The presentation listens to these events and delegates the message to the render root. The tree is traversed until the relevant renderer is reached. It will repaint itself

## The painting order

1.  background color
2.  background image
3.  border
4.  children  
5.  outline