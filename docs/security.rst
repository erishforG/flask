생각해 볼 보안문제들
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

Flask는 특별한 설정이 주어지지 않는 한 자동으로 모든 값들을 이스케이프(escape)하도록 
Jinja2를 설정한다. 이것으로 template내에서의 XSS문제들을 해결 할 수 있다. 하지만 그래도 
당신이 조심해야 될 부분이 몇 구석 있다.

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

만약 이것을 하지 않는다면 불순한 유저가 매우 쉽게 직접 만든 JavaScript 핸들러를 주입 할 수 
있을 것이다. 예를 들어 아래와 같은 HTML+JavaScript 공격이 들어올 수 있다:


.. sourcecode:: html

   onmouseover=alert(document.cookie)

유저가 마우스를 링크 위로 옮기면 쿠키정보가 경고창에 나오게 된다. 그러나 쿠키를 유저에게 보여주는
대신에 영리한 해커는 다른 Javascript코드를 이용할 수 있다. 해커는 CSS 주입을 혼용하여 이 요소를
페이지 전체에 적용 할 수 있으며 유저는 마우스를 어딘가에다 가져다 대기만 하면 공격을 일으킬 수 있다.


Cross-Site Request Forgery (CSRF)
---------------------------------

다른 큰 문제는 CSRF이다. 이건 매우 복잡한 주제이므로 여기에 자세히 써놓을 순 없지만 
이것이 무엇이고 이론적으로 어떻게 예방을 하는지 다루겠다.

만약 당신의 (로그인)증명정보가 쿠키에 저장되어있다면, 암시적(implicit)으로 상태관리를 하는 것이다.
로그인 상태는 쿠키에 의해 제어가되며 쿠키는 매 페이지 호출시 보내진다. 불행하게도 이것은 
제 3자가 유발한 호출을 포함한다. 이것을 염두해주지 않으면 사람들이 당신의 앱 유저들이 모르는 사이 
바보같은 짓을 저지르며 혼란을 줄 수 있다.

당신이 예를 들어 `http://example.com/user/delete`라는 URL을 갖고 있고 POST 요청을 
하면 해당 유저를 지우는 기능을 한다고 가정하라. 만약 공격자가 Javascript로 그 POST요청을 하는 
페이지를 만들어 몇 유저들을 속이면 유저들은 그 페이지를 로드하고 자신의 프로파일을 지우기에 이를 것이다.

당신이 몇백만의 동시접속자를 가진 페이스북을 운영하고 있고 누군가 귀여운 고양이 사진을 보여주는 링크를
공유하였다고 상상해 보라. 유저들이 그 링크를 들어가서 귀여운 고양이 사진들을 관람하는 사이
그들의 프로파일은 삭제될 것이다.

이것을 예방하는 방법은 서버의 콘탠츠를 수정하는 매 요청 시 마다 일회용 토큰을 보내 쿠키에 저장하고
Form 데이터와 같이 보내게 하는 것이다. 데이터를 받으면 서버에서 두 토큰을 비교하면 된다. 

플라스크는 당신을 위해 그것을 해주지 않는다. 그것을 하기 위한 이상적인 장소는 바로 form 을 확인하는
프레임워크이다. (예: WTForm) 플라스크에는 이것이 존재하지 않는다.

.. _json-security:

JSON 보안
-------------

.. admonition:: ECMAScript 5 변경사항

   ECMAScript 5 부터 리터럴의 성격이 바뀌었다. 리터럴은 이제 ``Array``와 다른 생성자로부터
   생성되지 않고 내장된 ``Array``생성자로부터 생성된다. 이것은 다음의 공격백터를 차단한다. 

JSON은 그 자체로 고급 직렬화(serialize) 된 방식이므로 거의 보안문제를 일으키지 않을 거라고 
믿을 것이다. 하지만 반복되는 구조는 문제를 야기할 수 있기 때문에 선언하면 안된다. 매우 긴 응답은
받아들이는 측에서 서비스 거부를 일으키게 할 수 있다.

그러나 여기엔 숨겨진 문제가 있다. 브라우저들이 CSRF를 방어하기 위해 새운 대책들이 불행하게도 
JSON과의 문제를 일으킨다. 하지만 불행중 다행으로 자바스크립트의 이상한 기능 중 이 문제를 쉽게
해결할 방법 또한 있다. 플라스크는 이것을 이용하여 당신이 위험한 짓을 하는 것을 방지해준다.
이 보호책은 :func:`~flask.jsonify`에만 유효하므로 다른방식으로 JSON을 생성할 경우
위험에 노출될 수 있다.

그래서 위험이 무엇이고 어떻게 대처하냐고 물을 것이다. 문제는 JSON 최상의의 배열(array)에 있다.
JSON호출에 다음과 같은 데이터를 보낸다고 상상해보라. 일반적인 일은 아니지만 그 데이터는 당신의 모든 
친구들의 이름과 이메일주소를 Javascript로 제작된 유저 인터페이스의 일부로 보내고 있다고 가정하자.  

.. sourcecode:: javascript

    [
        {"username": "admin",
         "email": "admin@localhost"}
    ]

그리고 그것은 당현히 당신이 인증을 하여 로그인 되어있을 경우에만 정보를 보낼 것이다. 그리고 그것은
``http://example.com/api/get_friends.json``라는 URL의 모든 GET요청을 통해 이루어진다.

그리고 이제는 영리한 해커가 자신의 웹사이트에 다음과 같은 코드를 내장하고 피해자를 속여 방문하게 하면
어떻게되겠는가:

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
    // 이제 모든 데이터는 captured 배열에 들어있게 된다.
    </script>

당신이 Javascript의 내부를 조금이라도 알고 있다면 이것이 생성자를 패치하고 세터에 콜백을 등록할 
수 있다는 것을 알 수 있을것이다. 공격자는 위와 같은 방법으로 당신이 JSON으로 보낸 모든 데이터를 
가로챌 수 있다. 만약 스크립트 테크에 컨탠트 타입이 ``text/javascript``으로 설정되어 있으면 
브라우저는 ``application/json``마임타입을 완전히 무시하고 Javascript로 인식할 것이다. 
(비록 쓸모 없을지라도) 최상위 배열 요소들이 허락되어있고 생성자를 연결하였기 때문에, 페이지 로드 후
JSON 응답의 데이터는 `captured`배열로 옮겨진다.

자바스크립트에서는 최상위에 (``{...}``)객체 리터럴을 갖는 것이 구문 오류이기 때문에 공격자는 
스크립트로 외부 URL에서 데이터를 로드하도록 요청 할 수 없게 된다. 그러므로 플라스크는 
:func:`~flask.jsonify`를 이용하여 모든 객체를 최상위 요소로만 받도록 허락한다. 다른 평범한
JSON 생성 함수에도 같은 방법을 사용하기 바란다.
