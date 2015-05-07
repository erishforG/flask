Security Considerations
=======================

웹 앱들은 주로 여러가지 보안문제에 직면하기 마련이고 모든것을 제대로 하긴 힘들다. 
Flask는 당신을 위해 몇가지 문제를 대신 해결해준다. 하지만 당신이 해야할 일도 몇가지 있다.

.. _xss:

Cross-Site Scripting (XSS)
--------------------------

Cross-Site Scripting은 웹사이트에 임의의 HTML과 Javascript를 주입(inject)하는 개념이다.
이것을 예방하기 위해서는 의도하지 않은 HTML코드가 받아지지 않도록 적절하게 텍스트를 escpae 
해주어야 한다. 더 많은 정보를 위해선 위키피디아의 다음 글을 확인하기 바란다.
`Cross-Site Scripting<http://en.wikipedia.org/wiki/Cross-site_scripting>`_.

Flask는 특별한 설정이 주어지지 않는 한 자동으로 모든 값들을 escape하도록 Jinja2를 설정한다.
이것으로 template내에서의 XSS문제들을 해결 할 수 있다. 하지만 그래도 당신이 조심해야 될 부분이 
몇 구석 있다.

- Jinja2없이 HTML을 생성하는 것
- 유저가 제출한 데이터에 :class:`~flask.Markup`를 호출 하는것
- 업로드 된 파일에서부터 HTML파일을 보내는것은 절대로 하지 말길 바란다. 이것 을 예방하기 위해
  `Content-Disposition: attachment`를 사용하라.
- 업로드 된 파일로 부터 텍스트파일을 보내는 것. 몇몇의 브라우저들은 첫 한두개의 byte를 기반으로
  content-type을 파악하므로 혼란을 줄 수 있다.


다음으로 중요한 것중 하나는 따옴표 처리가 되지 않은 속성들이다. Jinja2는 HTML을 escape함으로서 
당신을 XSS로부터 지켜줄 수 있지만 속성 injection을 통한 XSS로 부터는 보호할 수 없다.
이 취약점을 해결하기 위해선 Jinja 표현을 할때 모든 속성을 아래와 같이 큰따옴표나 작은따옴표로 
묶어주어야 한다.

.. sourcecode:: html+jinja

   <a href="{{ href }}">the text</a>

만약 이것을 하지 않는다면 불순한 유저가 매우 쉽게 직접 만든 JavaScript handler를 주입 할 수 
있을 것이다. 예를 들어 아래와 같은 HTML+JavaScript 공격이 들어올 수 있다:


.. sourcecode:: html

   onmouseover=alert(document.cookie)

When the user would then move with the mouse over the link, the cookie
would be presented to the user in an alert window.  But instead of showing
the cookie to the user, a good attacker might also execute any other
JavaScript code.  In combination with CSS injections the attacker might
even make the element fill out the entire page so that the user would
just have to have the mouse anywhere on the page to trigger the attack.


Cross-Site Request Forgery (CSRF)
---------------------------------

다른 큰 문제는 CSRF이다. 이건 매우 복잡한 주제이므로 여기에 자세히 써놓을 순 없지만 
이것이 무엇이고 이론적으로 어떻게 예방을 하는지 다루겠다.

If your authentication information is stored in cookies, you have implicit
state management.  

만약 당신의 (로그인)증명정보가 쿠키에 저장되어있다면, implicit state management가 있는 것이다.
로그인 상태는 쿠키에 의해 제어가되며 쿠키는 매 페이지 호출시 보내진다. 불행하게도 이것은 
제 3자가 유발한 호출을 포함한다. 이것을 염두해주지 않으면 사람들이 당신의 앱 유저들이 모르는 사이 
바보같은 짓을 저지르며 혼란을 줄 수 있다.

당신이 (예를 들어 `http://example.com/user/delete`)라는 URL을 갖고 있고 POST 요청을 
하면 유저를 지우는 기능을 한다고 가정하라. 만약 공격자가 Javascript로 그 POST요청을 하는 페이지를 
만들어 몇 유저들을 속이면 유저들은 그 페이지를 로드하고 자신의 프로파일을 지우기에 이를 것이다.

당신이 몇백만의 동시접속자를 가진 페이스북을 운영하고 있고 누군가 귀여운 고양이 사진을 보여주는 링크를
공유하였다고 상상해 보라. 유저들이 그 링크를 들어가서 귀여운 고양이 사진들을 관람하는 사이
그들의 프로파일은 삭제될 것이다.

이것을 예방하는 방법은 서버의 콘탠츠를 수정하는 매 요청 시 마다 일회용 토큰을 보내 쿠키에 저장하고
Form 데이터와 같이 보내게 하는 것이다. 데이터를 받으면 서버에서 두 토큰을 비교하면 된다. 

Why does Flask not do that for you?  The ideal place for this to happen is
the form validation framework, which does not exist in Flask.

.. _json-security:

JSON Security
-------------

.. admonition:: ECMAScript 5 Changes

   Starting with ECMAScript 5 the behavior of literals changed.  Now they
   are not constructed with the constructor of ``Array`` and others, but
   with the builtin constructor of ``Array`` which closes this particular
   attack vector.

JSON itself is a high-level serialization format, so there is barely
anything that could cause security problems, right?  You can't declare
recursive structures that could cause problems and the only thing that
could possibly break are very large responses that can cause some kind of
denial of service at the receiver's side.

However there is a catch.  Due to how browsers work the CSRF issue comes
up with JSON unfortunately.  Fortunately there is also a weird part of the
JavaScript specification that can be used to solve that problem easily and
Flask is kinda doing that for you by preventing you from doing dangerous
stuff.  Unfortunately that protection is only there for
:func:`~flask.jsonify` so you are still at risk when using other ways to
generate JSON.

So what is the issue and how to avoid it?  The problem are arrays at
top-level in JSON.  Imagine you send the following data out in a JSON
request.  Say that's exporting the names and email addresses of all your
friends for a part of the user interface that is written in JavaScript.
Not very uncommon:

.. sourcecode:: javascript

    [
        {"username": "admin",
         "email": "admin@localhost"}
    ]

And it is doing that of course only as long as you are logged in and only
for you.  And it is doing that for all `GET` requests to a certain URL,
say the URL for that request is
``http://example.com/api/get_friends.json``.

So now what happens if a clever hacker is embedding this to his website
and social engineers a victim to visiting his site:

.. sourcecode:: html

    <script type=text/javascript>
    var captured = [];
    var oldArray = Array;
    function Array() {
      var obj = this, id = 0, capture = function(value) {
        obj.__defineSetter__(id++, capture);
        if (value)
          captured.push(value);
      };
      capture();
    }
    </script>
    <script type=text/javascript
      src=http://example.com/api/get_friends.json></script>
    <script type=text/javascript>
    Array = oldArray;
    // now we have all the data in the captured array.
    </script>

If you know a bit of JavaScript internals you might know that it's
possible to patch constructors and register callbacks for setters.  An
attacker can use this (like above) to get all the data you exported in
your JSON file.  The browser will totally ignore the ``application/json``
mimetype if ``text/javascript`` is defined as content type in the script
tag and evaluate that as JavaScript.  Because top-level array elements are
allowed (albeit useless) and we hooked in our own constructor, after that
page loaded the data from the JSON response is in the `captured` array.

Because it is a syntax error in JavaScript to have an object literal
(``{...}``) toplevel an attacker could not just do a request to an
external URL with the script tag to load up the data.  So what Flask does
is to only allow objects as toplevel elements when using
:func:`~flask.jsonify`.  Make sure to do the same when using an ordinary
JSON generate function.
