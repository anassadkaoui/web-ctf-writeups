CTFtime: https://ctftime.org/event/3309

---

**Starting Point**

When I first read the challenge description I broke it down:

- "Internal" meant it wasn't supposed to be public
- "Exposed" meant it was accessible when it shouldn't be
- "Or does it?" meant the restriction was flawed

**Reading the Page Source**

I looked at the HTML and asked questions about every element:

- `a {}` and `a:hover` both style the same element — one is default, one is on hover
- `<hr>` is a horizontal rule, a void element with no closing tag
- Key insight: something is only interesting if it carries your input to the server and the server acts on it unsafely

**Understanding the Attack Surface**

I went through every element and asked "can I hack this?":

- `<h1>`, `<div>`, `<style>`, `<p>` — just tell the browser how to display things, never go back to the server
- `<script>` tags — run locally in my browser, don't affect the server
- `<a href="/view?file=readme.txt">` — the only interesting one because it sends input to the server

**Understanding the URL Structure**

I questioned why the URL was `/view?file=readme.txt` instead of just `/readme.txt`:

- `/readme.txt` would serve a file directly with no logic in between
- `/view?file=readme.txt` means there is a program running in between
- `?` separates the endpoint from the parameters
- `&` separates multiple parameters

**Two-Way Hacking Realization**

I realized hacking is not one directional:

- I can hack the server by sending malicious input
- The server through other users can hack me via XSS
- It's not about who sends what, it's about where untrusted input ends up

**Reading the Files**

- `readme.txt` — just an introduction, confirmed it's a document viewer
- `notes.txt` — confirmed it's a CTF environment
- `about.txt` — critical hint: there is a file called `flag.txt` in a folder outside of public view

**Building the Mental Model**

The server likely does something like:

\```
open("public/" + your_input)
\```

So when you send `file=readme.txt` it becomes `open("public/readme.txt")`.

**Understanding Folder Navigation**

- `..` means go up one folder
- `../` is valid navigation, `..filename` is not

**My Prediction Errors**

- I wrote `..flag.txt` — missing the `/` separator, which makes it invalid
- I invented a folder name `/private/` that was never hinted at

**The Logic**

I needed the server to open:

\```
open("public/../flag.txt")
\```

Because `public/../` cancels out — `..` goes up from `public/` making it equivalent to `open("flag.txt")`.

**Result**

I constructed the correct `file` parameter, sent it to the server, and got the flag.

**What I Learned**

- Read everything carefully, question everything
- A vulnerability is the gap between what a developer intended and what the code actually allows
- Error messages are feedback not failures
- Reasoning beats guessing every time
- This vulnerability is called Path Traversal
