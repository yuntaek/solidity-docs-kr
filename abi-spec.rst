.. index:: abi, application binary interface

.. _ABI:

******************************************
어플리케이션 바이너리 인터페이스 설명
******************************************

기본 디자인
============

어플리케이션 바이너리 인터페이스는 블럭체인 밖에서든 컨트렉트 대 컨트렉트 상호작용이든, 이더리움 생태계에서 컨트렉트들과 상호작용 하는 표준 방법 입니다.
이 설명에 나와있는 것 같이, 타입에 따라 데이터는 인코딩 되어 집니다.
인코딩은 스스로 설명하지 않기때문에 디코드 하기 위해 스키마를 요구합니다.

컨트렉트의 인터페이스 함수는 컴파일 타임 및 정적으로 알려진 강력한 형식이라고 가정합니다. 내성 메커니즘은 제공되지 않습니다.
모든 컨트렉트에는 컴파일 타임에 사용할 수있는 모든 컨트렉트의 인터페이스 정의가 있다고 가정합니다.

이 설명은 인터페이스가 동적이거나 달리 런타임에만 알려져있는 계약을 다루지 않습니다. 이러한 사례가 중요 해지면 Ethereum 생태계 내에 구축 된 시설로 적절하게 처리 할 수 있습니다.

.. _abi_function_selector:

함수 선택자
=================

함수 호출을 위한 호출 데이터의 첫 4바이트는 호출될 함수를 지정합니다. 그것은 함수 시그니쳐의 Keccak (SHA-3) 해시의 첫 번째(빅 엔디안)에서 4바이트 입니다. 이 시그니쳐는 기본 프로토콜의 정식 표현으로써 정의됩니다.
매개변수 타입의 괄호로 묶인 목록이  있는 함수 이름. 매개 변수 타입은 공백을 사용하지 않고 단일 쉼표로 구분합니다.

.. note::
    함수의 반환 타입은 이 시그니쳐의 일부분이 아닙니다. :ref:`솔리디티 함수 오버로딩 <overload-function>`에서 반환타입은 고려하지 않습니다. 그이유는 함수 호출 해결을 컨텍스트 독립적으로 유지하기 위해서 입니다. 그러나 ABI에 대한 JSON 설명에는 입력과 출력이 모두 들어 있습니다. (the :ref:`JSON ABI <abi_json>`)를 확인하세요.

인자 인코딩
=================

다섯 번째 바이트부터 인코딩 된 인수가옵니다. 이 인코딩은 다른 장소에서도 사용됩니다 (예 : 반환 값과 이벤트 인수는 함수를 지정하는 4 바이트가없는 동일한 방식으로 인코딩됩니다.

타입
=====

아래에 같은 타입들이 존재합니다:

- ``uint<M>``: ``M`` 비트 크기인 부호가 없는 정수 타입 , ``0 < M <= 256``, ``M % 8 == 0``. 예) ``uint32``, ``uint8``, ``uint256``.

- ``int<M>``:  ``M`` 비트 크기인 2의 보수를 이용하는 부호가 있는 정수 타입 , ``0 < M <= 256``, ``M % 8 == 0``.

- ``address``: 가정된 해석과 언어 입력을 제외하고, ``uint160`` 와 동일 하다. 함수선택자를 계산시, ``address`` 가 사용된다..

- ``uint``, ``int``: 각각 ``uint256``, ``int256`` 와 동의어이다. 함수 선택자 계산시  ``uint256`` 와 ``int256`` 가 사용 되어져야 한다.

- ``bool``: 값이 1과 0으로 제한되어진 것 외에 ``uint8``과 동일하다. 함수 선택자 계산시, ``bool`` 이 사용된다.

- ``fixed<M>x<N>``: 부호가 있는 소수점 이하의 숫자가 고정된 수로 ``M`` 비트로 이루어진 타입, ``8 <= M <= 256``, ``M % 8 ==0``,과 ``0 < N <= 80``, 값을 의미하는 ``v`` 는 ``v / (10 ** N)`` 로 표현된다.

- ``ufixed<M>x<N>``:  ``fixed<M>x<N>``의 부호가 없는 타입

- ``fixed``, ``ufixed``: 각각 ``fixed128x19``, ``ufixed128x19``의 동의어 이다. 함수 선택자를 계산시  ``fixed128x19`` 와 ``ufixed128x19`` 을 사용해야만 한다..

- ``bytes<M>``: ``M`` 바이트의 바이너리 타입의 , ``0 < M <= 32``.

- ``function``: 함수 선택자(4 bytes)에 뒤에 오는 주소(20 bytes). ``bytes24``와 동일하게 인코딩 되어진다.

아래와 같은 (고정된 크기)배열 타입들이 존재합니다:

- ``<type>[M]``: 주어진 타입의 ``M`` 개의 요소들로 이루어진 고정된 길이의 배열, ``M > 0``

아래와 같은 가변 길이 타입 타입들이 존재합니다:

- ``bytes``: 가변 크기의 바이트 바이트 시퀀스.

- ``string``: UTF-8으로 인코딩으로 된것으로 간주하는 가변길이의 유니코드 문자열

- ``<type>[]``: 주어진 타입의 가변길이의 요소들로 이루어진 배열

타입들은 괄혼안에서 유한개 타입들을 ","로 구분 하여 묶을 수 있다.:

- ``(T1,T2,...,Tn)``: 타입 ``T1``, ..., ``Tn``, ``n >= 0``
 이루어진 튜플

튜플의 튜플, 튜플의 배열 등등의 형태를 갖을 수 있다.

.. note::
    솔리디티는 위의 나열된 모든 타입들 중 튜플을 제외하고 같은 이름을 지원합니다. ABI 튜플 타입은 인코딩된 솔리디티 ``structs``를 위해 이용되어진다.

인코딩의 공식 사양
====================================

우리는 공식적으로 인코딩을 규격화 할 것입니다. 다음의 따라는 요소들은 
We will now formally specify the encoding, such that it will have the following
properties, which are especially useful if some arguments are nested arrays:

Properties:
  1. 값에 접근하기 위해 필요한 읽기 횟수는 적어도 인자 배열 구조 안의 값의 깊이 이다. 즉, ``a_i[k][l][r]``를 읽기 위해 4회의 읽기가 필요한다.  
  ABI의 이전 버전에서, 최악의 경우의 읽기 횟수는 가변적인 파라미터의 총 갯수 만큼 순차적으로 증가되었다.
  2. 변수 또는 배열의 요소의 데이타는 다른 데이터와 (인터리빙)교차적으로 배치 될 수 없고, 재 배치 될 수 있다.  상대적인 "addresses"를 사용한다.

동적인 타입과 정적인 타입을 구분하였다. 정적인 타입은 현 블록 후에 그 자리에서 인코딩 되고, 동적인 타입들은 현재블록 다음의 분리되어 배정된 곳에서 인코딩 된다.

**정의:** 아래의 타입들은 "dynamic"이다:

* ``bytes``
* ``string``
* ``T[]`` for any ``T``
* ``T[k]`` for any dynamic ``T`` and any ``k > 0``
* ``(T1,...,Tk)`` if any ``Ti`` is dynamic for ``1 <= i <= k``

모든 다른 타입들을 정적이라 부른다.

**정의:** ``len(a)`` 바이너리 문자열 ``a``안에 바이트 길이이다.
``len(a)``의 타입은 ``uint256``로 간주되어 진다.

We define ``enc``, the actual encoding, as a mapping of values of the ABI types to binary strings such
that ``len(enc(X))`` depends on the value of ``X`` if and only if the type of ``X`` is dynamic.

**Definition:** For any ABI value ``X``, we recursively define ``enc(X)``, depending
on the type of ``X`` being

- ``(T1,...,Tk)`` for ``k >= 0`` and any types ``T1``, ..., ``Tk``

  ``enc(X) = head(X(1)) ... head(X(k-1)) tail(X(0)) ... tail(X(k-1))``

  where ``X(i)`` is the ``ith`` component of the value, and
  ``head`` and ``tail`` are defined for ``Ti`` being a static type as

    ``head(X(i)) = enc(X(i))`` and ``tail(X(i)) = ""`` (the empty string)

  and as

    ``head(X(i)) = enc(len(head(X(0)) ... head(X(k-1)) tail(X(0)) ... tail(X(i-1))))``
    ``tail(X(i)) = enc(X(i))``

  otherwise, i.e. if ``Ti`` is a dynamic type.

  Note that in the dynamic case, ``head(X(i))`` is well-defined since the lengths of
  the head parts only depend on the types and not the values. Its value is the offset
  of the beginning of ``tail(X(i))`` relative to the start of ``enc(X)``.

- ``T[k]`` for any ``T`` and ``k``:

  ``enc(X) = enc((X[0], ..., X[k-1]))``

  i.e. it is encoded as if it were a tuple with ``k`` elements
  of the same type.

- ``T[]`` where ``X`` has ``k`` elements (``k`` is assumed to be of type ``uint256``):

  ``enc(X) = enc(k) enc([X[1], ..., X[k]])``

  i.e. it is encoded as if it were an array of static size ``k``, prefixed with
  the number of elements.

- ``bytes``, of length ``k`` (which is assumed to be of type ``uint256``):

  ``enc(X) = enc(k) pad_right(X)``, i.e. the number of bytes is encoded as a
    ``uint256`` followed by the actual value of ``X`` as a byte sequence, followed by
    the minimum number of zero-bytes such that ``len(enc(X))`` is a multiple of 32.

- ``string``:

  ``enc(X) = enc(enc_utf8(X))``, i.e. ``X`` is utf-8 encoded and this value is interpreted as of ``bytes`` type and encoded further. Note that the length used in this subsequent encoding is the number of bytes of the utf-8 encoded string, not its number of characters.

- ``uint<M>``: ``enc(X)`` is the big-endian encoding of ``X``, padded on the higher-order (left) side with zero-bytes such that the length is 32 bytes.
- ``address``: as in the ``uint160`` case
- ``int<M>``: ``enc(X)`` is the big-endian two's complement encoding of ``X``, padded on the higher-order (left) side with ``0xff`` for negative ``X`` and with zero bytes for positive ``X`` such that the length is 32 bytes.
- ``bool``: as in the ``uint8`` case, where ``1`` is used for ``true`` and ``0`` for ``false``
- ``fixed<M>x<N>``: ``enc(X)`` is ``enc(X * 10**N)`` where ``X * 10**N`` is interpreted as a ``int256``.
- ``fixed``: as in the ``fixed128x19`` case
- ``ufixed<M>x<N>``: ``enc(X)`` is ``enc(X * 10**N)`` where ``X * 10**N`` is interpreted as a ``uint256``.
- ``ufixed``: as in the ``ufixed128x19`` case
- ``bytes<M>``: ``enc(X)`` is the sequence of bytes in ``X`` padded with trailing zero-bytes to a length of 32 bytes.

Note that for any ``X``, ``len(enc(X))`` is a multiple of 32.

Function Selector and Argument Encoding
=======================================

All in all, a call to the function ``f`` with parameters ``a_1, ..., a_n`` is encoded as

  ``function_selector(f) enc((a_1, ..., a_n))``

and the return values ``v_1, ..., v_k`` of ``f`` are encoded as

  ``enc((v_1, ..., v_k))``

i.e. the values are combined into a tuple and encoded.

Examples
========

Given the contract:

::

    pragma solidity ^0.4.16;

    contract Foo {
      function bar(bytes3[2]) public pure {}
      function baz(uint32 x, bool y) public pure returns (bool r) { r = x > 32 || y; }
      function sam(bytes, bool, uint[]) public pure {}
    }


Thus for our ``Foo`` example if we wanted to call ``baz`` with the parameters ``69`` and ``true``, we would pass 68 bytes total, which can be broken down into:

- ``0xcdcd77c0``: the Method ID. This is derived as the first 4 bytes of the Keccak hash of the ASCII form of the signature ``baz(uint32,bool)``.
- ``0x0000000000000000000000000000000000000000000000000000000000000045``: the first parameter, a uint32 value ``69`` padded to 32 bytes
- ``0x0000000000000000000000000000000000000000000000000000000000000001``: the second parameter - boolean ``true``, padded to 32 bytes

In total::

    0xcdcd77c000000000000000000000000000000000000000000000000000000000000000450000000000000000000000000000000000000000000000000000000000000001

It returns a single ``bool``. If, for example, it were to return ``false``, its output would be the single byte array ``0x0000000000000000000000000000000000000000000000000000000000000000``, a single bool.

If we wanted to call ``bar`` with the argument ``["abc", "def"]``, we would pass 68 bytes total, broken down into:

- ``0xfce353f6``: the Method ID. This is derived from the signature ``bar(bytes3[2])``.
- ``0x6162630000000000000000000000000000000000000000000000000000000000``: the first part of the first parameter, a ``bytes3`` value ``"abc"`` (left-aligned).
- ``0x6465660000000000000000000000000000000000000000000000000000000000``: the second part of the first parameter, a ``bytes3`` value ``"def"`` (left-aligned).

In total::

    0xfce353f661626300000000000000000000000000000000000000000000000000000000006465660000000000000000000000000000000000000000000000000000000000

If we wanted to call ``sam`` with the arguments ``"dave"``, ``true`` and ``[1,2,3]``, we would pass 292 bytes total, broken down into:

- ``0xa5643bf2``: the Method ID. This is derived from the signature ``sam(bytes,bool,uint256[])``. Note that ``uint`` is replaced with its canonical representation ``uint256``.
- ``0x0000000000000000000000000000000000000000000000000000000000000060``: the location of the data part of the first parameter (dynamic type), measured in bytes from the start of the arguments block. In this case, ``0x60``.
- ``0x0000000000000000000000000000000000000000000000000000000000000001``: the second parameter: boolean true.
- ``0x00000000000000000000000000000000000000000000000000000000000000a0``: the location of the data part of the third parameter (dynamic type), measured in bytes. In this case, ``0xa0``.
- ``0x0000000000000000000000000000000000000000000000000000000000000004``: the data part of the first argument, it starts with the length of the byte array in elements, in this case, 4.
- ``0x6461766500000000000000000000000000000000000000000000000000000000``: the contents of the first argument: the UTF-8 (equal to ASCII in this case) encoding of ``"dave"``, padded on the right to 32 bytes.
- ``0x0000000000000000000000000000000000000000000000000000000000000003``: the data part of the third argument, it starts with the length of the array in elements, in this case, 3.
- ``0x0000000000000000000000000000000000000000000000000000000000000001``: the first entry of the third parameter.
- ``0x0000000000000000000000000000000000000000000000000000000000000002``: the second entry of the third parameter.
- ``0x0000000000000000000000000000000000000000000000000000000000000003``: the third entry of the third parameter.

In total::

    0xa5643bf20000000000000000000000000000000000000000000000000000000000000060000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000000000464617665000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000003000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000003

Use of Dynamic Types
====================

A call to a function with the signature ``f(uint,uint32[],bytes10,bytes)`` with values ``(0x123, [0x456, 0x789], "1234567890", "Hello, world!")`` is encoded in the following way:

We take the first four bytes of ``sha3("f(uint256,uint32[],bytes10,bytes)")``, i.e. ``0x8be65246``.
Then we encode the head parts of all four arguments. For the static types ``uint256`` and ``bytes10``, these are directly the values we want to pass, whereas for the dynamic types ``uint32[]`` and ``bytes``, we use the offset in bytes to the start of their data area, measured from the start of the value encoding (i.e. not counting the first four bytes containing the hash of the function signature). These are:

 - ``0x0000000000000000000000000000000000000000000000000000000000000123`` (``0x123`` padded to 32 bytes)
 - ``0x0000000000000000000000000000000000000000000000000000000000000080`` (offset to start of data part of second parameter, 4*32 bytes, exactly the size of the head part)
 - ``0x3132333435363738393000000000000000000000000000000000000000000000`` (``"1234567890"`` padded to 32 bytes on the right)
 - ``0x00000000000000000000000000000000000000000000000000000000000000e0`` (offset to start of data part of fourth parameter = offset to start of data part of first dynamic parameter + size of data part of first dynamic parameter = 4\*32 + 3\*32 (see below))

After this, the data part of the first dynamic argument, ``[0x456, 0x789]`` follows:

 - ``0x0000000000000000000000000000000000000000000000000000000000000002`` (number of elements of the array, 2)
 - ``0x0000000000000000000000000000000000000000000000000000000000000456`` (first element)
 - ``0x0000000000000000000000000000000000000000000000000000000000000789`` (second element)

Finally, we encode the data part of the second dynamic argument, ``"Hello, world!"``:

 - ``0x000000000000000000000000000000000000000000000000000000000000000d`` (number of elements (bytes in this case): 13)
 - ``0x48656c6c6f2c20776f726c642100000000000000000000000000000000000000`` (``"Hello, world!"`` padded to 32 bytes on the right)

All together, the encoding is (newline after function selector and each 32-bytes for clarity):

::

    0x8be65246
      0000000000000000000000000000000000000000000000000000000000000123
      0000000000000000000000000000000000000000000000000000000000000080
      3132333435363738393000000000000000000000000000000000000000000000
      00000000000000000000000000000000000000000000000000000000000000e0
      0000000000000000000000000000000000000000000000000000000000000002
      0000000000000000000000000000000000000000000000000000000000000456
      0000000000000000000000000000000000000000000000000000000000000789
      000000000000000000000000000000000000000000000000000000000000000d
      48656c6c6f2c20776f726c642100000000000000000000000000000000000000

Events
======

Events are an abstraction of the Ethereum logging/event-watching protocol. Log entries provide the contract's address, a series of up to four topics and some arbitrary length binary data. Events leverage the existing function ABI in order to interpret this (together with an interface spec) as a properly typed structure.

Given an event name and series of event parameters, we split them into two sub-series: those which are indexed and those which are not. Those which are indexed, which may number up to 3, are used alongside the Keccak hash of the event signature to form the topics of the log entry. Those which are not indexed form the byte array of the event.

In effect, a log entry using this ABI is described as:

- ``address``: the address of the contract (intrinsically provided by Ethereum);
- ``topics[0]``: ``keccak(EVENT_NAME+"("+EVENT_ARGS.map(canonical_type_of).join(",")+")")`` (``canonical_type_of`` is a function that simply returns the canonical type of a given argument, e.g. for ``uint indexed foo``, it would return ``uint256``). If the event is declared as ``anonymous`` the ``topics[0]`` is not generated;
- ``topics[n]``: ``EVENT_INDEXED_ARGS[n - 1]`` (``EVENT_INDEXED_ARGS`` is the series of ``EVENT_ARGS`` that are indexed);
- ``data``: ``abi_serialise(EVENT_NON_INDEXED_ARGS)`` (``EVENT_NON_INDEXED_ARGS`` is the series of ``EVENT_ARGS`` that are not indexed, ``abi_serialise`` is the ABI serialisation function used for returning a series of typed values from a function, as described above).

For all fixed-length Solidity types, the ``EVENT_INDEXED_ARGS`` array contains the 32-byte encoded value directly. However, for *types of dynamic length*, which include ``string``, ``bytes``, and arrays, ``EVENT_INDEXED_ARGS`` will contain the *Keccak hash* of the encoded value, rather than the encoded value directly. This allows applications to efficiently query for values of dynamic-length types (by setting the hash of the encoded value as the topic), but leaves applications unable to decode indexed values they have not queried for. For dynamic-length types, application developers face a trade-off between fast search for predetermined values (if the argument is indexed) and legibility of arbitrary values (which requires that the arguments not be indexed). Developers may overcome this tradeoff and achieve both efficient search and arbitrary legibility by defining events with two arguments — one indexed, one not — intended to hold the same value.

.. _abi_json:

JSON
====

The JSON format for a contract's interface is given by an array of function and/or event descriptions.
A function description is a JSON object with the fields:

- ``type``: ``"function"``, ``"constructor"``, or ``"fallback"`` (the :ref:`unnamed "default" function <fallback-function>`);
- ``name``: the name of the function;
- ``inputs``: an array of objects, each of which contains:

  * ``name``: the name of the parameter;
  * ``type``: the canonical type of the parameter (more below).
  * ``components``: used for tuple types (more below).

- ``outputs``: an array of objects similar to ``inputs``, can be omitted if function doesn't return anything;
- ``payable``: ``true`` if function accepts ether, defaults to ``false``;
- ``stateMutability``: a string with one of the following values: ``pure`` (:ref:`specified to not read blockchain state <pure-functions>`), ``view`` (:ref:`specified to not modify the blockchain state <view-functions>`), ``nonpayable`` and ``payable`` (same as ``payable`` above).
- ``constant``: ``true`` if function is either ``pure`` or ``view``

``type`` can be omitted, defaulting to ``"function"``.

Constructor and fallback function never have ``name`` or ``outputs``. Fallback function doesn't have ``inputs`` either.

Sending non-zero ether to non-payable function will throw. Don't do it.

An event description is a JSON object with fairly similar fields:

- ``type``: always ``"event"``
- ``name``: the name of the event;
- ``inputs``: an array of objects, each of which contains:

  * ``name``: the name of the parameter;
  * ``type``: the canonical type of the parameter (more below).
  * ``components``: used for tuple types (more below).
  * ``indexed``: ``true`` if the field is part of the log's topics, ``false`` if it one of the log's data segment.

- ``anonymous``: ``true`` if the event was declared as ``anonymous``.

For example,

::

    pragma solidity ^0.4.0;

    contract Test {
      function Test() public { b = 0x12345678901234567890123456789012; }
      event Event(uint indexed a, bytes32 b);
      event Event2(uint indexed a, bytes32 b);
      function foo(uint a) public { Event(a, b); }
      bytes32 b;
    }

would result in the JSON:

.. code:: json

  [{
  "type":"event",
  "inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"bytes32","indexed":false}],
  "name":"Event"
  }, {
  "type":"event",
  "inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"bytes32","indexed":false}],
  "name":"Event2"
  }, {
  "type":"function",
  "inputs": [{"name":"a","type":"uint256"}],
  "name":"foo",
  "outputs": []
  }]

Handling tuple types
--------------------

Despite that names are intentionally not part of the ABI encoding they do make a lot of sense to be included
in the JSON to enable displaying it to the end user. The structure is nested in the following way:

An object with members ``name``, ``type`` and potentially ``components`` describes a typed variable.
The canonical type is determined until a tuple type is reached and the string description up
to that point is stored in ``type`` prefix with the word ``tuple``, i.e. it will be ``tuple`` followed by
a sequence of ``[]`` and ``[k]`` with
integers ``k``. The components of the tuple are then stored in the member ``components``,
which is of array type and has the same structure as the top-level object except that
``indexed`` is not allowed there.

As an example, the code

::

    pragma solidity ^0.4.19;
    pragma experimental ABIEncoderV2;

    contract Test {
      struct S { uint a; uint[] b; T[] c; }
      struct T { uint x; uint y; }
      function f(S s, T t, uint a) public { }
      function g() public returns (S s, T t, uint a) {}
    }

would result in the JSON:

.. code:: json

  [
    {
      "name": "f",
      "type": "function",
      "inputs": [
        {
          "name": "s",
          "type": "tuple",
          "components": [
            {
              "name": "a",
              "type": "uint256"
            },
            {
              "name": "b",
              "type": "uint256[]"
            },
            {
              "name": "c",
              "type": "tuple[]",
              "components": [
                {
                  "name": "x",
                  "type": "uint256"
                },
                {
                  "name": "y",
                  "type": "uint256"
                }
              ]
            }
          ]
        },
        {
          "name": "t",
          "type": "tuple",
          "components": [
            {
              "name": "x",
              "type": "uint256"
            },
            {
              "name": "y",
              "type": "uint256"
            }
          ]
        },
        {
          "name": "a",
          "type": "uint256"
        }
      ],
      "outputs": []
    }
  ]

.. _abi_packed_mode:

Non-standard Packed Mode
========================

Solidity supports a non-standard packed mode where:

- no :ref:`function selector <abi_function_selector>` is encoded,
- types shorter than 32 bytes are neither zero padded nor sign extended and
- dynamic types are encoded in-place and without the length.

As an example encoding ``int1, bytes1, uint16, string`` with values ``-1, 0x42, 0x2424, "Hello, world!"`` results in ::

    0xff42242448656c6c6f2c20776f726c6421
      ^^                                 int1(-1)
        ^^                               bytes1(0x42)
          ^^^^                           uint16(0x2424)
              ^^^^^^^^^^^^^^^^^^^^^^^^^^ string("Hello, world!") without a length field

More specifically, each statically-sized type takes as many bytes as its range has
and dynamically-sized types like ``string``, ``bytes`` or ``uint[]`` are encoded without
their length field. This means that the encoding is ambiguous as soon as there are two
dynamically-sized elements.
