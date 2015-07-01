Flask에서의 유니코드(Unicode)
================

Flask는 Jinja2와 Werkzeug와 마찬가지로 문자를 유니코드로 다룬다. 이 라이브러리들만이
아니라 대부분의 Python 라이브러리들이 그렇다. 유니코드가 무엇인지 모른다면 다음 글을 읽는것을 추천한다. 
`The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets <http://www.joelonsoftware.com/articles/Unicode.html>`_.
이 항목에서는 유니코드를 다루는 것이 나쁜 기억이 되지 않도록 기초적인 것들을 다룰 것이다.

자동 변환
--------------------

Flask는 유니코드 지원이 불편하지 않도록 다음을 가정하고 있다.

-   사이트의 모든 글은 UTF-8으로 인코딩되어있다.
-   ASCII 문자로만 이루어진 문자열 상수(literal strings)를 제외하고는 유니코드만을 이용할 것이다.
-   바이트 단위로 통신하는 프로토콜 내에선 인코딩과 디코딩이 이뤄진다.

이것이 무엇을 의미할까?

HTTP는 바이트단위로 통신한다. 프로토콜만이 아니라 서버에서 주소를 표시하는 방식(URI, URL) 또한 
그렇다. 하지만 HTTP를 통해 전송되는 HTML은 다양한 문자 집합(Character set)을 지원하고 어느
집합을 쓰는지는 HTTP 헤더를 통해 전달된다. 이 과정이 복잡하지 않도록 Flask는 유니코드를 발송할 일이
생기면 UTF-8으로 인코딩 하여 발송할 것이라 가정한다. Flask가 인코딩과 헤더 설정을 대신해줄 것이다.

SQLAlchemy나 비슷한 ORM를 이용해 데이터베이스와 통신할 때도 마찬가지이다. 일부 데이터베이스는
유니코드를 송수신하는 프로토콜을 갖추고 있고, 만일 그렇지 않더라도 SQLAlchemy나 다른 ORM이
해결해 줄 것이다.

황금률
---------------

경험에 의한 규칙: binary 데이터를 다루는 것이 아니면 유니코드를 이용하라. Python 2.x에서
유니코드를 쓰는 것은 무엇을 의미하는가?

-   ASCII 문자(숫자, 몇개의 특수문자, 움라우트(umlaut)가 없는 로마자)를 쓰는 한 문자열 상수
    (string literals)를 쓸 수 있다. (``'Hello World'``) 
-   ASCII 문자 외의 글자들을 다루려면 그 문자열이 유니코드 문자열이라는 것을 앞에 u를 붙이는 것으로
    표시를 해야 한다. (``u'안녕 세상!'`` 처럼 말이다.)
-   유니코드가 아닌 문자를 Python 파일에서 이용할 경우 Python에 어떤 인코딩을 쓰는지 알려야 한다.
    이러한 이유에 의해 UTF-8을 이용하는 것을 추천한다. 인터프리터에게 인코딩을 알리기 위해
    ``# -*- coding: utf-8 -*-`` 를 소스코드의 첫 번째나 두 번째 줄에 넣을 수 있다.
-   Jinja는 template 파일들을 UTF-8으로 디코딩하도록 설정되어 있다. 그러니 쓰고 있는 편집기도
    저장할 때에 UTF-8으로 저장하도록 하길 바란다.

직접 인코딩 및 디코딩하기
------------------------------

파일 시스템이라던가 유니코드를 지원하지 않는 인터페이스와 통신할 때는 올바르게 디코드했는지를 보장해 야한다.
예를 들어 파일을 읽어 들여 Jinja2에 적용하려면 그 파일을 인코딩에 맞게 디코드해야 한다. 여기서 text
파일은 인코딩이 명시하지 않는 문제점이 발생한다. 그러니 모든 text파일도 UTF-8로 인코딩하는 것으로 수고를
덜길 바란다.

어쨌든 유니코드를 쓰는 파일을 읽기 위해 내장 메소드(built-in method)인 :meth:`str.decode`를 
쓸 수 있다::

    def read_file(filename, charset='utf-8'):
        with open(filename, 'r') as f:
            return f.read().decode(charset)

유니코드에서 UTF-8 같은 특정한 문자 집합(character set)으로 바꾸려면 :meth:`unicode.encode`를
쓸 수 있다::

    def write_file(filename, contents, charset='utf-8'):
        with open(filename, 'w') as f:
            f.write(contents.encode(charset))

편집기 설정하기
-------------------

요즘 대부분의 편집기들이 기본적으로 UTF-8으로 설정되어 있으나 그렇지 않은 경우 설정을 바꿔 줘야 한다.
다음은 UTF-8으로 저장하도록 설정하는 방법들이다:

-   Vim: ``.vimrc`` 파일에 ``set enc=utf-8`` 를 넣는다.

-   Emacs: 인코딩 쿠키(encoding cookie)를 쓰거나 다음을 ``.emacs`` 파일에 넣는다::

        (prefer-coding-system 'utf-8)
        (setq default-buffer-file-coding-system 'utf-8)

-   Notepad++:

    1. *설정->환경설정* (*Settings -> Preferences ...*)으로 간다.
    2. "새 문서 / 기본 디렉터리" ("New Document/Default Directory") 탭을 고른다.
    3. "UTF-8(BOM 없음)" ("UTF-8 without BOM")을 고른다.
    
    또 개행문자를 Unix 형식에 맞추는 것을 권장한다. 같은 설정 창에서 고를 수 있으나 필수 사항은 아니다.
