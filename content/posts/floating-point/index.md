---
title: "Be Careful when Working With Floating Point Numbers"
date: 2023-09-26T14:00:07+02:00
draft: true
toc: false
images:
tags:
  - untagged
---

---- sky::sun::test::sun_position_wiki stdout ----
thread 'sky::sun::test::sun_position_wiki' panicked at 'the difference of 0.33556810268310705 (is) and 0.3335324200561164 (want) is 0.0020356826269906647, which is larger than 0.00005 (epsilon)', sky-service/src/sky/util.rs:3:5

# First Approach

Let's replace the normalization functions.
- Better results
- Better developer ergonomics

old approach with divide trunc etc: 0.6666666666665719

new approach with rem_euclid: 0.6666666666669698

---- sky::sun::test::sun_position_wiki stdout ----
thread 'sky::sun::test::sun_position_wiki' panicked at 'the difference of 0.33556810268310705 (is) and 0.3335324200561164 (want) is 0.0020356826269906647, which is larger than 0.00005 (epsilon)', sky-service/src/util.rs:3:5

Did not solve the problem :(

# Second Approach

Start normalizing angles before addition.


let epsilon = SKEW_OF_ECLIPTIC_C0 + n * SKEW_OF_ECLIPTIC_C1;

had - instead of plus, my bad

thread 'sky::sun::test::sun_position_wiki' panicked at 'the difference of 0.3355799935815938 (is) and 0.3335324200561164 (want) is 0.0020475735254774086, which is larger than 0.00005 (epsilon)', sky-service/src/util.rs:3:5

no real improvement


comparing golang implementation: thetaGh, thetaG, and theta are much larger in my rust implementation...

in golang implementation took the remainder of thetaGh and 24 since 24 hours in a day.

golang: 2.975916317316887
rust: 170.99234377344283
after taking remainder: 2.992343773442826
