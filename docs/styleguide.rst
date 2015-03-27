Pocoo 스타일가이드
================

The Pocoo styleguide is the styleguide for all Pocoo Projects, including
Flask.  This styleguide is a requirement for Patches to Flask and a
recommendation for Flask extensions.

Pococo 스타일가이드는 Flask를 포함한 모든 Pococo 프로젝트들을 위한 스타일가이드이다.
이 스타일가이드는 Flask의 패치와 Flask 확장기능(extensions)를 위한 요구사항이다.

In general the Pocoo Styleguide closely follows :pep:`8` with some small
differences and extensions.

일반적으로 Pocoo 스타일 가이드는 :pep:`8`을 따르는데 몇가지 작은 차이와 확장기능이 있다.

일반적인 레이아웃(General Layout)
--------------

Indentation:
  4 real spaces.  No tabs, no exceptions.

들여쓰기:
  스페이스 4개. 탭없음, 예외없음.

Maximum line length:
  79 characters with a soft limit for 84 if absolutely necessary.  Try
  to avoid too nested code by cleverly placing `break`, `continue` and
  `return` statements.

줄당 최대 길이(Maximum line length):
  79 글자인데 절대적으로 필요한 경우 84 글자까지 유연하게 제한한다.
  `break`, `continue` 와 `return` 문에 의해서 너무 중첩된 코드는 피해라.

Continuing long statements:
  To continue a statement you can use backslashes in which case you should
  align the next line with the last dot or equal sign, or indent four
  spaces::

긴구문 계속 쓰기(Continuing long statements):
  구문을 계속 쓰기 위해서는 backslashes(\) 사용할 수 있는데 다음 줄의 마지막 마침표(dot) 또는 같음 기호(equal sign) 또는
  4스페이스 들여쓰기와 정렬 해야한다::

    this_is_a_very_long(function_call, 'with many parameters') \
        .that_returns_an_object_with_an_attribute

    MyModel.query.filter(MyModel.scalar > 120) \
                 .order_by(MyModel.name.desc()) \
                 .limit(10)

  If you break in a statement with parentheses or braces, align to the
  braces::
  만약 괄호 또는 중괄호에서 구문을 끊는다면, 중괄호에 정렬해라.

    this_is_a_very_long(function_call, 'with many parameters',
                        23, 42, 'and even more')

  For lists or tuples with many items, break immediately after the
  opening brace::

  리스트 또는 튜플(tuple)의 많은 아이템을 위해서는, 중괄호를 연 이후 즉시 끊어라.

    items = [
        'this is the first', 'set of items', 'with more items',
        'to come in this line', 'like this'
    ]

Blank lines:
  Top level functions and classes are separated by two lines, everything
  else by one.  Do not use too many blank lines to separate logical
  segments in code.  Example::

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

Expressions and Statements
--------------------------

General whitespace rules:
  - No whitespace for unary operators that are not words
    (e.g.: ``-``, ``~`` etc.) as well on the inner side of parentheses.
  - Whitespace is placed between binary operators.

  Good::

    exp = -1.05
    value = (item_value / item_count) * offset / exp
    value = my_list[index]
    value = my_dict['key']

  Bad::

    exp = - 1.05
    value = ( item_value / item_count ) * offset / exp
    value = (item_value/item_count)*offset/exp
    value=( item_value/item_count ) * offset/exp
    value = my_list[ index ]
    value = my_dict ['key']

Yoda statements are a no-go:
  Never compare constant with variable, always variable with constant:

  Good::

    if method == 'md5':
        pass

  Bad::

    if 'md5' == method:
        pass

Comparisons:
  - against arbitrary types: ``==`` and ``!=``
  - against singletons with ``is`` and ``is not`` (eg: ``foo is not
    None``)
  - never compare something with `True` or `False` (for example never
    do ``foo == False``, do ``not foo`` instead)

Negated containment checks:
  use ``foo not in bar`` instead of ``not foo in bar``

Instance checks:
  ``isinstance(a, C)`` instead of ``type(A) is C``, but try to avoid
  instance checks in general.  Check for features.


Naming Conventions
------------------

- Class names: ``CamelCase``, with acronyms kept uppercase (``HTTPWriter``
  and not ``HttpWriter``)
- Variable names: ``lowercase_with_underscores``
- Method and function names: ``lowercase_with_underscores``
- Constants: ``UPPERCASE_WITH_UNDERSCORES``
- precompiled regular expressions: ``name_re``

Protected members are prefixed with a single underscore.  Double
underscores are reserved for mixin classes.

On classes with keywords, trailing underscores are appended.  Clashes with
builtins are allowed and **must not** be resolved by appending an
underline to the variable name.  If the function needs to access a
shadowed builtin, rebind the builtin to a different name instead.

Function and method arguments:
  - class methods: ``cls`` as first parameter
  - instance methods: ``self`` as first parameter
  - lambdas for properties might have the first parameter replaced
    with ``x`` like in ``display_name = property(lambda x: x.real_name
    or x.username)``


Docstrings
----------

Docstring conventions:
  All docstrings are formatted with reStructuredText as understood by
  Sphinx.  Depending on the number of lines in the docstring, they are
  laid out differently.  If it's just one line, the closing triple
  quote is on the same line as the opening, otherwise the text is on
  the same line as the opening quote and the triple quote that closes
  the string on its own line::

    def foo():
        """This is a simple docstring"""


    def bar():
        """This is a longer docstring with so much information in there
        that it spans three lines.  In this case the closing triple quote
        is on its own line.
        """

Module header:
  The module header consists of an utf-8 encoding declaration (if non
  ASCII letters are used, but it is recommended all the time) and a
  standard docstring::

    # -*- coding: utf-8 -*-
    """
        package.module
        ~~~~~~~~~~~~~~

        A brief description goes here.

        :copyright: (c) YEAR by AUTHOR.
        :license: LICENSE_NAME, see LICENSE_FILE for more details.
    """

  Please keep in mind that proper copyrights and license files are a
  requirement for approved Flask extensions.


Comments
--------

Rules for comments are similar to docstrings.  Both are formatted with
reStructuredText.  If a comment is used to document an attribute, put a
colon after the opening pound sign (``#``)::

    class User(object):
        #: the name of the user as unicode string
        name = Column(String)
        #: the sha1 hash of the password + inline salt
        pw_hash = Column(String)
