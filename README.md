# How-browsers-work

##### Introduction
 The main function of a browser is to present the web resource you choose, by requesting it from a web server and displaying it in the browser window. The resource is usually an HTML document, PDF, image, or some other type of content. The location of the resource is specified by the user using a Uniform Resource Identifier .

Different browser user interfaces are simillar to another other. Among the common user interface elements are:

 1.  Address bar for inserting a URI
 2. Back and forward buttons
 3. Bookmarking options
 4. Refresh and stop buttons for refreshing or stopping the loading of current documents
 5. Home button that takes you to your home page
 
### How the browser Works

Inorder to understand the working of a browser one must understand the components of a web browser.A browser's main components are :

  1. The user interface: this includes the address bar, back/forward button, bookmarking menu and every part of the browser    display except the window where you see the requested page.
  2. The browser engine: marshals actions between the UI and the rendering engine.
  3. The rendering engine : responsible for displaying requested content. For example if the requested content is HTML, the rendering engine parses HTML and CSS, and displays the parsed content on the screen.
   4. Networking: for network calls such as HTTP requests, using different implementations for different platform behind a platform-independent interface.
   5. UI backend: used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform specific. Underneath it uses operating system user interface methods.
   6. JavaScript interpreter. Used to parse and execute JavaScript code.
    7. Data storage. This is a persistence layer. The browser may need to save all sorts of data locally, such as cookies. Browsers also support storage mechanisms such as localStorage, IndexedDB, WebSQL and FileSystem.
  
![alt text](http://arvindr21.github.io/howBrowserWorks/imgs/layers.png "How Browsers work1 Image")

The web browser works in the following steps:

  1. Resolve DNS
  2. Request Page
  3. Tokenize the response
  4. Parse HTML
  5. Build DOM tree
  6. Build Render tree
  7. Layout the Render tree
  8. Painting

Taking an indepth view of how browsers work.

### The rendering engine
Rendering,displays requested contents on the browser screen.
By default the rendering engine can display HTML and XML documents and images. It can display other types of data via plug-ins or extension.
Different browsers use different rendering engines: Internet Explorer uses Trident, Firefox uses Gecko, Safari uses WebKit. Chrome and Opera (Later versions) use Blink, a fork of WebKit.
The rendering engine will start getting the contents of the requested document from the networking layer. This will usually be done in 8kB chunks. 
This is the general workflow of the rendering engine
1. The rendering engine will start parsing the HTML document and convert elements to DOM nodes in a tree called the "content tree". The engine will parse the style data, both in external CSS files and in style elements. Styling information together with visual instructions in the HTML will be used to create another tree: the render tree. 
2. The render tree contains rectangles with visual attributes like color and dimensions. The rectangles are in the right order to be displayed on the screen.
3. After the construction of the render tree it goes through a layout process. This means giving each node the exact coordinates where it should appear on the screen.
4. The next stage is painting–the render tree will be traversed and each node will be painted using the UI backend layer. 
Graphical display of the steps.
![alt text](http://arvindr21.github.io/howBrowserWorks/imgs/flow2.png "How brosers work 2")

## Parsing
Parsing a document means translating it to a structure the code can use. The result of parsing is usually a tree of nodes that represent the structure of the document. This is called a parse tree or a syntax tree.
### HTML Parsing
The job of the HTML parser is to parse the HTML markup into a parse tree. 
###The tokenization algorithm
The algorithm's output is an HTML token. The algorithm is expressed as a state machine. Each state consumes one or more characters of the input stream and updates the next state according to those characters. The decision is influenced by the current tokenization state and by the tree construction state. This means the same consumed character will yield different results for the correct next state, depending on the current state. The algorithm is too complex to describe fully, so let's see a simple example that will help us understand the principle. 
Basic example–tokenizing the following HTML: 
<html>
  <body>
    Hello world
  </body>
</html>
 The initial state is the "Data state". When the < character is encountered, the state is changed to "Tag open state". Consuming an a-z character causes creation of a "Start tag token", the state is changed to "Tag name state". We stay in this state until the > character is consumed. Each character is appended to the new token name. In our case the created token is an html token.

When the > tag is reached, the current token is emitted and the state changes back to the "Data state". The <body> tag will be treated by the same steps. So far the html and body tags were emitted. We are now back at the "Data state". Consuming the H character of Hello world will cause creation and emitting of a character token, this goes on until the < of </body> is reached. We will emit a character token for each character of Hello world.

We are now back at the "Tag open state". Consuming the next input / will cause creation of an end tag token and a move to the "Tag name state". Again we stay in this state until we reach >.Then the new tag token will be emitted and we go back to the "Data state". The </html> input will be treated like the previous case. 

##CSS Parsing
The CSS Parser is implemented as a package of Java classes, that inputs Cascading Style Sheets source text and outputs a Document Object Model Level 2 Style tree. Alternatively, applications can use SAC: The Simple API for CSS. Its purpose is to allow developers working with Java to incorporate Cascading Style Sheet information, primarily in conjunction with XML application developments. 

##Layout
 When the renderer is created and added to the tree, it does not have a position and size. Calculating these values is called layout or reflow.

HTML uses a flow based layout model, meaning that most of the time it is possible to compute the geometry in a single pass. Elements later ``in the flow'' typically do not affect the geometry of elements that are earlier ``in the flow'', so layout can proceed left-to-right, top-to-bottom through the document. There are exceptions: for example, HTML tables may require more than one pass (3.5).

The coordinate system is relative to the root frame. Top and left coordinates are used.

Layout is a recursive process. It begins at the root renderer, which corresponds to the <html> element of the HTML document. Layout continues recursively through some or all of the frame hierarchy, computing geometric information for each renderer that requires it.
The position of the root renderer is 0,0 and its dimensions are the viewport–the visible part of the browser window.

All renderers have a "layout" or "reflow" method, each renderer invokes the layout method of its children that need layout. 

### Layout Process
 The layout usually has the following pattern:

  1. Parent renderer determines its own width.
  2.Parent goes over children and:
       1. Place the child renderer (sets its x and y).
      2.Calls child layout if needed–they are dirty or we are in a global layout, or for some other reason–which calculates the child's height.
   3. Parent uses children's accumulative heights and the heights of margins and padding to set its own height–this will be used by the parent renderer's parent.
    4.Sets its dirty bit to false.

Firefox uses a "state" object(nsHTMLReflowState) as a parameter to layout (termed "reflow"). Among others the state includes the parents width.
The output of the Firefox layout is a "metrics" object(nsHTMLReflowMetrics). It will contain the renderer computed heigh
##Painting
In the painting stage, the render tree is traversed and the renderer's "paint()" method is called to display content on the screen. Painting uses the UI infrastructure component. 
###The painting order
CSS2 defines the order of the painting process. This is actually the order in which the elements are stacked in the stacking contexts. This order affects painting since the stacks are painted from back to front. The stacking order of a block renderer is:

 1. background color
2.  background image
3.  border
4. children
5. outline
FInally all the contents are displayed in the UI or   the elements are prited on the screen.

#REFERENCES 
1. http://cssparser.sourceforge.net/
2. http://arvindr21.github.io/howBrowserWorks/#/37
3. http://www.vineetgupta.com/2010/11/how-browsers-work-part-1-architecture/
4. http://broadcast.oreilly.com/2009/05/the-bold-and-the-beautiful-two.html
5. http://dev.w3.org/html5/spec/Overview.html

