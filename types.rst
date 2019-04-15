.. index:: type

.. _types:

*****
타입
*****

solidity는 컴파일 시점에 각 변수(상태변수와 지역변수)의 타입이 명시되어야하는
(또는 최소한 추론가능해야하는 - :ref:`type-deduction` 참조) 정적 타입 언어입니다.
solidity는 몇 가지의 기본 타입을 제공하며 이를 조합해서 복합 타입을 만들 수 있습니다.

또한, 타입은 연산자가 포함된 표현식에서 서로 상호작용할 수 있습니다.
여러 가지 연산자에 대한 내용은  :ref:`order` 를 참조하세요. 

.. index:: ! value type, ! type;value

값 타입
===========

다음의 타입들은 변수가 전달될 때 값(value)이 전달되므로 값 타입이라고도 불립니다.
즉, 이 타입이 함수의 인자로 사용되거나 할당값으로 사용될 땐, 값이 복사됩니다.

.. index:: ! bool, ! true, ! false

Booleans
--------

``bool``: 가능한 값은 상수 ``true`` 그리고 ``false`` 입니다.

연산자:

*  ``!`` (논리 부정)
*  ``&&`` (논리 AND, "and")
*  ``||`` (논리 OR, "or")
*  ``==`` (같음)
*  ``!=`` (같지 않음)

``||`` 과 ``&&`` 에는 일반적인 short-circuiting rules이 적용됩니다.
이것은 ``f(x) || g(y)`` 에서 만약 ``f(x)`` 가 ``true`` 라면,   
``g(y)`` 의 값을 확인하지 않는다면 부작용이 있을 수 있음에도 불구하고 값을 확인하지 않는것을 의미합니다.

.. index:: ! uint, ! int, ! integer

정수
--------

``int`` / ``uint``: 다양한 크기의 부호있는 정수 타입, 부호없는 정수 타입이 존재합니다.
``uint8`` 에서 ``uint256`` 까지, 그리고 ``int8`` 부터 ``int256`` 까지 8비트 단위로 키워드가 존재합니다.
``uint`` 와 ``int`` 는 각각 ``uint256`` 와 ``int256`` 의 별칭입니다.

연산자:

* 비교 연산자: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>`` (``bool`` 결과값을 가짐)
* 비트 연산자: ``&``, ``|``, ``^`` (배타적 비트 or), ``~`` (비트 보수)
* 산술 연산자: ``+``, ``-``, 단항 ``-``, 단항 ``+``, ``*``, ``/``, ``%`` (나머지), ``**`` (거듭제곱), ``<<`` (왼쪽 시프트), ``>>`` (오른쪽 시프트)

나눗셈의 결과는 항상 정수이며 소수부분은 절사됩니다(EVM의 ``DIV`` opcode로 컴파일 됩니다).
그러나 두 연산자가 :ref:`literals<rational_literals>` (또는 리터럴 표현식)인 경우 소수부분은 절사되지 않습니다.

0으로 나누거나 0으로 모듈로 연산을 하면 런타임 예외가 발생합니다.

시프트 연산 결과의 타입은 왼쪽 피연산자의 타입을 따릅니다.
``x << y`` 는 ``x * 2**y`` 와 동일하며, ``x >> y`` 는 ``x / 2**y`` 와 동일합니다.
이는 음수를 시프트하는 경우 부호가 확장됨을 의미합니다.(This means that shifting negative numbers sign extends.)
음수만큼 시프트 연산을 실행하는 경우 런타임 예외가 발생합니다.

.. warning::
    부호있는 음의 정수를 우측 시프트 연산 한 결과값은 다른 프로그래밍 언어에서의 결과값과 다릅니다.
    solidity에서는, 우측 시프트는 나눗셈과 매핑되며 그로 인해 시프트된 음의 값은 0으로 반올림되어 갑니다(절사).
    다른 프로그래밍 언어에서는, 음의 값을 우측 시프트연산 하는 경우, 나눗셈과 소수점 이하 버림이 동시에 작동하는것과 유사하게 동작합니다(음의 무한대 방향).

.. index:: ! ufixed, ! fixed, ! fixed point number

부동 소수점 숫자
-------------------

.. warning::
    고정 소수점 수는 아직 solidity에서 완벽하게 지원되지 않습니다. 고정 소수점 수는 선언될 수는 있지만 할당될 수는 없습니다.

``fixed`` / ``ufixed``: 다양한 크기의 부호있는 고정 소수점, 부호없는 고정 소수점 타입이 존재합니다.
키워드 ``ufixedMxN`` 와 ``fixedMxN`` 에서 ``M`` 은 타입에 의해 취해진 비트의 수를 나타내며 ``N`` 은 소수점이하 자리수를 나타냅니다.
``M`` 은 8에서 256비트 사이의 값이며 반드시 8로 나누어 떨어져야 합니다.
``N`` 은 0과 80 사이의 값이어야만 합니다.
``ufixed`` 와 ``fixed`` 는 각각 ``ufixed128x19`` 와 ``fixed128x19`` 의 별칭입니다.

연산자:

* 비교 연산자: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>`` (``bool`` 결과값을 가짐)
* 산술 연산자: ``+``, ``-``, 단항 ``-``, 단항 ``+``, ``*``, ``/``, ``%`` (나머지)

.. note::
    부동 소수점 수와 고정 소수점 수의 주요한 차이점은, 부동 소수점 수는 정수와 소수점 부분을 표현하기 위해 사용되는 비트의 수가 유동적인데 반해, 고정 소수점의 경우 엄격히 정의되어 있습니다.
    일반적으로, 부동 소수점 방식에서는 거의 모든 공간이 소수 부분을 나타내기 위해 사용되지만 고정 소수점 방식에서는 적은 수의 비트만이 소수 부분을 정의하는데 사용됩니다.

.. index:: address, balance, send, call, callcode, delegatecall, transfer

.. _address:

Address
-------

``address`` : 20바이트(이더리움 address의 크기)를 담을 수 있습니다. address 타입에는 멤버가 있으며 모든 컨트랙트의 기반이 됩니다.


연산자:

* ``<=``, ``<``, ``==``, ``!=``, ``>=`` and ``>``

.. note::
    0.5.0으로 시작하는 버전의 컨트랙트는 address 타입에서 파생되지 않았지만, address 타입으로 명시적 변환될 수 있습니다.

.. _members-of-addresses:

address의 members
^^^^^^^^^^^^^^^^^^^^

* ``balance`` 와 ``transfer``

빠르게 훑으려면 :ref:`address_related` 를 참조하세요.

``balance`` 속성을 이용하여 address의 잔고를 조회하고 ``transfer`` 함수를 이용하여 다른 address에 Ether를 (wei 단위로) 보낼 수 있습니다:

::

    address x = 0x123;
    address myAddress = this;
    if (x.balance < 10 && myAddress.balance >= 10) x.transfer(10);

.. note::
    ``x`` 가 컨트랙트 address인 경우, 코드(더 구체적으로는: fallback 함수가 존재하는 경우)는 ``transfer`` 호출과 함께
     실행될 것입니다(이건 EVM의 특성이며 막을 수 없습니다).
     코드가 실행될때 가스가 부족하거나 어떤식으로든 실패한다면, Ether 전송은 원상복구되며 현재의 컨트랙트는 예외를 발생하며 중지됩니다.
     
* ``send``

Send는 low-level 수준에서 ``transfer`` 에 대응됩니다. 실행이 실패하면 컨트랙트는 중단되지 않고 대신 ``send`` 가 ``false`` 를 반환할 것입니다.

.. warning::
    ``send`` 를 사용할 땐 몇가지 주의사항이 있습니다: call stack의 깊이가 1024라면 전송은 실패하며(이것은 항상 호출자에 의해 강제 될 수 있습니다) 그리고
    수신자의 gas가 전부 소모되어도 실패합니다. 그러므로 안전한 Ether 전송을 위해서, 항상 ``send`` 의 반환값을 확인하고, ``transfer`` 를 사용하세요: 혹은 더 좋은 방법은 수신자가 돈을 인출하는 패턴을 사용하는 것입니다. 

* ``call``, ``callcode`` 그리고 ``delegatecall``

또한, ABI를 준수하지 않는 컨트랙트와 상호작용하기 위하여 임의 숫자의 인자를 취하는 ``call`` 함수가 제공되며 인자의 타입 역시 모든 타입을 취할 수 있습니다.
이러한 인자는 32바이트가 될 때까지 채워지고 연결됩니다. 한 가지 예외는 첫 번째 인자가 정확히 4바이트로 인코딩 되는 경우입니다.
이 경우에는, 함수 서명이 사용되도록 하기 위해 인자가 채워지지 않습니다.

::

    address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
    nameReg.call("register", "MyName");
    nameReg.call(bytes4(keccak256("fun(uint256)")), a);

``call`` 은 호출된 함수가 종료되었는지(``true``) 아니면 EVM 예외를 발생시켰는지를(``false``) 나타내는 boolean을 반환합니다.
반환된 데이터 자체에 접근하는건 불가능합니다(이를 위해선 우리는 인코딩과 크기를 미리 알고 있어야 합니다).

``.gas ()`` 제어자를 사용하여 제공된 가스를 조절할 수 있습니다::

    namReg.call.gas(1000000)("register", "MyName");

이와 유사하게, 제공된 Ether의 값도 역시 조절할 수 있습니다::

    nameReg.call.value(1 ether)("register", "MyName");

마지막으로, 이 제한자들은 함께 사용할 수 있으며 순서는 상관 없습니다::

    nameReg.call.gas(1000000).value(1 ether)("register", "MyName");

.. note::
    현재로서는 오버로딩된 함수에서 가스 제한자나 값 제한자를 사용할 수 없습니다.
    
    이를 위한 해결책은 가스와 값에 관해 특수한 경우가 있다는걸 소개하고 다시 한번 더 문제를 일으키지 않는지 확인하는 것입니다.

이와 유사하게, 함수 ``delegatecall`` 을 사용할 수 있습니다:
차이점은, 주어진 주소에선 오직 코드만 사용되고, 다른 것들(저장소, 잔고, ...)은 현재 컨트랙트의 것을 사용합니다.
``delegatecall`` 의 목적은 다른 컨트랙트에 저장된 라이브러리 코드를 사용하기 위함입니다.
사용자는 양쪽 컨트랙트의 저장소 레이아웃이 delegatecall을 통해 사용되기 적합한지 반드시 확인해야 합니다.
homestead 단계 전까지는, ``callcode`` 라고 불리는 delegatecall의 제한된 변형형태만이 이용가능했는데,
이 변형된 형태는 ``msg.sender`` 와 ``msg.value`` 에 접근하는 기능을 제공하지 않았습니다.

세 가지 함수 ``call``, ``delegatecall`` 및 ``callcode`` 는 매우 low-level 함수이므로 Solidity의 타입 안전성을 깨뜨리니 *최후의 수단* 으로서만 사용해야합니다.

``.gas ()`` 옵션은 세 가지 메소드 모두에서 사용할 수 있지만, ``.value ()`` 옵션은 ``delegatecall`` 에서 사용할 수 없습니다.

.. note::
    모든 컨트랙트는 address의 멤버를 상속하므로, ``this.balance`` 를 이용하여 현재 컨트랙트의 잔액을 조회하는것이 가능합니다.

.. note::
    ``callcode`` 는 추후 버전에서 제거 될 예정이라 사용을 권장하지 않습니다.

.. warning::
    이 함수들은 low-level 함수이므로 주의해서 사용해야 합니다.
    특히 알지 못하는 컨트랙트는 위험할 수 있으며 만약 이를 호출할 경우
    해당 컨트랙트에 대한 제어 권한을 넘겨주므로 호출이 반환될 때 상태 변수를 변경할 수 있습니다.

.. index:: byte array, bytes32


고정 크기 바이트 배열
----------------------

``bytes1``, ``bytes2``, ``bytes3``, ..., ``bytes32``. ``byte`` is an alias for ``bytes1``.

연산자:

* 비교 연산자: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>`` (``bool`` 결과값을 가짐)
* 비트 연산자: ``&``, ``|``, ``^`` (배타적 비트 or), ``~`` (비트 보수), ``<<`` (왼쪽 시프트), ``>>`` (오른쪽 시프트)
* 인덱스 접근: 만약 ``x`` 가 ``bytesI`` 타입이라면,  ``0 <= k < I`` 일때 ``x[k]`` 는 ``k`` 번째 바이트를 반환한다(읽기 전용).

시프트 연산자는 몇 비트만큼 이동할건지를 나타내는 오른쪽 피연산자로 모든 정수를 취할 수 있습니다(그렇지만 왼쪽 피연산자의 타입을 반환합니다).
음수만큼 시프트하는 경우 런타임 예외가 발생합니다.

Members:

* ``.length`` 는 바이트 배열의 고정된 길이를 반환합니다(읽기 전용).

.. note::
    바이트 배열은 ``byte[]`` 로도 사용이 가능하지만, 이럴 경우 각 요소마다 정확히 31바이트의 공간을 낭비하게됩니다. ``bytes`` 를 사용하는것이 더 낫습니다.

동적 크기 바이트 배열
----------------------------

``bytes``:
    동적 크기 바이트 배열, :ref:`arrays` 을 참조하세요. 값 타입이 아닙니다!

``string``:
    동적 크기의 UTF-8 인코딩된 문자열, :ref:`arrays` 을 참조하세요. 값 타입이 아닙니다!

경험에 따르면 임의 길이의 원시 바이트 데이터의 경우에는 ``bytes`` 를 사용하고
임의 길이의 문자열(UTF-8) 데이터의 경우에는 ``string`` 을 사용하세요.
만약 길이를 특정 바이트만큼 제한할수 있다면, 항상 ``bytes1`` 에서 ``bytes32`` 중 하나를 사용하세요. 왜냐하면 공간을 더 절약할 수 있기 때문입니다.

.. index:: address, literal;address

.. _address_literals:

Address 리터럴
----------------

address 체크섬 테스트를 통과한 16진수 리터럴(예를 들면 ``0xdCad3a6d3569DF655070DEd06cb7A1b2Ccd1D3AF``)은 ``address`` 타입입니다.
체크섬 테스트를 통과하지 못한 39자리 ~ 41자리 길이의 16진수 리터럴은 경고를 발생시키고 일반적인 유리수 리터럴로 취급됩니다.

.. note::
    혼합 케이스 address의 체크섬 형식은 `EIP-55 <https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md>`_ 에 정의되어 있습니다.

.. index:: literal, literal;rational

.. _rational_literals:

유리수 리터럴 및 정수 리터럴
-----------------------------

정수 리터럴은 0-9 범위의 일련의 숫자로 구성됩니다.
정수 리터럴은 십진법으로 나타내어집니다. 예를 들어, ``69`` 는 육십구를 의미합니다.
8진법 리터럴은 solidity에 존재하지 않으며 선행 0은 유효하지 않습니다.

소수점 이하 리터럴은 한쪽에 적어도 하나의 숫자가 있을때 ``.`` 에 의해 구성됩니다. 예로는 ``1.``, ``.1``, ``1.3`` 이 있습니다.

소수가 밑이 될 수 있지만 지수는 될 수 없는 과학적 표기법 또한 지원됩니다.
``2e10``, ``-2e10``, ``2e-10``, ``2.5e1`` 같은 예가 있습니다.

숫자 리터럴 표현식은 리터럴이 아닌 타입으로 변환될때까지(즉, 리터럴이 아닌 표현식과 함께 사용되는 경우) 임의 정밀도를 유지합니다.
이는 계산이 수행될때 오버플로우가 발생하지 않으며 나눗셈이 수행될때 자릿수를 잘라내지 않는걸 의미합니다.

예를 들어, ``(2**800 + 1) - 2**800`` 의 결과는 비록 중간 결과값이 machine word size에 적합하지 않을지라도 상수 ``1`` (``uint8`` 타입)입니다.
게다가 ``.5 * 8`` 의 결과값은 (비록 중간에 정수가 아닌 숫자가 사용되었을지라도) 정수 ``4`` 입니다.

정수에 사용할 수 있는 연산자는 피연산자가 정수인 숫자 리터럴 표현식에도 사용할 수 있습니다.
(피연산자) 둘 중 하나라도 소수일 경우에는, 비트 연산이 허용되지 않으며 지수가 소수일 경우에도 지수 연산이 허용되지 않습니다(무리수가 발생할 수 있으므로).

.. note::
    solidity는 각 유리수에 대해 숫자 리터럴 타입을 가집니다.
    정수 리터럴과 유리수 리터럴은 숫자 리터럴 타입에 속합니다.
    또한 모든 숫자 리터럴 표현식(즉, 오직 숫자 리터럴과 연산자로만 구성된 표현식)은 숫자 리터럴 타입에 속합니다.
    그러므로 숫자 리터럴 표현식  ``1 + 2`` 와 ``2 + 1`` 모두 유리수 3에 대해 같은 숫자 리터럴 타입에 속합니다.

.. warning::
    이전 버전에서는 정수 리터럴에 대한 나눗셈의 결과에서 자릿수를 버렸지만, 현재는 유리수로 변환됩니다. 즉 ``5 / 2`` 는 ``2`` 가 아니라 ``2.5`` 입니다.

.. note::
    숫자 리터럴 표현식은 리터럴이 아닌 표현식과 함께 사용되는 즉시 리터럴이 아닌 타입으로 변환됩니다.
    비록 우리는 다음 예제에서 ``b`` 에 할당된 값이 정수로 평가된다는걸 알고 있지만, 부분 표현식 ``2.5 + a`` 은 타입 검사를 하지 않으며 코드는 컴파일되지 않습니다.

::

    uint128 a = 1;
    uint128 b = 2.5 + a + 0.5;

.. index:: literal, literal;string, string

문자열 리터럴
---------------

문자열 리터럴은 큰따옴표나 작은따옴표와 함께 작성됩니다(``"foo"`` 또는 ``'bar'``).
solidity에선 C에서 처럼 trailing zeroes를 포함하진 않습니다;
``"foo"`` 는 4바이트가 아닌 3바이트를 차지합니다.
정수 리터럴과 마찬가지로, 문자열 리터럴의 타입은 다양하며 ``bytes1``, ..., ``bytes32`` 로 암시적 변환될 수 있습니다.
적합한 크기라면 ``bytes`` 와 ``string`` 으로도 변환될 수 있습니다.

문자열 리터럴은 ``\n``, ``\xNN``, ``\uNNNN`` 와 같은 escape characters을 지원합니다.
``\xNN`` 은 16진수 값을 취해 적절한 바이트를 삽입하는 반면  ``\uNNNN`` 은 Unicode codepoint를 취해 UTF-8 sequence를 삽입합니다.

.. index:: literal, bytes

16진수 리터럴
--------------------

16진수 리터럴은 키워드 ``hex`` 가 접두사로 붙고 큰따옴표나 작은따옴표로 둘러싸여집니다(``hex"001122FF"``).
내용은 16진수 문자열이어야 하며 값은 바이너리로 표현됩니다.

16진수 리터럴은 문자열 리터럴과 같이 동작하기에 동일하게 변경에 제한이 있습니다.

.. index:: enum

.. _enums:

Enums
-----

열거형은 solidity에서 사용자 정의 타입을 만드는 한 가지 방법입니다.
열거형은 모든 정수타입으로/정수타입에서 명시적 변환이 가능하지만 암시적 변환은 허용되지 않습니다.
명시적 변환은 런타임때 값 범위를 체크하고 실패시 예외를 발생시킵니다.
열거형은 최소 하나의 멤버를 필요로 합니다.

::

    pragma solidity ^0.4.16;

    contract test {
        enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
        ActionChoices choice;
        ActionChoices constant defaultChoice = ActionChoices.GoStraight;

        function setGoStraight() public {
            choice = ActionChoices.GoStraight;
        }

        // enum 타입은 ABI의 파트가 아니므로, "getChoice의 서명은 Solidity의 모든 외부적인 문제를 위한  
        // "getChoice() returns (uint8)"로  변경 될 것입니다. 사용되어지는 integer 타입은 
        // 모든 enum의 값들을 충분히 담을 만큼의 크기를 가집니다. (더 많은 값들을 가진다면 `uint16` 등이 사용될 것입니다.) 

        function getChoice() public view returns (ActionChoices) {
            return choice;
        }

        function getDefaultChoice() public pure returns (uint) {
            return uint(defaultChoice);
        }
    }

.. index:: ! function type, ! type; function

.. _function_types:

함수 타입
--------------

함수 타입입니다.
함수 타입의 변수는 함수에서 할당 될 수 있으며 함수 타입의 함수매개변수는 함수가 호출될 때, 함수를 전달하거나 반환하는데 사용될 수 있습니다.
함수 타입에는 두 종류가 있습니다 - *내부* 및 *외부* 함수 입니다:

내부 함수는 오직 현재 컨트랙트의 내부에서만(더 구체적으로는, 내부 라이브러리 함수와 상속받은 함수를 포함한 현재 코드 유닛 내부에서만) 호출될 수 있습니다.
왜냐하면 내부 함수는 현재 컨트랙트의 컨텍스트 밖에서 실행될 수 없기 때문입니다.
내부 함수를 호출하는것은 마치 현재 컨트랙트의 함수를 내부적으로 호출할떄와 마찬가지로 entry label로 가서 실현됩니다.

외부 함수는 address와 함수 서명으로 구성되며 외부 함수 호출을 통해 전달되고 반환 될 수 있습니다.

함수 타입은 다음과 같이 표기됩니다::

    function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]

매개변수 타입과 달리, 반환 타입은 비워 둘 수 없습니다.
함수 타입이 아무것도 반환하지 않는다면 ``returns (<return types>)`` 이 부분 전체를 생략해야 합니다.

기본적으로, 함수 타입은 내부함수이므로, ``internal`` 키워드는 생략 가능합니다.
반대로, 컨트랙트 함수 자체는 기본적으로 public이며 타입의 이름으로 사용될 때만 기본값이 internal 입니다.

현재 컨트랙트에서 함수에 접근하는 방법은 두가지가 있습니다:
``f`` 이렇게 직접 이름을 사용하거나, ``this.f`` 이런식으로 접근할 수 있습니다.
전자는 내부함수, 후자는 외부함수가 될 것입니다.

함수 타입 변수가 초기화 되지 않은 상태에서, 이를 호출하면 예외가 발생합니다.
함수에 ``delete`` 를 사용 후 그 함수를 호출하는 경우에도 동일하게 예외가 발생합니다.

외부 함수 타입이 solidity 컨텍스트의 외부에서 사용되는 경우,
이들은 뒤에 함수 식별자가 붙는 address를 단일 ``bytes24`` 타입으로 인코딩하는 ``function`` 으로 취급됩니다.

현재 컨트랙트의 퍼블릭 함수는 내부 함수로도 외부함수로도 사용될 수 있습니다.
``f`` 를 내부 함수로 사용하려면, ``f`` 만 사용하고 외부 함수로 사용하려면  ``this.f`` 로 사용하세요. 

또한 퍼블릭 (또는 external) 함수에는 :ref:`ABI function selector <abi_function_selector>` 를 반환하는 
특수한 멤버 `selector` 가 있습니다.

    pragma solidity ^0.4.16;

    contract Selector {
      function f() public view returns (bytes4) {
        return this.f.selector;
      }
    }

내부 함수 타입을 사용하는 방법을 보여주는 예제::

    pragma solidity ^0.4.16;

    library ArrayUtils {
      // 내부 함수와 내부 라이브러리 함수 들은 같은 코드 콘텍스트의 한 부분이 될 것이므로, 
      // 내부 함수들은 내부 라이브러리 함수안에서 사용될 수 있습니다.
      function map(uint[] memory self, function (uint) pure returns (uint) f)
        internal
        pure
        returns (uint[] memory r)
      {
        r = new uint[](self.length);
        for (uint i = 0; i < self.length; i++) {
          r[i] = f(self[i]);
        }
      }
      function reduce(
        uint[] memory self,
        function (uint, uint) pure returns (uint) f
      )
        internal
        pure
        returns (uint r)
      {
        r = self[0];
        for (uint i = 1; i < self.length; i++) {
          r = f(r, self[i]);
        }
      }
      function range(uint length) internal pure returns (uint[] memory r) {
        r = new uint[](length);
        for (uint i = 0; i < r.length; i++) {
          r[i] = i;
        }
      }
    }

    contract Pyramid {
      using ArrayUtils for *;
      function pyramid(uint l) public pure returns (uint) {
        return ArrayUtils.range(l).map(square).reduce(sum);
      }
      function square(uint x) internal pure returns (uint) {
        return x * x;
      }
      function sum(uint x, uint y) internal pure returns (uint) {
        return x + y;
      }
    }

외부 함수 타입을 사용하는 또 다른 예제::

    pragma solidity ^0.4.21;

    contract Oracle {
      struct Request {
        bytes data;
        function(bytes memory) external callback;
      }
      Request[] requests;
      event NewRequest(uint);
      function query(bytes data, function(bytes memory) external callback) public {
        requests.push(Request(data, callback));
        emit NewRequest(requests.length - 1);
      }
      function reply(uint requestID, bytes response) public {
        // 여기에서 답장이 신뢰 되는 소스로 부터 왔는지를 체크합니다.
        requests[requestID].callback(response);
      }
    }

    contract OracleUser {
      Oracle constant oracle = Oracle(0x1234567); // 알려진 contract
      function buySomething() {
        oracle.query("USD", this.oracleResponse);
      }
      function oracleResponse(bytes response) public {
        require(msg.sender == address(oracle));
        // 데이타를 사용합니다.
      }
    }

.. note::
    람다함수나 인라인함수 역시 계획되어있지만 아직 지원되지 않습니다.

.. index:: ! type;reference, ! reference type, storage, memory, location, array, struct

참조 타입
==================
참조타입의 값은 다중의 다른 이름을 통해서 변경될 수 있습니다. value타입의 변수들은 어디에서든지 독립적인 복사본을 얻을 수 있는 것과는 대조적입니다.
이런 이유로, 참조타입들은 value타입보다 좀더 주의를 가지면서 다루어야 합니다. 현재 참조타입은 structs,array 와 mapping으로 구성되어 있습니다. 
참조타입을 사용한다면, 타입이 저장되어질 데이타 구역을 명시적으로 제공해 주어야 합니다:```memory```(함수호출에 생명주기를 가짐), ```storage```(state 변수가 저장될 위치), 
```calldata```(함수 아규먼트를 포함하고 있는 특별한 데이타 위치로, 외부 함수 호출 파라미터들을 위해서만 허용)

데이타의 위치를 변경하는 할당 또는 타입을 변경하는 것은 자동적인 복사를 하로록 할 것입니다. 반면에 같은 데이타 위치에 있는 할당들은 storage 타입일 경우에만 복사됩니다. 


데이터 위치
-------------

모든 참조 타입은, *arrays*와 *structs*, 추가적인 "데이타 위치"에 관한 annotation 이 있습니다.
세가지 데이타 위치로, ``memory``, ``storage`` 와 ``calldata``가 있습니다. Calldata는 외부 contract 함수들의 파라미터로만 유효하고, 이런 파라미터들의 위해 필요합니다.
Calldata는 변경할 수 없고, 함수 아규먼틀들이 저장되는 비영구적인 지역, 그리고 대부분 메모리와 비슷한 경향이 있습니다.

.. note::
    버전 0.5.0 이전에서는 데이타 위치가 생략되고 변수와 함수 타입 등의 종류에 따라 다른 기본 위치기 될 수 있지만, 
    이후 버전에서는 모든 복합 타입들은 데이타 위치가 명시적으로 주어져야 합니다.

.. _data-location-assignment:

데이터 위치와 데이터 할당 하기
Data location and assignment behaviour

데이터 위치들은 데이타의 영속을 위한것 뿐만 아니라, 할당의 의미와 연관이 있습니다.
Data locations are not only relevant for persistency of data, but also for the semantics of assignments:

* ``storage`` 와 ``memory`` (또는 ``calldata`` 로부터) 사이에서의 할당은 독립적인 복사본을 생성합니다.
* ``memory`` 로부터 ``memory`` 로의 할당은 참조를 생성합니다. 하나의 메모리 변수의 변화는 같은 값을 참조하고 있는 다른 메모리 변수들에서 보이는 것을 의미합니다.
* ``storage``에서 로칼 storage 변수로의 할당은 참조에 할당합니다.
* ``storage``로 모든 할당은 항상 복사를 합니다. 지역변수 자채가 참조자라도, state 변수 또는 storage 구조체 타입의 지역 변수 멤버에 할당하는 것이 예가 될 수 있습니다.

    pragma solidity >=0.4.0 <0.7.0;

    contract C {
        uint[] x; // x의 데이타 위치는 storage 입니다.

        // memoryArray의 위치는 memory 입니다.
        function f(uint[] memory memoryArray) public {
            x = memoryArray; //  전체 배열을 storage로 복사합니다.
            uint[] storage y = x; //  포인트를 할당하고 y의 위치는 storage 입니다.
            y[7]; // 여덟번째 요소를 돌려줍니다.
            y.length = 2; // x를 y를 통해서 변경합니다.
            delete x; // 배열을 비우고, y를 변경합니다.
            // 다음의 코드는 동작하지 않습니다; storage 안에 새로운 이름이 없는 임시 배열을 생성이 필요하지만, 
            // storage는 정적으로 할당되어집니다.
            // y = memoryArray;
            // 포인터가 "reset" 되었으므로 이것은 동작하지 않을뿐아니라, 가리킬 수 있는 의미있는 위치가 없습니다.
            // delete y;
            g(x); // x의 참조자를 다루는 h를 호출 
            h(x); // h를 호출하고 독립적인 임시 복제본을 메모리에 생성한다.
        }

        function g(uint[] storage) internal pure {}
        function h(uint[] memory) public pure {}
    }

.. index:: ! array

.. _arrays:

배열
------

배열은 컴파일시 고정 크기를 가질 수도 있고 동적인 크기를 가질 수도 있습니다.
스토리지 배열의 경우, 요소의 타입은 임의적일 수(즉, 다른 배열이 될 수도 있고, 매핑이나 구조체일수도 있음) 있습니다.
메모리 배열의 경우, 매핑이 될 수 없으며 만약 공개적으로 보여지는 함수의 인자라면 ABI 타입이어야 합니다.

크기는 ``k`` 로 고정되었고 요소의 타입은 ``T`` 인 배열은 ``T[k]`` 로 표시하며, 동적인 크기의 배열은 ``T[]`` 로 표시합니다.
예를 들자면, ``uint`` 타입을 요소로 하는 동적 크기 배열 5개로 구성된 배열은 ``uint[][5]`` 입니다(C와 같은 다른 언어들과 달리 행과 열의 표현이 바뀌어있음에 유의하세요).

인덱스는 0부터 시작하며 접근은 선언과는 반대되는 방식으로 작동합니다. 

세번째 동적 크기 배열의 두번째 uint에 접근하려면, ``x[2][1]`` 이렇게 하고, 세번째 동적 배열을 접근하려면, ``x[2]``를 사용하세요. 
``T[5] a``라는 배열이 될 수 있는 T타입의 배열이 있다면, ``a[2]``는 항상 ``T`` 타입입니다.

배열의 요소들은 맵핑과 구조체를 포함한 어떤 타입이든 될수 있습니다. 

``bytes`` 와 ``string`` 타입의 변수는 특별한 형태의 배열입니다.
``bytes`` 는 ``byte[]`` 와 유사하지만 calldata로 꽉 차여져 있습니다.
``string`` 은 ``bytes`` 와 동일하지만 (현재로서는) 길이나 인덱스 접근을 허용하지 않습니다

그러므로 ``bytes`` 는 언제나 ``byte[]`` 보다 우선순위로 고려되어야합니다. 더 저렴하기 때문입니다.

.. note::
    문자열 ``s`` 의 byte-representation에 접근하고자 한다면, ``bytes(s).length`` / ``bytes(s)[7] = 'x';`` 이렇게 하세요.
    개별 문자가 아닌 UTF-8의 low-level 바이트에 접근하고 있다는걸 명심하세요!

배열을 ``public`` 으로 생성하고 solidity가 :ref:`getter <visibility-and-getters>` 를 생성하도록 할 수 있습니다.
숫자 인덱스는 getter의 필수 매개 변수가 됩니다.

.. index:: ! array;allocating, new

메모리 배열 할당
^^^^^^^^^^^^^^^^^^^^^^^^

``new`` 키워드를 사용해 크기 변경이 가능한 배열을 메모리에 생성할 수 있습니다.
스토리지 배열과는 달리, ``.length`` 멤버에 값을 할당함으로써 메모리 배열의 크기를 변경하는것은 **불가능** 합니다.

::

    pragma solidity ^0.4.16;

    contract C {
        function f(uint len) public pure {
            uint[] memory a = new uint[](7);
            bytes memory b = new bytes(len);
            // Here we have a.length == 7 and b.length == len
            a[6] = 8;
        }
    }

.. index:: ! array;literals, !inline;arrays

배열 리터럴 / 인라인 배열
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

배열 리터럴은 표현식으로 작성된 배열이며 즉각적으로 변수에 할당되지 않습니다.

::

    pragma solidity ^0.4.16;

    contract C {
        function f() public pure {
            g([uint(1), 2, 3]);
        }
        function g(uint[3] _data) public pure {
            // ...
        }
    }

배열 리터럴의 타입은 고정된 크기를 가지는 메모리 배열이며 base type은 주어진 요소들의 공통 타입을 따릅니다.
``[1, 2, 3]`` 의 타입은 ``uint8[3] memory`` 입니다. 왜냐하면 정수 각각의 타입이 ``uint8`` 이기 때문입니다.
그렇기 때문에, 위 예제의 첫 번째 요소를 ``uint`` 로 변환해야 했습니다. 
현재로서는, 고정된 크기의 메모리 배열을 동적 크기의 메모리 배열에 할당할 수 없습니다.
즉, 다음과 같은 것은 불가능합니다:

::

    // This will not compile.

    pragma solidity ^0.4.0;

    contract C {
        function f() public {
            // The next line creates a type error because uint[3] memory
            // cannot be converted to uint[] memory.
            uint[] x = [uint(1), 3, 4];
        }
    }

이 제약사항은 추후에 제거될 예정이지만,  현재 이러한 제약사항으로 인해 배열이 ABI로 전달되는 방식에 따라 몇가지 문제가 발생합니다.

.. index:: ! array;length, length, push, !array;push

멤버
^^^^^^^

**length**:
    배열에는 요소의 갯수를 저장하기 위한 ``length`` 멤버가 존재합니다.
    (메모리가 아닌) 스토리지에 저장된 동적 배열은 ``length`` 멤버의 값을 변경하여 크기를 조절할 수 있습니다.
    현재 length를 벗어나는 요소에 접근을 시도한다고해서 크기의 조절이 자동으로 되는건 아닙니다.
    메모리 배열은 생성될 때 크기가 결정되며 크기의 변경은 불가능합니다(그러나 동적 배열의 경우, 런타임 매개변수에 따라 달라질 수 있습니다.).

**push**:
    동적 크기의 스토리지 배열과 ``bytes`` (``string`` 은 제외)는 ``push`` 라는 멤버 함수를 가지고 있습니다.
    이 함수는 배열의 끝에 요소를 추가하는데 사용됩니다.
    이 함수는 새로운 length를 반환합니다.

.. warning::
    외부 함수에서 배열의 배열을 사용하는건 아직 불가능합니다.

.. warning::
    EVM의 한계로 인해, 외부함수를 호출했을때 동적인 것을 반환하는건 불가능합니다.
    ``contract C { function f() returns (uint[]) { ... } }`` 내부의  함수 ``f`` 는
    web3.js에서 호출될 경우 무언가를 반환하겠지만 solidity에서 호출될 경우 반환이 불가능합니다.

    현재로선 유일한 해결 방법은 크기가 고정된(동적이지 않은) 거대한 크기의 배열을 사용하는 것입니다.

::

    pragma solidity ^0.4.16;

    contract ArrayContract {
        uint[2**20] m_aLotOfIntegers;
        // 아래의 배열은 동적배열들의 쌍이 아니라, 길이가 2로 고정된 크기의 동적배열입니다.
        bool[2][] m_pairsOfFlags;
        // newPairs는 메모리에 저장됩니다. - 함수 아규먼트를에 대한 기본
        function setAllFlagPairs(bool[2][] newPairs) public {
            // 스토리지 배열에 할당하는 것은 배열 전체를 교체하는 것입니다.
            m_pairsOfFlags = newPairs;
        }

        function setFlagPair(uint index, bool flagA, bool flagB) public {
            // 존재하지 않는 인텍들에 접근하는 것은 예외를 발생 시킵니다.
            m_pairsOfFlags[index][0] = flagA;
            m_pairsOfFlags[index][1] = flagB;
        }

        function changeFlagArraySize(uint newSize) public {
            // 새로운 크기가 작으면, 제거된 배열의 요소들이 정리되어진다
            m_pairsOfFlags.length = newSize;
        }

        function clear() public {
            // 배열들을 완벽하게 삭제합니다.
            delete m_pairsOfFlags;
            delete m_aLotOfIntegers;
            // 위와 동일한 효과
            m_pairsOfFlags.length = 0;
        }

        bytes m_byteData;

        function byteArrays(bytes data) public {
            // 바이트 배열("bytes")은 패팅없이 저장되어지기 때문에 다르지만, 
            // "uint8[]"와 동일하게 다루어 질 수 있습니다.
            m_byteData = data;
            m_byteData.length += 7;
            m_byteData[3] = byte(8);
            delete m_byteData[2];
        }

        function addFlag(bool[2] flag) public returns (uint) {
            return m_pairsOfFlags.push(flag);
        }

        function createMemoryArray(uint size) public pure returns (bytes) {
            // 동적 메모리 배열들은 `new`를 사용하여 생성됩니다. : 
            uint[2][] memory arrayOfPairs = new uint[2][](size);
            // 동적 바이트 배열을 생성합니다.
            bytes memory b = new bytes(200);
            for (uint i = 0; i < b.length; i++)
                b[i] = byte(i);
            return b;
        }
    }


.. index:: ! struct, ! type;struct

.. _structs:

구조체
-------

solidity는 아래의 예시처럼 구조체의 형식으로 새로운 타입을 정의하는 방법을 제공합니다.

::

    pragma solidity ^0.4.11;

    contract CrowdFunding {
        //두개의 필드와 함게 새로운 타입을 정의합니다. 
        struct Funder {
            address addr;
            uint amount;
        }

        struct Campaign {
            address beneficiary;
            uint fundingGoal;
            uint numFunders;
            uint amount;
            mapping (uint => Funder) funders;
        }

        uint numCampaigns;
        mapping (uint => Campaign) campaigns;

        function newCampaign(address beneficiary, uint goal) public returns (uint campaignID) {
            campaignID = numCampaigns++; // campaignID 는 반환되어질 변수입니다.
            // 새로운 구조체를 생성하고 스토리지에 저장합니다. mapping 타입을 생략했습니다.
            campaigns[campaignID] = Campaign(beneficiary, goal, 0, 0);
        }

        function contribute(uint campaignID) public payable {
            Campaign storage c = campaigns[campaignID];
            // 새로운 임시 메모리 구조체를 생성하고, 주어진 값으로 초기화 하였고, 스토리지로 복사였습니다.
            // 초기화 하기 위해서 Funder(msg.sender, msg.value)를 사용할 수 있습니다.
            c.funders[c.numFunders++] = Funder({addr: msg.sender, amount: msg.value});
            c.amount += msg.value;
        }

        function checkGoalReached(uint campaignID) public returns (bool reached) {
            Campaign storage c = campaigns[campaignID];
            if (c.amount < c.fundingGoal)
                return false;
            uint amount = c.amount;
            c.amount = 0;
            c.beneficiary.transfer(amount);
            return true;
        }
    }

컨트랙트는 크라우드펀딩에서의 계약에 필요한 모든 기능을 제공하진 않지만 구조체를 이해하는데 필요한 기본적인 개념을 포함합니다.
구조체 타입은 매핑과 배열의 내부에서 사용될 수 있으며 구조체 역시 내부에 매핑과 배열을 포함할 수 있습니다.

구조체는 매핑 멤버의 값 타입이 될 순 있지만, 구조체가 동일한 구조체 타입의 멤버를 포함할 순 없습니다.
구조체의 크기는 유한해야 하므로 이러한 제약이 필요한것이죠.

모든 종류의 함수에서, 어떻게 구조체 타입이 (기본 스토리지 데이터 위치의) 지역 변수에 할당되는지 유의하십시오.
이는 구조체를 복사(copy)하지 않고 참조(reference)만 저장하므로 지역 변수의 멤버에 할당하는것은 실제로 상태에 기록됩니다.

물론 ``campaigns[campaignID].amount = 0`` 처럼 지역 변수에 할당하지 않고도 구조체의 멤버에 직접 접근 할 수도 있습니다.

.. index:: !mapping

매핑
========

매핑 타입은 ``mapping(_KeyType => _ValueType)`` 와 같이 선언됩니다.
여기서 ``_KeyType`` 은 매핑, 동적 크기 배열, 컨트랙트, 열거형, 구조체를 제외한 거의 모든 유형이 될 수 있습니다.
``_ValueType`` 은 매핑 타입을 포함한 어떤 타입이든 될 수 있습니다.

매핑은 사실상 모든 가능한 키가 초기화되고 byte-representation이 모두 0인 값(타입의 :ref:`기본 값 <default-value>`)에 매핑되는 
`해시 테이블 <https://en.wikipedia.org/wiki/Hash_table>`_ 로 볼 수 있습니다.
이는 매핑과 해시테이블의 유사한 점이며 차이점은, 키 데이터는 실제로 매핑에 저장되지 않고 오직 ``keccak256`` 해시만이 값을 찾기 위해 사용됩니다.

이로 인해, 매핑에는 길이 또는 집합(set)을 이루는 키나 값의 개념을 가지고 있지 않습니다.

매핑은 상태변수(또는 내부 함수에서의 스토리지 참조 타입)에만 허용됩니다.

매핑을 ``public`` 으로 표시하고 solidity가 :ref:`getter <visibility-and-getters>` 를 생성토록 할 수 있습니다.
``_KeyType`` 은 getter의 필수 매개 변수이며 ``_ValueType`` 을 반환 합니다.

매핑 역시 ``_ValueType`` 이 될 수 있습니다. getter는 각각의 ``_KeyType`` 에 대하여 하나의 매개변수를 재귀적으로 가집니다.

::

    pragma solidity ^0.4.0;

    contract MappingExample {
        mapping(address => uint) public balances;

        function update(uint newBalance) public {
            balances[msg.sender] = newBalance;
        }
    }

    contract MappingUser {
        function f() public returns (uint) {
            MappingExample m = new MappingExample();
            m.update(100);
            return m.balances(this);
        }
    }


.. note::
  매핑은 iterable하진 않지만, 그 위에 자료구조(data structure)를 구현하는건 가능합니다.
  예시는 `iterable mapping <https://github.com/ethereum/dapp-bin/blob/master/library/iterable_mapping.sol>`_ 을 참조하세요.

.. index:: assignment, ! delete, lvalue

Operators Involving LValues
===========================

만약 ``a`` 가 LValue라면(즉, 할당 될 수 있는 변수 또는 무언가), 다음의 연산자를 약자로 사용할 수 있습니다:

``a += e`` is equivalent to ``a = a + e``. The operators ``-=``, ``*=``, ``/=``, ``%=``, ``|=``, ``&=`` and ``^=`` are defined accordingly. ``a++`` and ``a--`` are equivalent to ``a += 1`` / ``a -= 1`` but the expression itself still has the previous value of ``a``. In contrast, ``--a`` and ``++a`` have the same effect on ``a`` but return the value after the change.


``a += e`` 는 ``a = a + e`` 와 동일합니다. 
연산자 ``-=``, ``*=``, ``/=``, ``%=``, ``|=``, ``&=``, ``^=`` 역시 동일한 방식으로 정의됩니다.
``a++`` 와 ``a--`` 는 ``a += 1`` / ``a -= 1``와 동일하게 값을 변경시키지만, 표현식 자체는 여전히 ``a``의 변경이 일어나지 않은 값을 반환합니다.
이와 반대로, ``--a`` 와 ``++a`` 역시 ``a``의 값을 변화시키지만, 이 표현식은 변경된 값을 반환합니다.

delete
------

``delete a`` 는 타입의 초기 값을 ``a`` 에 할당합니다. 즉, 정수의 경우라면 ``a = 0`` 입니다.
배열에서도 사용될 수 있는데 이 경우, 길이가 0인 동적 배열이나 동일한 길이의 정적 배열의 모든 요소를 초기화합니다.
구조체에 사용할 경우, 구조체의 모든 멤버를 초기화합니다.

``delete`` 는 매핑에 아무런 영향을 미치지 못합니다(매핑의 키는 임의적이며 일반적으로 알려져있지 않기 때문입니다).
따라서 구조체를 delete할 경우, 매핑이 아닌 모든 멤버를 초기화하며 멤버의 내부도 매핑이 아니라면 재귀적으로 초기화합니다.
그러나, 개별 키 그리고 그 키가 어디에 매핑되었는지는 삭제될 수 있습니다.

``delete a`` 는 실제로 ``a`` 에 초기값을 할당하는것처럼 동작합니다. 즉, ``a`` 에 새로운 객체를 저장합니다. 

::

    pragma solidity ^0.4.0;

    contract DeleteExample {
        uint data;
        uint[] dataArray;

        function f() public {
            uint x = data;
            delete x; // x에 0으로 지정합니다. data의 값에 영향을 끼치지 않습니다.
            delete data; // data를 0으로 지정합니다. data의 복사본인 x의 값에 영향을 끼치지 않습니다.
            uint[] storage y = dataArray;
            delete dataArray; 
            // dataArray.length를 0으로 지정하지만, uint[] 는 복합객체 이므로 스토리지 객체의 별칭인 y는 영향을 받습니다. 
            // 반면에, "delete y" 유효하지 않습니다.  스토리지 객체들을 참조하는 지역 변수들은 이미 존재하고 있는 스토리지 객체들로 부터로만 할당 받을 수 있습니다.
            // 즉, "delete y" 존재하지 않는 스토리지 객체를 할당하는 시도입니다.
        
    }

.. index:: ! type;conversion, ! cast

기본 타입간의 변환
====================================

암시적 형변환
--------------------

피연산자가 서로 다른 타입이라면, 컴파일러는 하나의 피연산자를 다른 피연산자의 타입으로 암시적 형변환을 시도합니다(할당의 경우에도 마찬가지입니다).
일반적으로, 의미가 통하며 손실되는 정보가 없다면 value-type간 암시적 형변환이 가능합니다:
``uint8`` 는 ``uint16`` 로 암시적 형변환되며 ``int128`` 는 ``int256`` 로 암시적 형변환됩니다, 그러나 ``int8`` 는 ``uint256`` 으로 암시적 형변환될 수 없습니다(왜냐하면 ``uint256`` 는 ``-1`` 같은 값을 표현할 수 없기 때문입니다).
더욱이, 부호없는 정수는 같거나 큰 크기의 바이트로 변환 될 수 있지만 그 반대는 불가능합니다.
``uint160`` 로 변환 가능한 타입이라면 ``address`` 로도 변환될 수 있습니다.

명시적 형변환
--------------------

컴파일러가 암시적 형변환을 허용하지 않더라도 당신이 현재 무엇을 하고있는지 알고 있다면 명시적 형변환이 때때로 가능할 수 있습니다.
이는 예상치 않은 작동을 불러일으킬수 있으므로 확실히 당신이 원하는 결과가 나오는지 테스트해봐야 합니다!
음수 ``int8`` 을 ``uint`` 로 변환하는 다음의 예제를 보겠습니다:

::

    int8 y = -3;
    uint x = uint(y);

이 코드 조각의 끝에서, ``x`` 는 ``0xfffff..fd`` 값을 가질것이고, (64 hex characters) 이는 256 비트의 2의 보수 표현에서 -3입니다.

크기가 더 작은 타입으로 명시적 형변환 될 경우, 상위 비트가 잘려져 나갑니다::

    uint32 a = 0x12345678;
    uint16 b = uint16(a); // b will be 0x5678 now

.. index:: ! type;deduction, ! var

.. _type-deduction:

타입 추론
==============

편의상, 항상 변수의 타입을 명시적으로 지정할 필요는 없으며 컴파일러는 변수에 할당된 첫번째 표현식의 타입에서 자동으로 타입을 추론합니다::

    uint24 x = 0x123;
    var y = x;

여기서, ``y`` 의 타입은 ``uint24`` 가 될겁니다. ``var`` 은 함수 매개 변수나 반환 매개 변수에선 사용될 수 없습니다.

.. warning::
    첫 번째 할당에서만 타입이 추론되기에, i는 ``uint8`` 타입이고 이 타입의 가장 큰 값이 ``2000`` 보다 작기에 아래 코드 조각의 반복문은 무한반복문입니다.
    ``for (var i = 0; i < 2000; i++) { ... }``
