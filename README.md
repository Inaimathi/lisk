# Lisk
###### S-expression-based syntax alternative for Haskell.

# This software is incomplete. Don't try to use it.

# General notes/brainstorming

Ok, so first off, we want naive s-expression syntax for Haskell. We want it o that the translation is essentially

    (a b c) ==> (a) b c

We could use clojure-style arglists

	(def foo 5)
    > foo = 5

	(def test (str) (, str 1))
	> test str = ((,) str 1)

	(def fib
	   (0) 0
	   (1) 1
	   (n) (+ (fib (- n 1))
	          (fib (- n 2))))

	> fib 0 = 0
	  fib 1 = 1
	  fib n = ((+) (fib ((-) n 1)) (fib ((-) n 2)))

	(data Foo Int Int)

	> data Foo = Foo Int Int

Actually, we might want to go the whole nine and treat symbol-named functions as infix so that that last line of `fib` cn get translated as

	(fib (n - 1)) + (fib (n - 2))

we don't need to be idiomatic, but we should be readable.

Probably most importantly, I want friggin' macros. Specifically, I want this macro

	(defmacro record (data-name &rest slot/type-pairs)
	  `(progn (data ,data-name ,@(map second slot/type-pairs))
	     ,@(let ((slots (map first slot/type-pairs)))
	         (map (fn (name) `(def ,name ((,data-name ,@(substitute-if-not '_ (fn (n) (/= n name)) slots))))) slots))))

	(record Foo (a String) (b Int) (c Int))

	> data Foo = Foo String Int Int
	  a (Foo a _ _) = a
	  b (Foo _ b _) = b
	  c (Foo _ _ c) = c

because I eventually want to be able to put together getters *and* setters this way. Syntactic extensions like that are probably the main reason I'm interested in doing some github necro here.

# Specific TODOs

- The `lisk` binary should be capable of taking a single file as input; in that event, it should spit out the converted file to stdout
