# Diff, Match and Patch

This is a mirror/fork of the [Diff, Match and Patch Library](http://code.google.com/p/google-diff-match-patch/) by Neil Fraser.


Diff, Match and Patch Library
==============================

[http://code.google.com/p/google-diff-match-patch/](http://code.google.com/p/google-diff-match-patch/)
**Neil Fraser**

Online demo: http://GerHobbelt.github.io/google-diff-match-patch/


## License and installing the software

The software is licenced under the Apache License Version 2.0.

To install the library please use [bower](https://github.com/bower/bower) or simply clone this repository.

    bower install google-diff-match-patch-js


## Available languages / ports

This library is currently available in seven different ports, all using the same API.
Every version includes a full set of unit tests.

C++:
* Ported by Mike Slemmer.
* Currently requires the Qt library.

C#:
* Ported by Matthaeus G. Chajdas.

Dart:
* The Dart language is still growing and evolving, so this port is only as
  stable as the underlying language.

Java:
* Included is both the source and a Maven package.

JavaScript:
* diff_match_patch_uncompressed.js is the human-readable version.
  Users of node.js should 'require' this uncompressed version since the
  compressed version is not guaranteed to work outside of a web browser.
* diff_match_patch.js has been compressed using Google's internal JavaScript compressor.
  Non-Google hackers who wish to recompress the source can use:
  http://dean.edwards.name/packer/

Lua:
* Ported by Duncan Cross.
* Does not support line-mode speedup.

Objective C:
* Ported by Jan Weiss.
* Includes speed test (this is a separate bundle for other languages).

Python:
* Two versions, one for Python 2.x, the other for Python 3.x.
* Runs 10x faster under PyPy than CPython.

Demos:
* Separate demos for Diff, Match and Patch in JavaScript.
	
	


<div id="wikicontent">
 <div class="vt" id="wikimaincol">
 <p><strong>Introduction</strong> </p><blockquote>This library is available in multiple languages.  Regardless of the language used, the interface for using it is the same.  This page describes the API for the public functions.  For further examples, see the relevant test harness. 
</blockquote><p><strong>Initialization</strong> </p><blockquote>The first step is to create a new <tt>diff_match_patch</tt> object.  This object contains various properties which set the behaviour of the algorithms, as well as the following methods/functions: 
</blockquote><p><strong>diff_main(text1, text2) =&gt; diffs</strong> </p><blockquote>An array of differences is computed which describe the transformation of text1 into text2. Each difference is an array (JavaScript, Lua) or tuple (Python) or Diff object (C++, C#, Objective C, Java). The first element specifies if it is an insertion (1), a deletion (-1) or an equality (0). The second element specifies the affected text. 
</blockquote><blockquote><tt>diff_main("Good dog", "Bad dog") =&gt; [(-1, "Goo"), (1, "Ba"), (0, "d dog")]</tt> 
</blockquote><blockquote>Despite the large number of optimisations used in this function, diff can take a while to compute. The <tt>diff_match_patch.Diff_Timeout</tt> property is available to set how many seconds any diff's exploration phase may take. The default value is 1.0. A value of 0 disables the timeout and lets diff run until completion. Should diff timeout, the return value will still be a valid difference, though probably non-optimal. 
</blockquote><p><strong>diff_cleanupSemantic(diffs) =&gt; null</strong> </p><blockquote>A diff of two unrelated texts can be filled with coincidental matches. For example, the diff of "mouse" and "sofas" is <tt>[(-1, "m"), (1, "s"), (0, "o"), (-1, "u"), (1, "fa"), (0, "s"), (-1, "e")]</tt>. While this is the optimum diff, it is difficult for humans to understand. Semantic cleanup rewrites the diff, expanding it into a more intelligible format. The above example would become: <tt>[(-1, "mouse"), (1, "sofas")]</tt>. If a diff is to be human-readable, it should be passed to <tt>diff_cleanupSemantic</tt>. 
</blockquote><p><strong>diff_cleanupEfficiency(diffs) =&gt; null</strong> </p><blockquote>This function is similar to <tt>diff_cleanupSemantic</tt>, except that instead of optimising a diff to be human-readable, it optimises the diff to be efficient for machine processing. The results of both cleanup types are often the same. 
</blockquote><blockquote>The efficiency cleanup is based on the observation that a diff made up of large numbers of small diffs edits may take longer to process (in downstream applications) or take more capacity to store or transmit than a smaller number of larger diffs. The <tt>diff_match_patch.Diff_EditCost</tt> property sets what the cost of handling a new edit is in terms of handling extra characters in an existing edit. The default value is 4, which means if expanding the length of a diff by three characters can eliminate one edit, then that optimisation will reduce the total costs. 
</blockquote><p><strong>diff_levenshtein(diffs) =&gt; int</strong> </p><blockquote>Given a diff, measure its <a href="http://en.wikipedia.org/wiki/Levenshtein_distance" rel="nofollow">Levenshtein distance</a> in terms of the number of inserted, deleted or substituted characters.  The minimum distance is 0 which means equality, the maximum distance is the length of the longer string. 
</blockquote><p><strong>diff_prettyHtml(diffs) =&gt; html</strong> </p><blockquote>Takes a diff array and returns a pretty HTML sequence.  This function is mainly intended as an example from which to write ones own display functions. 
</blockquote><p><strong>match_main(text, pattern, loc) =&gt; location</strong> </p><blockquote>Given a text to search, a pattern to search for and an expected location in the text near which to find the pattern, return the location which matches closest. The function will search for the best match based on both the number of character errors between the pattern and the potential match, as well as the distance between the expected location and the potential match. 
</blockquote><blockquote>The following example is a classic dilemma. There are two potential matches, one is close to the expected location but contains a one character error, the other is far from the expected location but is exactly the pattern sought after: 
<tt>match_main("abc12345678901234567890abbc", "abc", 26)</tt> 
Which result is returned (0 or 24) is determined by the <tt>diff_match_patch.Match_Distance</tt> property.  An exact letter match which is 'distance' characters away from the fuzzy location would score as a complete mismatch. For example, a distance of '0' requires the match be at the exact location specified, whereas a threshold of '1000' would require a perfect match to be within 800 characters of the expected location to be found using a 0.8 threshold (see below).  The larger Match_Distance is, the slower match_main() may take to compute.  This variable defaults to 1000. 
</blockquote><blockquote>Another property is <tt>diff_match_patch.Match_Threshold</tt> which determines the cut-off value for a valid match. If Match_Threshold is closer to 0, the requirements for accuracy increase. If Match_Threshold is closer to 1 then it is more likely that a match will be found.  The larger Match_Threshold is, the slower match_main() may take to compute.  This variable defaults to 0.5. If no match is found, the function returns -1. 
</blockquote><p><strong>patch_make(text1, text2) =&gt; patches</strong> </p><p><strong>patch_make(diffs) =&gt; patches</strong> </p><p><strong>patch_make(text1, diffs) =&gt; patches</strong> </p><blockquote>Given two texts, or an already computed list of differences, return an array of patch objects.  The third form (text1, diffs) is preferred, use it if you happen to have that data available, otherwise this function will compute the missing pieces. 
</blockquote><p><strong>patch_toText(patches) =&gt; text</strong> </p><blockquote>Reduces an array of patch objects to a block of text which looks extremely similar to the standard GNU diff/patch format. This text may be stored or transmitted. 
</blockquote><p><strong>patch_fromText(text) =&gt; patches</strong> </p><blockquote>Parses a block of text (which was presumably created by the patch_toText function) and returns an array of patch objects. 
</blockquote><p><strong>patch_apply(patches, text1) =&gt; [text2, results]</strong> </p><blockquote>Applies a list of patches to text1. The first element of the return value is the newly patched text. The second element is an array of true/false values indicating which of the patches were successfully applied.  <tt>[</tt>Note that this second element is not too useful since large patches may get broken up internally, resulting in a longer results list than the input with no way to figure out which patch succeeded or failed.  A more informative API is in development.<tt>]</tt> 
</blockquote><blockquote>The previously mentioned Match_Distance and Match_Threshold properties are used to evaluate patch application on text which does not match exactly.  In addition, the <tt>diff_match_patch.Patch_DeleteThreshold</tt> property determines how closely the text within a major (~64 character) delete needs to match the expected text.  If Patch_DeleteThreshold is closer to 0, then the deleted text must match the expected text more closely.  If Patch_DeleteThreshold is closer to 1, then the deleted text may contain anything.  In most use cases Patch_DeleteThreshold should just be set to the same value as Match_Threshold. 
</blockquote>
 </div>
 </div>
	
