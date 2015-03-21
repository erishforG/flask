.. _design:

Flask에서 설계 결정(Design decisions)
=========================

만약 당신이 Flask가 다른 방식도 아니고 특정한 방식으로 운영되는지 궁금하다면,
이 섹션은 당신을 위한 것이다. 이것은 당신에게 다른 프레임워크와 비교하였을때,
처음에는 임의적이고 놀랍게 보이는 설계 결정들에 대한 아이디어를 줄 것이다.

명시적 어플리케이션 객체(The Explicit Application Object)
-------------------------------

WSGI 기반 파이썬 웹 어플리케이션은 실제 어플리케이션으로 구현된
하나의 중앙 컬러블(callable) 객체를 가지고 있어야 한다.
이것은 Flask에서 Flask 클래스의 객체이다. 각각의 Flask 어플리케이션은
자신이 이 클래스의 객체를 생성해야만 하고, 모듈의 이름을 넘여야 한다.
하지만 왜 Flask 자신이 하지 않는 것일까?

다음은 명시적 어플리케이션 객체가 없는 코드는::

    from flask import Flask
    app = Flask(__name__)

    @app.route('/')
    def index():
        return 'Hello World!'

이것처럼 보이게 될 것이다::

    from hypothetical_flask import route

    @route('/')
    def index():
        return 'Hello World!'

여기에는 세가지 주요한 이유가 있다. 제일 중요한 이유는 
내시적 어플리케이션 객체들(implicit application objects)은 매 번마다 하나의 변수를
필요로 하기 때문이다. 다량의 어플리케이션들을 유지하는 것처럼, 
하나의 어플리케이션 객체로 여러개의 어플리케이션들을 속이는 방법도 있다.
하지만 여기서는 자세히 설명하지 않는 몇몇의 문제들을 발생시킨다. 지금 질문은 이렇다:
똑같은 상황에 하나의 어플리케이션보다도 더 많이 마이크로프레임워크를 필요로 하는때는
언제인가? 이에 대한 좋은 예시는 단위테스트이다. 당신이 특정한 기능을 테스트할 때
미니멀 어플리케이션을 만드는 것이 매우 도움이 될 것이다. 
어플리케이션 객체가 삭제가 된다면, 어플리케이션에 할당된 모든 것은 해제될 것이다.

명시적 객체들이 코드 내에 있을때 가능해진 또 다른 것은 특정 기능을 바꾸기 위해서
기본 클래스를 하위로 분리시킬 수 있다(:class:'~flask.Flask').
만약 객체가 노출되지 않은 클레스를 기반으로 예정보다 빨리 객체가 만들어졌다면
해킹을 제외하면 이는 불가능하다.

그러나 왜 Flask가 저 클래스의 명시적 인스턴스 생성에 의존하는 또 다른 매우 중요한 이유가 있다.
: 패키지 네임. 언제나 Flask 인스턴스를 생성할때마다, 보통 패키지 네임으로서 '__name__'으로 전달한다.
Flask는 당신의 모듈에 관련된 적절히 리소스를 부르는 것은 앞의 정보에 의존한다.
반영을 위해 Python의 외적인 지원의 도움으로서
어디에 템플릿과 고정(static) 파일들이 저장되었는지 찾기 위해 패키지에 접속하는 것이 가능해진다.
(참조 :meth:`~flask.Flask.open_resource`). 현재 명확하게도 
어떠한 환경 설정도 필요하지 않으며 어플리케이션 모듈에 관련된 템블릿을 불러오는 것이
가능한 프레임워크들이 존재한다.
하지만 그것들은 어디에 어플리케이션이 있는지 구분하기 위해 
매우 믿을 수없는 방법으로서 현재 운영되는 디렉토리를 사용해야만 한다.
현재 운영되는 디렉토리는 폭넓은 진행이 가능하며(process-wide)
만약에 하나의 프로세스 안에서 여러개의 어플리케이션을 운영중이라면
(당신이 모르게 웹서버에서 일어 날 수 있다) 경로는 사라진다.
나쁨 : 많은 웹서버들은 직접 당신의 어플리케이션의 디렉토리를 작동시키는 것보다도
 똑같은 폴더가 아니어도 되는 다큐먼트 루트(root)를 작동시킨다.

세번째 이유는 "명시적이 내시적인 것보다 좋다"는 것이다.
다른 것들은 기억할 필요 없이, 그 객체는 당신의 WSGI 어플리케이션이다.
혹시 당신이 WSGI 미들웨어를 적용시키고 싶다면, 단순하게 감싸면 끝이다.
(비록 당신은 어플리케이션에서의 레퍼런스를 잃지 않기 위해
이것을 하기 위해 더 좋은 방법들이 있다. :meth:`~flask.Flask.wsgi_app`)

더욱이 이 디자인은 유닛테스팅과 비슷한 것들을 위해 매우 도움이 되는
어플리케이션을 만들기 위해 팩토리 함수 (factory function)을 사용하는 것을
가능하게 만든다. (:ref:`app-factories`)

루팅 시스템(The Routing System)
------------------

Flask는 복합성에 의해 자동적으로 루트를 정렬하기 위해 고안된
베크저그 루팅 시스템(Werkzeug routing system)을 사용한다.
이것은 당신이 임의적인 순서에서 루트들을 선언할 수 있으며
그것들이 기대한대로 여전히 작동할 것이라는 뜻이다.
만약 어플리케이션이 여러개의 모듈로 나뉠때
데코레이터들(decorators)이 한정되지 않은 순서로서 던져지기 때문에
당신이 루팅에 기반한 데코레이터를 제대로 상속을 원한다면 이것은 필수조건이다.
베크저그 루팅 시스템과 함께 또 다른 디자인 결정은
베크저그에서의 루트들은 URL을 특벽하게 보장하려 한다는 것이다.
만약에 루트가 애매하면 이것은 자동적으로 표준 URL로 재연결하는 것이므로
베크저그는 상당히 좋다.

하나의 템블릿 엔진(One Template Engine)
-------------------

Flask decides on one template engine: Jinja2.  Why doesn't Flask have a
pluggable template engine interface?  You can obviously use a different
template engine, but Flask will still configure Jinja2 for you.  While
that limitation that Jinja2 is *always* configured will probably go away,
the decision to bundle one template engine and use that will not.

Template engines are like programming languages and each of those engines
has a certain understanding about how things work.  On the surface they
all work the same: you tell the engine to evaluate a template with a set
of variables and take the return value as string.

But that's about where similarities end.  Jinja2 for example has an
extensive filter system, a certain way to do template inheritance, support
for reusable blocks (macros) that can be used from inside templates and
also from Python code, uses Unicode for all operations, supports
iterative template rendering, configurable syntax and more.  On the other
hand an engine like Genshi is based on XML stream evaluation, template
inheritance by taking the availability of XPath into account and more.
Mako on the other hand treats templates similar to Python modules.

When it comes to connecting a template engine with an application or
framework there is more than just rendering templates.  For instance,
Flask uses Jinja2's extensive autoescaping support.  Also it provides
ways to access macros from Jinja2 templates.

A template abstraction layer that would not take the unique features of
the template engines away is a science on its own and a too large
undertaking for a microframework like Flask.

Furthermore extensions can then easily depend on one template language
being present.  You can easily use your own templating language, but an
extension could still depend on Jinja itself.


Micro with Dependencies
-----------------------

Why does Flask call itself a microframework and yet it depends on two
libraries (namely Werkzeug and Jinja2).  Why shouldn't it?  If we look
over to the Ruby side of web development there we have a protocol very
similar to WSGI.  Just that it's called Rack there, but besides that it
looks very much like a WSGI rendition for Ruby.  But nearly all
applications in Ruby land do not work with Rack directly, but on top of a
library with the same name.  This Rack library has two equivalents in
Python: WebOb (formerly Paste) and Werkzeug.  Paste is still around but
from my understanding it's sort of deprecated in favour of WebOb.  The
development of WebOb and Werkzeug started side by side with similar ideas
in mind: be a good implementation of WSGI for other applications to take
advantage.

Flask is a framework that takes advantage of the work already done by
Werkzeug to properly interface WSGI (which can be a complex task at
times).  Thanks to recent developments in the Python package
infrastructure, packages with dependencies are no longer an issue and
there are very few reasons against having libraries that depend on others.


Thread Locals
-------------

Flask uses thread local objects (context local objects in fact, they
support greenlet contexts as well) for request, session and an extra
object you can put your own things on (:data:`~flask.g`).  Why is that and
isn't that a bad idea?

Yes it is usually not such a bright idea to use thread locals.  They cause
troubles for servers that are not based on the concept of threads and make
large applications harder to maintain.  However Flask is just not designed
for large applications or asynchronous servers.  Flask wants to make it
quick and easy to write a traditional web application.

Also see the :ref:`becomingbig` section of the documentation for some
inspiration for larger applications based on Flask.


What Flask is, What Flask is Not
--------------------------------

Flask will never have a database layer.  It will not have a form library
or anything else in that direction.  Flask itself just bridges to Werkzeug
to implement a proper WSGI application and to Jinja2 to handle templating.
It also binds to a few common standard library packages such as logging.
Everything else is up for extensions.

Why is this the case?  Because people have different preferences and
requirements and Flask could not meet those if it would force any of this
into the core.  The majority of web applications will need a template
engine in some sort.  However not every application needs a SQL database.

The idea of Flask is to build a good foundation for all applications.
Everything else is up to you or extensions.
