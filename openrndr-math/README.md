# openrndr-math as kotlin multiplatform project

I hope this document will become obsolete soon and can be removed. The challenges mentioned here
should not impact users of `openrndr-math` component, but it is here to document rationale behind
some changes and give context for future development decisions.

## Move from build.gradle to build.gradle.kts

Kotlin multiplatform project examples are provided only as `build.gradle.kts`
files, therefore it was adopted just for this module. I tried to do it in a way impacting the
overall project structure as less as possible, but some changes had to be introduced into
master `build.gradle` file. Mainly to avoid applying all the standard build/test options to
the `openrndr-math` module.

## Changing the testing framework from Spek/Kluent to Junit/Kotest

Spek is not really supporting multiplatform Kotlin development. Neither is Kluent originally used
for assertions. Therefore, while having in mind that I want to use `openrndr-math` as the first
OPENRNDR Kotlin multiplatform lib compiled into JavaScript executed in the in web browser, I
replaced Spek with Kotest which provides both - test framework mostly compatible with Spek and own
assertions, but is built as a multiplatform library. Unfortunately after several hours I spent
trying to make it work, I also discovered Kotest framework limitations for MPP Kotlin:

* It can only run tests for JVM/JS, not for native
* In case of using new js(IR) the JS test runner will not start at all from gradle

Therefore I decided to stick to default `kotlin.test` as a framework because it is working
everywhere, but it is also limiting us in terms of test specifications. To bridge this gap I created
small helpers over `it/describe` to minimize the amount of code changes. The disadvantage is that
these tests cannot be run separately - one top leve `@Test` annotation = 1 test case.

Kotest also comes with assertions which seems even more mature than Kluent ones so I guess
assertions for values in ranges can be improved even further.

It would be nice to use backticks in function names for standard Kotlin tests so
`fun shouldNormalize0Length()` would becoming ``fun `should normalize 0 length`()``, but this
element of Kotlin is not supported in JS Kotlin. :(
