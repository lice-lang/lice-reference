# Lice Reference

## Language design

+ Strict syntax
+ Lisp-style comments

```lisp
(233) ; 233

233 ; 233

(type 233) ; 233 is a java.lang.Integer

; assignment
(-> fucker 233) ; OK

; immediate value
fucker ; 233
```

+ Variables

```lisp
|> 12903
12903 => java.lang.Integer

|> (-> fuck 9320)
9320 => java.lang.Integer

|> fuck
9320 => java.lang.Integer
```

+ Basic functions

```lisp
(+ 2 3 4 (* 2 5)) ; result: 19

(sqrt 100) ; result: 10.0

; also '||'
(&& (> 3 2 1) (>= 3 3 1)) ; result: true

(print 1) ; 1
(type 1) ; java.lang.Integer

; same as Java "String.format"
(format "%d %d" 233 233)
```

+ Compare

```lisp
(== 1 1) ; number comparison,
         ; is actually "1 == 1"
(== 1 1N) ; true
(== 1 (->double 1)) ; true
(=== a b) ; is actually a.equals(b)
(=== 1 1N) ; false

; also != !==
```

+ Literals

```lisp
() ; null
null ; null
true ; true
false ; false
10 ; 10
0o10 ; 8
0x10 : 16
0b10 ; 2
233N ; BigInteger("233")
"deep" ; "deep"
```

+ Primitives

```lisp
|> (- 1N 10)
-9 => java.math.BigInteger

|> (- 10N 1)
9 => java.math.BigInteger

|> (+ 1 1)
2 => java.lang.Integer

|> (+ 9999999999999999999999999999999999999N 9999999999)
10000000000000000000000000009999999998 => java.math.BigInteger

|> (- 9999999999999999999999999999999999999N 9999999999)
9999999999999999999999999990000000000 => java.math.BigInteger

|> (+ 1 1)
2 => java.lang.Integer

|> (+ 1 1.0)
2.0 => java.lang.Float

|> (+ 1 1.1287391873917392379372193792137198237189237291)
2.128739187391739 => java.lang.Double
```

+ File/URL APIs

```lisp
(if (! (file-exists? "save"))
    (|> (write-file (file "save") "0")
         (print "no no no"))
    (println "yes yes yes"))

(print (read-url (url "http://ice1000.tech")))

(print (read-file (file "src/lice/core/SymbolList.kt")))

(write-file (file "output")
            (str-con "deep " "dark" "fantasy"))
```

+ Casts

```lisp
(str->int "12345678") ; dicimal
(str->int "0xFFF") ; hex
(str->int "0o2333") ; octal
(str->int "0b10100101") ; binary

(int->hex 12345678) ; result: 0xbc614e
(int->oct 12345678) ; result: 057060516
(int->bin 12345678) ; result: 0b101111000110000101001110

(->str (file "deep")) ; result: "deep"
```

+ `if` is function, too

It's lazily evaluated.

```lisp
(print (if (>= 1 2)
    (read-file (file "out")) ; will not be read
    (read-file (file "in"))))
```

+ A global variable map to store values

```lisp
(-> var "actual") ; put "actual" into "var"

(print var) ; prints "actual"
```

+ Loop

```lisp
(-> i 0)
(while (> 10 i)
       (|> (print i)
           (-> i (+ 1 i))))
(undef i)
; |> means to evaluate every parameters given
; just like 'run' in clojure, 'begin' in scheme
; this prints from 0 to 9
```

## Functional Programming

+ Functions are first-class:

```lisp
((if true + -) 11 11)
; 22
; I'll show you the procedure:
;
; ((if true + -) 11 11)
; (+ 11 11)
; 22

(+)
; 0

(-)
; 0

(*)
; 1
```

### call-by-what?

+ Rules

the def-family functions defines functions.

```lisp
; rule
(def[xxx] function-name [parameters1, parameters2, ..] function body)

; example
(def gcd a b (if (== 0 b) a (gcd b (% a b))))
;^^^ ^^^ ^ ^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
; |   |  | |                |
; |   |  | |         function body
; |   | parameters
; | the name of the function
;the key word
```

+ Call by value (the mostly used)

To define a call-by-value lambda, use `lambda`:

```lisp
((lambda a b (+ a b)) 120 230)
; 120 + 230

((lambda a b (* a b)) 120 230)
; 120 * 230

((lambda a (+ a a)) 233)
; 466
```

Functions:

```lisp
(def gcd a b (if (== 0 b) a (gcd b (% a b))))
(gcd 24 36)
; 12
```

+ Call by name (like C style macro, but not the same)

To define a call-by-name lambda, use `expr`:

```lisp
(-> side-effect 10)
((expr used-twice
        (+ used-twice used-twice))
  (|> (-> side-effect (+ side-effect 1))
      233))

side-effect
; 12
```

Functions:

```lisp
(defexpr f op (op true 1 2))
(f if)
; 1
```

+ Call by need(lazy evaluation)

To define a call-by-need to lambda, use `lazy`:

```lisp
((lazy unused
       "any-val")
  (|> (def side-effect true)
      233))
(def? side-effect)
; false
```

Functions:

```lisp
(deflazy unless condition a b (if condition b a))
(defexpr f op (op true 1 2))
(f unless)
; 2
```

+ Higher Order Functions

You can use lambda expressions everywhere, lambdas are first-class in Lice.

```lisp
((lambda op (op 1 2 3)) +)
```

## Built-in stuffs

+ List processing

```lisp
(for-each i (.. 1 10) (print i))
; prints: from 1 to 10

(list "233" 1 3 "666")
; make a list

([|] 1 2 3 4)
; a pair: <1, <2, <3, <4, null>>>>
```

+ Metaprogramming

```lisp
(def? if)
; true

(def? shit)
; false

(undef def?)
(def? if)
; error: `def?` undefined

(undef undef)
(undef whatever)
; error: `undef` undefined

(eval "(+ 1 1)")
; 2
```

## LJI/Lice JVM Interface/Java API

+ Invoking Java

```java
// java
public class Main {
	public static void main(String... args) {
		SymbolList sl = new SymbolList();
		sl.provideFunction(
				"java-api-invoking",
				ls -> 100 // ls is a list of Objects, cast them!
		);
		Lice.run(new File("sample/test10.lice"), sl);
	}
}
```

```lisp
; Lice

(print (java-api-invoking))
```

## Repl

Lice has a command line REPL, which will do completion, or list all the candidates when you press <kbd>tab</kbd>.

Here are some examples.

```lisp
|> (+ 1 1)
2 => java.lang.Integer

|> ()
null => java.lang.Object

|> (eval "(+ 1 1)")
2 => java.lang.Integer
null => java.lang.Object

|> (eval (str-con "(+ " "1 " "1)"))
2 => java.lang.Integer
null => java.lang.Object

|> (cons 1 11 1)
[1, 11, 1] => java.util.ArrayList

|> (eval (read-file (file "sample/tests/test3.lice")))
16769025 => java.lang.Integer
My name is Van, I'm an artist => java.lang.String
My name is Van, I'm an artist => java.lang.String

|> (if (> 2 1) 1 2)
1 => java.lang.Integer

|> (-> file (file "fuck_you"))
fuck_you => java.io.File

|> (write-file file (str-con "deep" " dark fantasy"))
deep dark fantasy => java.lang.String

|> (read-file file)
deep dark fantasy => java.lang.String

|> ([|] 1 2 3 4 5)
[1 [2 [3 [4 [5 null]]]]] => org.lice.core.Pair

|> (-> i ([|] 1 2 3 4 5 6 7))
[1 [2 [3 [4 [5 [6 [7 null]]]]]]] => org.lice.core.Pair

|> (tail (tail i))
[3 [4 [5 [6 [7 null]]]]] => org.lice.core.Pair
```
