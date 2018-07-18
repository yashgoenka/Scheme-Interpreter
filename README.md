# Scheme-Interpreter
Built an interpreter for a subset of the Scheme (LISP) language.  

Check the wiki on GitHub for more details.
<h2 id="data-types">Data Types</h2>



<h3 id="numbers">Numbers</h3>


<p>Numbers are built on top of Python's number types and can thus support a
combination of arbitrarily-large integers and double-precision floating points.
The web level should also support the same, though may deviate from
Python-based versions due to the different host language and the necessity to
work-around the quirks of JavaScript when running in a browser. Any valid
number literal in the interpreter's host language should be properly read.
You should not count on consistent results when floating point numbers are
involved in any calculation or on any numbers with true division.</p>


<h3 id="booleans">Booleans</h3>


<p>Booleans are built on the host language's boolean type. In Scheme, the boolean
<code>#f</code> is the only false value. All other values, including the boolean <code>#t</code>
are considered true. Scheme booleans may be inputted either as their
canonical <code>#t</code> or <code>#f</code> representations or as any capitalization of the words
"true" or "false". To ease implementation, an interpreter may output booleans
as any representation that is valid as input. The Python-based project and
staff interpreter will output booleans as <code>True</code> and <code>False</code> while the
Dart-based web interpreter will use <code>true</code> and <code>false</code>.</p>


<h3 id="symbols">Symbols</h3>


<p>Symbols are used as identifiers in Scheme. Valid symbols consist of only the
following characters:</p>

<pre><code>ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!$%&amp;*/:&lt;=&gt;?@^_~+&#x2d;.</code></pre>

<p>All symbols are case-insensitive and should be internally stored with lowercase
letters. Symbols must not form a valid integer or floating-point number.</p>


<h3 id="strings">Strings</h3>


<p>Unlike other implementations, 61A Scheme has no concept of characters. Strings
are considered primitive data types in their own right. Strings can be entered
into the intepreter as a sequence of characters inside double quotes, with
certain characters, such as line breaks and double quotes escaped. As a general
rule, if a piece of text would be valid as a JSON key, it should work as a
string in Scheme. Because the student and staff interpreters have little use
for strings, they lack proper support for their manipulation. The web
interpreter, which requires strings for JS interop (among other things), has
more extensive support, which is described in the <a href="http://scheme.cs61a.org/help/web-primitives.html">web primitives</a> document.</p>


<h3 id="pairs-and-lists">Pairs and Lists</h3>


<p>Pairs are a built-in data structure consisting of two fields, a <code>car</code> and a
<code>cdr</code>. Each of these two fields can contain any Scheme data type, including
another pair. Pairs can be constructed by passing the values for the two fields
as arguments to <code>cons</code>.</p>

<p><code>nil</code> is a special value in Scheme which represents the empty list. It can be
inputted by typing <code>nil</code> into the interpreter.</p>

<p>A <em>list</em> is defined as either <code>nil</code> or a pair whose <code>cdr</code> is another list. This
may also be referred to as a <em>well-formed list</em>.</p>

<p>Pairs are displayed in the form <code>(a . b)</code> where <code>a</code> is the <code>car</code> and <code>b</code> is the
<code>cdr</code>. If <code>b</code> is <code>nil</code>, it will be omitted along with the preceding dot, so a
pair constructed as <code>(cons 1 nil)</code> would be displayed as <code>(1)</code>. If <code>b</code> is
another pair, the dot preceding <code>b</code> and the parentheses wrapping around it are
omitted, so a list constructed as <code>(cons 1 (cons 2 nil))</code> would be displayed as
<code>(1 2)</code>.</p>


<h3 id="procedures">Procedures</h3>


<p>Procedures represent some subroutine within a Scheme program. Procedures are
first-class in Scheme, meaning that they can be bound to names and passed
around just like any other Scheme value.</p>

<p>Procedures can be called on some number of arguments, performing some number
of actions and then returning some Scheme value.</p>

<p>A procedure call can be performed with the syntax <code>(&lt;operator&gt; &lt;operand&gt; ...)</code>,
where <code>&lt;operator&gt;</code> is some expression that evaluates to a procedure and each
<code>&lt;operand&gt;</code> (of which there can be any number, including 0) evaluates to one of
the procedure's arguments.</p>

<p>There are several types of procedures. Primitive procedures (or just
primitives) are built-in to the interpreter and already bound to names when
it is started (though it is still possible for you to rebind these names).
The web interpreter includes a few primitives that act more like special
forms, as they do not evaluate the argument expressions. A list of the
primitives available in each interpreter is available in the
<a href="scheme-primitives.html">Scheme primitives</a> document.</p>

<p>Lambda procedures are defined using the <code>lambda</code> special form (see below) and
create a new frame whose parent is the frame in which the lambda was defined in
when called. The expressions in the lambda's body are than evaluated in this
new environment. Mu procedures are similar, but the new frame's parent is the
frame in which the <code>mu</code> is called, not the frame in which it was created.</p>

<p>The staff and web levels also contain macro procedures, which must be defined
with the <code>define&#x2d;macro</code> special form. Macros work similarly to lambdas, except
that they pass the argument expressions in the call expression into the macro
instead of the evaluated arguments and they then evaluate the expression the
macro returns in the calling environment afterwards.</p>

<p>The web level also contains JS procedures, which appear like primitives in
Scheme, but actually wrap a JavaScript function. Any time a JS function is
returned through JS interop, it will be wrapped as a JS procedure so it can
be called like any normal Scheme primitive.</p>


<h3 id="promises-and-streams">Promises and Streams</h3>


<p>Promises represent the delayed evaluation of an expression in an enviornment.
They can be constructed by passing an expression into the <code>delay</code> special form.
The evaluation of a promise can be forced by passing it into the <code>force</code>
primitive. The expression of a promise will only ever be evaluated once. The
first call of <code>force</code> will store the result, which will be immediately returned
on subsequent calls of <code>force</code> on the same promise.</p>

<p>Promises are used to define <em>streams</em>, which are to lists what promises are to
regular values. A stream is defined as a pair where the <em>cdr</em> is a promise that
evaluates to another stream or <code>nil</code>. The <code>cons&#x2d;stream</code> special form and the
<code>cdr&#x2d;stream</code> primitive are provided make the construction and manipulation of
streams easier. <code>(cons&#x2d;stream a b)</code> is equivalent to <code>(cons a (delay b))</code>
while <code>(cdr&#x2d;stream x)</code> is equivalent to <code>(force (cdr x))</code>.</p>


<h3 id="other-types">Other Types</h3>


<p>The web level also contains vectors, which are data structures containing a
fixed number of fields, similar to arrays in languages like Java or C, and
JS objects, which simply wrap real JavaScript objects received through interop.
More information about these is available in the <a href="http://scheme.cs61a.org/help/web-primitives.html">web primitives</a> and
<a href="http://scheme.cs61a.org/help/js-interop.html">JS interop</a> documents, respectively.</p>


<h2 id="special-forms">Special Forms</h2>


<p>In all of the syntax definitions below, <code>&lt;x&gt;</code> refers to a required element <code>x</code>
that can vary, while <code>[x]</code> refers to an optional element <code>x</code>. Ellipses
indicate that there can be more than one of the preceding element.</p>


<h3 id="student-level">Student Level</h3>


<p>The following special forms are included in all versions of 61A Scheme.</p>


<h4 id="define"><strong><code>define</code></strong></h4>


<pre><code>(define &lt;name&gt; &lt;expression&gt;)</code></pre>

<p>Evaluates <code>&lt;expression&gt;</code> and binds the value to <code>&lt;name&gt;</code> in the current
environment. <code>&lt;name&gt;</code> must be a valid Scheme symbol.</p>

<pre><code>(define (&lt;name&gt; [param] ...) &lt;body&gt; ...)</code></pre>

<p>Constructs a new lambda procedure with <code>param</code>s as its parameters and the <code>body</code>
expressions as its body and binds it to <code>name</code> in the current environment.
<code>name</code> must be a valid Scheme symbol. Each <code>param</code> must be a unique valid Scheme
symbol. At the staff level and above, <code>(&lt;name&gt; [param] ...)</code> can be dotted, with
a variable number of excess arguments bound as a list to the symbol after the
dot. This shortcut is equivalent to:</p>

<pre><code>(define &lt;name&gt; (lambda ([param] ...) &lt;body&gt; ...))</code></pre>

<p>However, some interpreters may give lambdas created using the shortcut an
intrinsic name of <code>name</code> for the purpose of visualization or debugging.</p>


<h4 id="if"><strong><code>if</code></strong></h4>


<pre><code>(if &lt;predicate&gt; &lt;consequent&gt; [alternative])</code></pre>

<p>Evaluates <code>predicate</code>. If true, the <code>consequent</code> is evaluated and returned.
Otherwise, the <code>alternative</code>, if it exists, is evaluated and returned (if no
<code>alternative</code> is present in this case, the return value is undefined).</p>


<h4 id="cond"><strong><code>cond</code></strong></h4>


<pre><code>(cond &lt;clause&gt; ...)</code></pre>

<p>Each <code>clause</code> may be of the following form:</p>

<pre><code>(&lt;test&gt; [expression] ...)</code></pre>

<p>The last <code>clause</code> may instead be of the form <code>(else [expression] ...)</code>, which
is equivalent to <code>(#t [expression] ...)</code>.</p>

<p>Starts with the first <code>clause</code>. Evaluates <code>test</code>. If true, evaluate the
<code>expression</code>s in order, returning the last one. If there are none, return what
<code>test</code> evaluated to instead. If <code>test</code> is false, proceed to the next <code>clause</code>.
If there are no more <code>clause</code>s, the return value is undefined.</p>


<h4 id="and"><strong><code>and</code></strong></h4>


<pre><code>(and [test] ...)</code></pre>

<p>Evaluate the <code>test</code>s in order, returning the first false value. If no <code>test</code>
is false and there are no more <code>test</code>s left, return <code>#t</code>.</p>


<h4 id="or"><strong><code>or</code></strong></h4>


<pre><code>(or [test] ...)</code></pre>

<p>Evaluate the <code>test</code>s in order, returning the first true value. If no <code>test</code>
is true and there are no more <code>test</code>s left, return <code>#f</code>.</p>


<h4 id="let"><strong><code>let</code></strong></h4>


<pre><code>(let ([binding] ...) &lt;body&gt; ...)</code></pre>

<p>Each <code>binding</code> is of the following form:</p>

<pre><code>(&lt;name&gt; &lt;expression&gt;)</code></pre>

<p>First, the <code>expression</code> of each <code>binding</code> is evaluated in the current frame.
Next, a new frame that extends the current environment is created and each
<code>name</code> is bound to its corresponding evaluated <code>expression</code> in it.</p>

<p>Finally the <code>body</code> expressions are evaluated in order, returning the evaluated
last one.</p>


<h4 id="begin"><strong><code>begin</code></strong></h4>


<pre><code>(begin &lt;expression&gt; ...)</code></pre>

<p>Evaluates each <code>expression</code> in order in the current environment, returning the
evaluated last one.</p>


<h4 id="lambda"><strong><code>lambda</code></strong></h4>


<pre><code>(lambda ([param] ...) &lt;body&gt; ...)</code></pre>

<p>Creates a new lambda with <code>param</code>s as its parameters and the <code>body</code> expressions
as its body. When the procedure this form creates is called, the call frame
will extend the environment this lambda was defined in.</p>


<h4 id="mu"><strong><code>mu</code></strong></h4>


<pre><code>(mu ([param] ...) &lt;body&gt; ...)</code></pre>

<p>Creates a new mu procedure with <code>param</code>s as its parameters and the <code>body</code>
expressions as its body. When the procedure this form creates is called, the
call frame will extend the environment the mu is called in.</p>


<h4 id="quote"><strong><code>quote</code></strong></h4>


<pre><code>(quote &lt;expression&gt;)</code></pre>

<p>Returns the literal <code>expression</code> without evaluating it.</p>

<p><code>&#x27;&lt;expression&gt;</code> is equivalent to the above form.</p>


<h4 id="delay"><strong><code>delay</code></strong></h4>


<pre><code>(delay &lt;expression&gt;)</code></pre>

<p>Returns a promise of <code>expression</code> to be evaluated in the current environment.</p>


<h4 id="cons-x2d-stream"><strong><code>cons&#x2d;stream</code></strong></h4>


<pre><code>(cons&#x2d;stream &lt;first&gt; &lt;rest&gt;)</code></pre>

<p>Shorthand for <code>(cons &lt;first&gt; (delay &lt;rest&gt;))</code>.</p>


<h3 id="staff-level">Staff Level</h3>


<p>The staff level includes all of the special forms above, plus these.</p>


<h4 id="define-x2d-macro"><strong><code>define&#x2d;macro</code></strong></h4>


<pre><code>(define&#x2d;macro (&lt;name&gt; [param] ...) &lt;body&gt; ...)</code></pre>

<p>Constructs a new macro procedure with <code>param</code>s as its parameters and the <code>body</code>
expressions as its body and binds it to <code>name</code> in the current environment.
<code>name</code> must be a valid Scheme symbol. Each <code>param</code> must be a unique valid Scheme
symbol. <code>(&lt;name&gt; [param] ...)</code> can be dotted, with a variable number of excess
arguments bound as a list to the symbol after the dot.</p>


<h4 id="set"><strong><code>set!</code></strong></h4>


<pre><code>(set! &lt;name&gt; &lt;expression&gt;)</code></pre>

<p>Evaluates <code>expression</code> and binds the result to <code>name</code> in the first frame it can
be found in from the current environment. If <code>name</code> is not bound in the current
environment, this causes an error.</p>


<h4 id="quasiquote"><strong><code>quasiquote</code></strong></h4>


<pre><code>(quasiquote &lt;expression&gt;)</code></pre>

<p>Returns the literal <code>expression</code> without evaluating it, unless a subexpression
of <code>expression</code> is of the form:</p>

<pre><code>(unquote &lt;expr2&gt;)</code></pre>

<p>in which case that <code>expr2</code> is evaluated and replaces the above form in the
otherwise unevaluated <code>expression</code>.</p>

<p><code>`&lt;expression&gt;</code> is equivalent to the above form.</p>


<h4 id="unquote"><strong><code>unquote</code></strong></h4>


<p>See above. <code>,&lt;expr2&gt;</code> is equivalent to the form mentioned above.</p>


<h4 id="unquote-x2d-splicing"><strong><code>unquote&#x2d;splicing</code></strong></h4>


<pre><code>(unquote&#x2d;splicing &lt;expr2&gt;)</code></pre>

<p>Similar to <code>unquote</code>, except that <code>expr2</code> must evaluate to a list, which is
then spliced into the structure containing it in <code>expression</code>.</p>

<p><code>,@&lt;expr2&gt;</code> is equivalent to the above form.</p>


<h3 id="web-level">Web Level</h3>


<p>The web level does not contain any special forms beyond what is in the staff
level, though certain primitives do not evaluate their arguments and thus
behave like special forms.</p>
