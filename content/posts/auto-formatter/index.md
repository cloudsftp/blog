---
title: "How Auto-formatters Save Lives"
date: 2023-10-01T20:59:56+02:00
draft: false
toc: false
images:
tags:
  - reSnap
---

I know what you are thinking.
How in the world should an auto-formatter safe lives.
Well, maybe I exaggerated a little --- But I think auto-formatters reduce many bugs without running the program.
"How should they do this if they do not change the semantics," you may ask.
Well, let me explain.

### A Shell-formatter anecdote

Recently, I did some maintenance on the [reSnap](https://github.com/cloudsftp/reSnap) project.
A user had opened a [pull request](https://github.com/cloudsftp/reSnap/pull/14) fixing screenshots on newer version of the reMarkable software.
I looked at the changes on my phone, since I was not at my computer.
As far as I could tell, the changes were good --- they even updated the documentation in the `README.md`.
But one thing bugged me.
In the following part of the diff, they used `byte_correction` and `byte` as a boolean variable, which probably should have been the same.

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

I thought: "Alright, I will approve the CI run and fix the parameter later..."


<div style='text-align: center; font-size: 16pt'>
    <strong> The auto-formatter was not happy. </strong>
</div>

Minutes later, I got an e-mail that the CI run failed... **Huh.**
The CI only runs a shell formatter, so I thought: "Yeah, he just did something slightly different, and the formatter is being pedantic about it --- Let's see..."

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

"Ok, weird. I would have indented it the same way.
As a matter of fact, I *did* indent all the cases exactly the same
--- why is the formatter hating on this contributor?"
It turns out that he, in-fact, made a mistake:
He forgot to add `;;` after the line with `shift` to end the branch of the `case` statement.

This line is important because otherwise the `case` statement blows up.
This `case` statement is responsible for parsing the command-line arguments.
So, the script would crash when any argument was passed to it.

```out
./reSnap.sh: line 49: syntax error near unexpected token `)'
./reSnap.sh: line 49: `  -p | --og-pixel-format)'
```

I accepted his contribution and proceeded to fix the small mistake.

### Another Story

This situation reminded me of another situation that happened at my previous job.
A new trainee (also called "Azubi" here) was learning C++ as their first language --- a horrible choice, honestly.
One day, when I walked into the room, a senior engineer stood beside him, looking as confused as he.
They called me over to look at the code and give my input on what was wrong with it.
At first glance, it looked great and executed, but they expected the program to print something, and it did not.
So, I looked at the print statement in question again and saw the mistake.

```C++
...

if (cond);
    cout << "Hello World" << endl;

...
```

He put a semicolon after the `if` statement.

If he had an auto-formatter running on every save, he maybe would have seen the mistake since the line would have been de-indented every time he saved the document.

```C++
...

if (cond);
cout << "Hello World" << endl;

...
```

Also, this makes a strong case for always using curly braces for `if` statements and loops, even when they only have one line.
Especially when the code is in a beginner tutorial!
This is a whole other story --- just enforce curly braces at the linter level.

## Why are they useful?

You saw two examples where auto-formatters helped --- or might have helped --- discover bugs without executing the program.
But the question from the beginning remains: "How can they do this when they don't change or even analyze the semantics of the program"?

My explanation for this phenomenon is the following.
The formatter only looks at the syntax, but syntax that looks similar to our eyes may be very different, and therefore also the semantics of that syntax.
This means:

<div style='text-align: center; font-size: 14pt'>
    <strong>
        If the code looks weird after the auto-formatter changed it, <br>
        it probably is
    </strong>
</div>

I think everyone should use an auto-formatter.
Additionally to guiding your attention to possible bugs, it also automates indentation and line breaks.
