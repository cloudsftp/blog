---
title: "How Auto-Formatters Could Save Lives"
date: 2023-10-01T20:59:56+02:00
draft: true
toc: false
images:
tags:
  - reSnap
---

I know what you are thinking.
How in the world should a auto-formatter help safe lives.
Well maybe I exaggerated a little --- But I think auto-formatters help reduce a huge number of bugs without the program being run.
"How should they do this, if they do not change the semantics" you may ask.
Well, let me explain.

### A Shell-formatter anectode

Recently I did some maintenance on the `reSnap` project again.
A user opened a pull request fixing the screen on newer version of the reMarkable software.
https://github.com/cloudsftp/reSnap/pull/14
I had a quick look at the changes on my phone since I was not at my computer.
As far as I could tell, the changes were good --- they even updated the documentation in the `README.md`.
But one thing bugged me, in the following part of the diff, they used `byte_correction` and `byte` as a boolean variable which probably should have been the same.
I thought to myself "Alright, I just will approve the CI run and fix the parameter later..."

```diff
...
diff --git a/reSnap.sh b/reSnap.sh
index 6dd4ec9..6278b84 100755
--- a/reSnap.sh
+++ b/reSnap.sh
@@ -14,6 +14,7 @@ output_file="$tmp_dir/snapshot_$(date +%F_%H-%M-%S).png"
 delete_output_file="true"
 display_output_file="${RESNAP_DISPLAY:-true}"
 color_correction="${RESNAP_COLOR_CORRECTION:-true}"
+byte_correction="${RESNAP_BYTE_CORRECTION:-true}"
 filters="null"
 
 # parsing arguments
@@ -45,6 +46,9 @@ while [ $# -gt 0 ]; do
   -c | --og-color)
     color_correction="false"
     shift
+  -p | --og-pixel-format
+    byte="false"
+    col
     ;;
   -v | --version)
     echo "$0 version $version"
...
```

### The Auto-Formatter is not Happy

Minutes later I get an email, that the CI run failed... Weird.
The CI just runs a shell formatter, so I thought "Yeah he just did something different and the formatter is being pedantic about it -- Let's see..."

```diff
...
 'shfmt -i 2' returned error 1 finding the following formatting issues:

----------
--- reSnap.sh.orig
+++ reSnap.sh
@@ -46,7 +46,7 @@
   -c | --og-color)
     color_correction="false"
     shift
-  -p | --og-pixel-format
+    -p | --og-pixel-format
     byte="false"
     col
     ;;
----------
...
```

"Ok weird, I would have indented it the same way.
As a matter of fact, I *did* indent all the cases exactly the same why is the formatter hating on my boy?"
It turns out that he did in-fact make a mistake:
He forgot to add `;;` after the line with `shift` to end the branch of the `switch`-statement.

Otherwise, the following error when executing script with any parameters:
```out
./reSnap.sh: line 49: syntax error near unexpected token `)'
./reSnap.sh: line 49: `  -p | --og-pixel-format)'
```

- Reminded me of different story 
- At prev job, azubi confused why if statement not working
- Added ;
- Formatter would have saved him
```C
...

if (cond);
    printf("Hello, World!");

...
```

The auto-formatter would have de-indented the line with `printf` on it, which might have given a clue to the Azubi, that he did something wrong.


### Why are they useful?

- Similar symbols might have very different semantics
- The syntax is very similar for the human eye
- If the auto-formatted code looks different from expected, it probably will behave different from expected

Everyone should use an auto-formatter.
- years prior at my prev job
- talked about go and how nice it is that the autoformatter is installed and enabled by default by vscode
- colleague said "Well that's no good reason --- you can have an auto-formatter in every language"
- But the main thing was that it was set up and enabled automatically
- Just as with rust and their lsp even in nvim
- Many other lsps now do this too
- But setting up prettier configs or something similar is a pain to get working with specific lsps - maybe im just a noob
