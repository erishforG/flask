Pocoo 스타일가이드
================

Pococo 스타일가이드는 Flask를 포함한 모든 Pococo 프로젝트들을 위한 스타일가이드이다.
이 스타일가이드는 Flask의 패치와 Flask 확장기능(extensions)를 위한 요구사항이다.

일반적으로 Pocoo 스타일 가이드는 :pep:`8`을 따르는데 몇가지 작은 차이와 확장기능이 있다.

일반적인 레이아웃(General Layout)
--------------

들여쓰기:
  스페이스 4개. 탭없음, 예외없음.

줄당 최대 길이(Maximum line length):
  79 글자인데 절대적으로 필요한 경우 84 글자까지 유연하게 제한한다.
  `break`, `continue` 와 `return` 문에 의해서 너무 중첩된 코드는 피해라.

긴구문 계속 쓰기(Continuing long statements):
  구문을 계속 쓰기 위해서는 backslashes(\) 사용할 수 있는데 다음 줄의 마지막 마침표(dot) 또는 같음 기호(equal sign) 또는
  4스페이스 들여쓰기와 정렬 해야한다::

    this_is_a_very_long(function_call, 'with many parameters') \
        .that_returns_an_object_with_an_attribute

    MyModel.query.filter(MyModel.scalar > 120) \
                 .order_by(MyModel.name.desc()) \
                 .limit(10)

  만약 괄호 또는 중괄호에서 구문을 끊는다면, 중괄호에 정렬해라.

    this_is_a_very_long(function_call, 'with many parameters',
                        23, 42, 'and even more')

  리스트 또는 튜플(tuple)의 많은 아이템을 위해서는, 중괄호를 연 이후 즉시 끊어라.

    items = [
        'this is the first', 'set of items', 'with more items',
        'to come in this line', 'like this'
    ]


공백 줄(Blank lines):
  최상위(Top level) 함수 또는 클래스들은 2개의 공백줄로 나눠져야 하고, 나머지는 모두 한개의 공백줄이다.
  너무 많은 공백줄을 코드상에서 논리적인 구문을 주기 위해서 사용하지 마라. 예를들면::

    def hello(name):
        print 'Hello %s!' % name


    def goodbye(name):
        print 'See you %s.' % name


    class MyClass(object):
        """This is a simple docstring"""

        def __init__(self, name):
            self.name = name

        def get_annoying_name(self):
            return self.name.upper() + '!!!!111'

표현과 구문(Expressions and Statements)
--------------------------

일반적인 공백 규칙들:
  - 단어가 아닌 단항 연산자에는 (e.g.: ``-``, ``~`` etc.) 공백을 사용하지 마라.
  - 공백은 2항 연산자들 사이에 위치한다.


  좋은 경우::

    exp = -1.05
    value = (item_value / item_count) * offset / exp
    value = my_list[index]
    value = my_dict['key']

  좋지못한 경우::

    exp = - 1.05
    value = ( item_value / item_count ) * offset / exp
    value = (item_value/item_count)*offset/exp
    value=( item_value/item_count ) * offset/exp
    value = my_list[ index ]
    value = my_dict ['key']

요다 구문은 잘 어울리지 않는다:
  절대 상수를 변수에 비교하지마라, 항상 변수를 상수에 비교해라:

  좋은 경우::

    if method == 'md5':
        pass

  좋지 못한 경우::

    if 'md5' == method:
        pass

비교:
  - 임의의 타입(arbitrary types) 에 대해서는  : ``==`` 와 ``!=``
  - 싱글톤(singletons) 에 대해서는 : ``is`` 와 ``is not`` (eg: ``foo is not
    None``)
  - 어떤 것을 `True` 또는 `False`와 비교하지마라(예를들면, ``foo == False`` 하지마라, 대신에  ``not foo`` 사용해라)

부정 방지 검사
  ``not foo in bar`` 대신에  ``foo not in bar`` 사용해라.


인스턴스 검사:
  ``type(A) is C`` 대신에 ``isinstance(a, C)``, 그러나 일반적으로 인스턴스 검사를 피하도록 해라.
   특징들을 검사해라.


이름 규칙(Naming Conventions)
------------------

- 클래스 이름 : ``CamelCase``, 약어는 대문자 유지(``HttpWriter`` 가 아니라 ``HTTPWriter``)
- 변수 이름 : 소문자와 밑줄(``lowercase_with_underscores``)
- 메소드 와 함수 이름 : 소문자와 밑줄(``lowercase_with_underscores``)
- 상수 이름 : 대문자와 밑줄(``UPPERCASE_WITH_UNDERSCORES``)
- 전처리된 정규 표현식 : ``name_re``


숨기는(Protected) 멤버변수는 한개의 밑줄이 앞에 붙는다.  두개의 밑줄은 믹스인(mixin) 클래스들을 위해서
예약되어 있다.


클래스 상에서 키워드와 함께 밑줄이 추가된다.  내장(builtin) 되어있는 것들과의 충돌이
가능한데, 변수의 이름에 밑줄을 추가해서 이를 해결을 반드시 할 필요는 없다.
만약 함수가 숨겨져있는 내장 변수으로의 접근이 필요하다면, 내장 변수를 대신에 다른이름으로
연결해라.


함수와 메소드 인자:
  - 클래스 메소드 : ``cls`` 를 첫 파라미터로 쓴다.
  - 인스턴스 메소드 : ``self`` 를 첫 파라미터로 쓴다.
  - 프로퍼티를 위한 람다는 첫번째 파라미터로  ``x`` 를 쓰는데,  예를들면 ``display_name = property(lambda x: x.real_name
    or x.username)``

문서화 문자열(Docstrings)
----------

문서화 문자열 규칙:
  모든 문서화 문자열들은 Sphinx 에 의해서 해석되는 reStructuredText 형태이다.
  문서화 문자열 내 줄의 수에 따라서 다르게 보여진다. 만약 오직 한줄 뿐이라면, 닫는
  세개의 인용부호는 여는 부분과 같은 줄에 있게 되지만, 다른 경우 텍스트는 여는 부분과
  같은 줄에 있고 닫는 세개의 인용부호는 새로운 줄에 위치한다::

    def foo():
        """This is a simple docstring"""


    def bar():
        """This is a longer docstring with so much information in there
        that it spans three lines.  In this case the closing triple quote
        is on its own line.
        """

모듈 헤더:
  모듈 헤더는 utf-8 인코딩 선언(만약 비 ASCII 문자가 사용된다면, 매순간 이 부분을 추천한다) 과
  표준 문서화 문자열로 구성된다::

    # -*- coding: utf-8 -*-
    """
        package.module
        ~~~~~~~~~~~~~~

        A brief description goes here.

        :copyright: (c) YEAR by AUTHOR.
        :license: LICENSE_NAME, see LICENSE_FILE for more details.
    """

  적절한 저장권 및 라이센스 파일들이 허가된 플라스크 확장기능(extensions)에는
  필수적임을 명심해라.


주석(Comments)
--------

주석을 위한 규칙은 문서화 문자열과 비슷하다.  둘다 reStructuredText 를 포맷으로 한다.  만약
주석이 문서로 사용되어 진다면, (``#``) 뒤에 콜론(:)을 넣어라::

    class User(object):
        #: the name of the user as unicode string
        name = Column(String)
        #: the sha1 hash of the password + inline salt
        pw_hash = Column(String)
