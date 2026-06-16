Starting Point
When I first read the challenge description — "A company left their internal document viewer exposed. It only shows approved files... or does it?" — I didn't immediately know what it meant. I broke it down word by word:

"Internal" meant it wasn't supposed to be public
"Exposed" meant it was accessible when it shouldn't be
"Or does it?" meant the restriction was flawed

Reading the Page Source
I looked at the HTML and asked questions about every single element:

I asked whether a { color: blue } and a:hover both styled the same element — yes they do, one is default state, one is hover state
I asked what <hr> means — a horizontal rule, a void element with no closing tag
I asked whether elements are hackable or not — and I learned the key insight: something is only interesting if it carries your input to the server and the server acts on it unsafely

Understanding the Attack Surface
I went through every element and asked "can I hack this?" I realized:

<h1>, <div>, <style>, <p> — just tell the browser how to display things, never go back to the server
<script> tags — run locally in my browser, don't affect the server
<a href="/view?file=readme.txt"> — this was the only interesting one because it sends input to the server

Understanding the URL Structure
I questioned why the URL was /view?file=readme.txt instead of just /readme.txt. I learned:

/readme.txt would serve a file directly with no logic in between
/view?file=readme.txt means there is a program running in between that reads the file parameter and decides what to do
? separates the endpoint from the parameters
& separates multiple parameters

Two-Way Hacking Realization
I realized hacking is not one directional:

I can hack the server by sending malicious input
The server (through other users) can hack me via XSS
It's not about who sends what — it's about where untrusted input ends up and whether anything acts on it unsafely

Reading the Files
I read the three available files and extracted clues:

readme.txt — just an introduction, confirmed it's a document viewer
notes.txt — confirmed it's a CTF environment
about.txt — the critical hint: there is a file called flag.txt in a folder outside of public view

Building the Mental Model
I learned that the server likely does something like:
