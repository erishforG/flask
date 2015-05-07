HTML/XHTML FAQ
==============

Flask 문서와 예제 어플리케이션은 HTML5를 사용한다. 
당신은 그럼으로써 HTML이 더욱 명확하고 부르기 빨라진다는 것을
마침 태그(end tags)가 사용되지 않는 것에 대해 선택적인 것과 같은
많은 상황속에서 눈치챌 것이다.
왜냐하면 개발자 사이에서 HTML과 XHTML 사이에 혼란이 있으며, 이 문서는
주요한 질문들들 중 몇몇에 대해 대답하려 한다. 

XHTML의 역사
----------------

한동안, HTML은 XHTML로 대체되는 양상을 띄기 시작했다.
그렇지만 인터넷에 있는 거의 어떤 사이트도 사실 XHTML이 아니다. (XML 규칙을 사용하는 HTML이다)
왜 이런 모습을 가지는지에 대한 몇몇개의 주요한 이유들이 있다.
그들 중 하나는 인터넷 익스플로러의 제대로 된 XHTML 지원이 부족이다.
XHTML는 반드시 MIME 형식 'application/xhtml+xml'이 제공되야 한다는 XHTML 스펙을 명시하지만,
인터넷 익스플로러는 MIME 형식의 파일을 읽는 것을 거부한다.
상대적으로 웹 서버들을 XHTML 속성으로 제공하는 것을 설정하는 것은 쉽지만,
거의 적은 사람들만이 한다. 이것이 XHTML을 사용하는 속성은 상당히 고통스러운 이유이다.

고통의 제일 중요한 원인들중의 하나는 XML의 가혹한 (엄격하고 무자비한) 에러 핸들링이다.
XML 파싱 에러와 접하게 되면, 에러로부터 복구 및 무엇을 할 수 있는지에 대한 시도
대신 브라우저는 사용자가 보기 싫은 에러 메세지를 보이는 것으로 가정한다.
웹에서 대부분의 (X)HTML 세대는 효력없는 XHTML을 실수로 만드는 것에서부터
당신을 보호하지 않는 non-XML 템플릿 엔진을 기반으로 하고 있다.
Kid와 the pupular Genshi와 같은 XML 기반 템플릿 엔진들이 있지만,
이것들은 더 큰 런타임 부담과 함께 오기도 하며,
이것들은 XML 규칙을 따라야만 하기 때문에 사용하기 간단하지 않다.

그렇지만 다수의 사용자들은 대개 XHTML을 사용한다고 가정한다.
그들은 다큐먼트의 맨 위에 XHTML 타입을 쓰거나 자체적으로 닫힌 필수 태그들을 썼다.
(``<br>`` 은 XHTML에서 ``<br/>`` 이나 ``<br></br>``이 된다).
그렇지만, 다큐먼트가 적절히 XHTML로서 인증된다고 해도, 
무엇이 정말로 브라우저들안에서 XHTML/HTML 프로세싱이 MIME 타입이라고 단정짓는가,
이전에 말한 것이 적절히 성립되지 않을때도 있다. 그래서 유효한 XHTML은
유효하지 않은 HTML로 처리되었다.

XHTML은 또한 JavaScript가 사용되던 방식을 바꿨다. XHTML과 함께 적절히 작동하기 위해,
프로그래머들은 HTML 요소들을 위해 쿼리하려고 XHTML 네임스페이스와 함께 namespaced DOM 인터페이스를
사용해야만 한다.

HTML5의 역사
----------------

Development of the HTML5 specification was started in 2004 under the name
"Web Applications 1.0" by the Web Hypertext Application Technology Working
Group, or WHATWG (which was formed by the major browser vendors Apple,
Mozilla, and Opera) with the goal of writing a new and improved HTML
specification, based on existing browser behavior instead of unrealistic
and backwards-incompatible specifications.

For example, in HTML4 ``<title/Hello/`` theoretically parses exactly the
same as ``<title>Hello</title>``.  However, since people were using
XHTML-like tags along the lines of ``<link />``, browser vendors implemented
the XHTML syntax over the syntax defined by the specification.

In 2007, the specification was adopted as the basis of a new HTML
specification under the umbrella of the W3C, known as HTML5.  Currently,
it appears that XHTML is losing traction, as the XHTML 2 working group has
been disbanded and HTML5 is being implemented by all major browser vendors.

HTML VS XHTML
-----------------

The following table gives you a quick overview of features available in
HTML 4.01, XHTML 1.1 and HTML5. (XHTML 1.0 is not included, as it was
superseded by XHTML 1.1 and the barely-used XHTML5.)

.. tabularcolumns:: |p{9cm}|p{2cm}|p{2cm}|p{2cm}|

+-----------------------------------------+----------+----------+----------+
|                                         | HTML4.01 | XHTML1.1 | HTML5    |
+=========================================+==========+==========+==========+
| ``<tag/value/`` == ``<tag>value</tag>`` | |Y| [1]_ | |N|      | |N|      |
+-----------------------------------------+----------+----------+----------+
| ``<br/>`` supported                     | |N|      | |Y|      | |Y| [2]_ |
+-----------------------------------------+----------+----------+----------+
| ``<script/>`` supported                 | |N|      | |Y|      | |N|      |
+-----------------------------------------+----------+----------+----------+
| should be served as `text/html`         | |Y|      | |N| [3]_ | |Y|      |
+-----------------------------------------+----------+----------+----------+
| should be served as                     | |N|      | |Y|      | |N|      |
| `application/xhtml+xml`                 |          |          |          |
+-----------------------------------------+----------+----------+----------+
| strict error handling                   | |N|      | |Y|      | |N|      |
+-----------------------------------------+----------+----------+----------+
| inline SVG                              | |N|      | |Y|      | |Y|      |
+-----------------------------------------+----------+----------+----------+
| inline MathML                           | |N|      | |Y|      | |Y|      |
+-----------------------------------------+----------+----------+----------+
| ``<video>`` tag                         | |N|      | |N|      | |Y|      |
+-----------------------------------------+----------+----------+----------+
| ``<audio>`` tag                         | |N|      | |N|      | |Y|      |
+-----------------------------------------+----------+----------+----------+
| New semantic tags like ``<article>``    | |N|      | |N|      | |Y|      |
+-----------------------------------------+----------+----------+----------+

.. [1] This is an obscure feature inherited from SGML. It is usually not
       supported by browsers, for reasons detailed above.
.. [2] This is for compatibility with server code that generates XHTML for
       tags such as ``<br>``.  It should not be used in new code.
.. [3] XHTML 1.0 is the last XHTML standard that allows to be served
       as `text/html` for backwards compatibility reasons.

.. |Y| image:: _static/yes.png
       :alt: Yes
.. |N| image:: _static/no.png
       :alt: No

"strict"의 의미는?
------------------------

HTML5 has strictly defined parsing rules, but it also specifies exactly
how a browser should react to parsing errors - unlike XHTML, which simply
states parsing should abort. Some people are confused by apparently
invalid syntax that still generates the expected results (for example,
missing end tags or unquoted attribute values).

Some of these work because of the lenient error handling most browsers use
when they encounter a markup error, others are actually specified.  The
following constructs are optional in HTML5 by standard, but have to be
supported by browsers:

-   Wrapping the document in an ``<html>`` tag
-   Wrapping header elements in ``<head>`` or the body elements in
    ``<body>``
-   Closing the ``<p>``, ``<li>``, ``<dt>``, ``<dd>``, ``<tr>``,
    ``<td>``, ``<th>``, ``<tbody>``, ``<thead>``, or ``<tfoot>`` tags.
-   Quoting attributes, so long as they contain no whitespace or
    special characters (like ``<``, ``>``, ``'``, or ``"``).
-   Requiring boolean attributes to have a value.

This means the following page in HTML5 is perfectly valid:

.. sourcecode:: html

    <!doctype html>
    <title>Hello HTML5</title>
    <div class=header>
      <h1>Hello HTML5</h1>
      <p class=tagline>HTML5 is awesome
    </div>
    <ul class=nav>
      <li><a href=/index>Index</a>
      <li><a href=/downloads>Downloads</a>
      <li><a href=/about>About</a>
    </ul>
    <div class=body>
      <h2>HTML5 is probably the future</h2>
      <p>
        There might be some other things around but in terms of
        browser vendor support, HTML5 is hard to beat.
      <dl>
        <dt>Key 1
        <dd>Value 1
        <dt>Key 2
        <dd>Value 2
      </dl>
    </div>


HTML5에서의 신기술
-------------------------

HTML5 adds many new features that make Web applications easier to write
and to use.

-   The ``<audio>`` and ``<video>`` tags provide a way to embed audio and
    video without complicated add-ons like QuickTime or Flash.
-   Semantic elements like ``<article>``, ``<header>``, ``<nav>``, and
    ``<time>`` that make content easier to understand.
-   The ``<canvas>`` tag, which supports a powerful drawing API, reducing
    the need for server-generated images to present data graphically.
-   New form control types like ``<input type="date">`` that allow user
    agents to make entering and validating values easier.
-   Advanced JavaScript APIs like Web Storage, Web Workers, Web Sockets,
    geolocation, and offline applications.

Many other features have been added, as well. A good guide to new features
in HTML5 is Mark Pilgrim's soon-to-be-published book, `Dive Into HTML5`_.
Not all of them are supported in browsers yet, however, so use caution.

.. _Dive Into HTML5: http://www.diveintohtml5.org/

무엇을 썼어야 할까?
--------------------

Currently, the answer is HTML5.  There are very few reasons to use XHTML
considering the latest developments in Web browsers.  To summarize the
reasons given above:

-   Internet Explorer (which, sadly, currently leads in market share)
    has poor support for XHTML.
-   Many JavaScript libraries also do not support XHTML, due to the more
    complicated namespacing API it requires.
-   HTML5 adds several new features, including semantic tags and the
    long-awaited ``<audio>`` and ``<video>`` tags.
-   It has the support of most browser vendors behind it.
-   It is much easier to write, and more compact.

For most applications, it is undoubtedly better to use HTML5 than XHTML.
