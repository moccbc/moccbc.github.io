---
author: yuta
title: "Leetcode 761: Special Binary String"
date: 2021-02-21 -0800
categories: [CompetitiveProgramming, LeetCode]
tags: [algorithms, algorithms_string, competitiveprogramming, leetcode]
---

> Link to problem: [https://leetcode.com/problems/special-binary-string/description/](https://leetcode.com/problems/special-binary-string/description/)

## Problem
Special binary strings are binary strings with the following two properties:

1. The number of 0's is equal to the number of 1's.
1. Every prefix of the binary string has at least as many 1's as 0's.

You are given a special binary string s. A "move" consists of choosing two consecutive, non-empty, special substrings of s, and swapping them. Two strings are consecutive if the last character of the first string is exactly one index before the first character of the second string.

Return the lexicographically largest resulting string possible after applying the mentioned operations on the string.

## Thought Process
Staring with the conclusion, I'm going to end up modelling this problem as a balanced parentheses problem.

I first explored some binary strings to see if I can actually identify what a "special binary string" is. The most confusing part about the problem for me was that the first condition did not need to apply for every prefix. In other words, for a binary string to be "special", every prefix of it did not also need to be special as well. They just need to have "at least as many 1s as 0s".
1. `10`: This is obviously a special binary string. The entire string has an equal number of 1s and 0s. In addition the prefixes `1` and `10` satisfies condition 1.
1. `111000`: This is also a special binary string. Again, the entire string has an equal number of 1s and 0s and the prefixes `1`, `11`, `111`, `1110`, `11100`, `111000` also satisfies the second property.
1. `1`: This is not a special binary string because it doesn't satisfy the first condition.
1. `01`: This is not a special binary string, because although it satisfies the first condition, the prefix `0` does not satisfy the second condition.
1. `10101001`: This is tricky, but it is not a special binary string. It satisfies the first condition, but the second condition is not met for the prefix `1010100` (i.e. the substring that doesn't include the last character).

So what is a "move"? A move is simply a swapping of 2 different special substrings within the string itself. However, one of the substrings must not be a substring of the other. For example, if `s = 101100` (or `10 1100` to make it easier to see), you can swap the first `10` with the latter `1100`. But within `1100` you cannot swap the outer `1100` with the inner `10`.

Next I made some abstractions. From the example `01`, I pondered if a special binary string can start with a `0`. You cannot because it would violate property 2. 

Then I pondered if a special binary string can start with a `1`. This is a little more involved to show. If we have a special binary string like `...1`, then property 1 must be satisfied, so there should be a `0` somewhere in the middle of the string that accounts for the last `1`. The string would look something like `...0...1`. However, this would break property 2 because if you consider the prefix string up until the last `1`, there would be more `0`s than `1`s since the `1` that matches with one of the `0`s in the middle is not included in the prefix.

So a special binary string must always start with a `1` and end with a `0`. This kind of already reminded me of how valid parentheses only string would work, where `1 = (` and `2 = )`. Thinking about it more, the properties basically dictate that a "special binary string" is just a "balanced parentheses string". For example, a special binary string `1100` would be `(())`, and strings like `110001` or `0101` would be invalid because `(()))(` or `)()(` respectively are not balanced parentheses strings. 

Modelling the special binary strings as parentheses also helps in understanding what the "moves" look like. For example, the special binary string `110010 = (())()` can never become `110100 = (()())` because in order to do that you would need to take the last `()` in the first one and **insert** it into the outer parentheses of the first valid substring. 

```
Take these 2 characters
(())()
    ^^

And insert into the * position
( ())()
 *   ^^

(()())
```

What you can do though, is turn `110010 = (())()` into `101100 = ()(())` because you're allowed to swap 2 adjacent balanced parentheses with each other.

```
Both **** and ^^ are balanced parentheses.
(())()
****^^

You can swap them.
()(())
^^****
```

Now if we think recursively by thinking about the largest balanced parentheses substring that we can make, then we notice that the "move" only applies to substrings on the same level in the same grouping, which is essentially the same as sorting all of them from the largest to smallest.
```
Entire string is a balanced parentheses string.
((()(()))(()(())))

Each character represents the pairing parentheses.
((()(()))(()(())))
abccdeedbfgghiihfa

 (()(())) (()(())) <- can only swap these 2
 bccdeedb fgghiihf

  () (())  () (()) <- cc and deed can be swapped, gg and hiih can be swapped, but cc and hiih cannot.
  cc deed  gg hiih

      ()        ()
      ee        ii

```

## Solution
```java
class Solution {
  public String solution(final String s) {
    return dfs(s, 0, s.length()-1);
  }

  private dfs(final String s, final int l, final int r) {
    if (r - l < 0) {
      return "";
    }

    int sum = 0;
    int currL = l;
    final List<String> ls = new ArrayList<>();

    for (int currR = l; currR <= r; currR++) {
      // Applying the "balanced parentheses" check thought process.
      sum += s.charAt(currR) == '0' ? -1 : 1;
      if (sum == 0) {
        final String subString = "1" + dfs(s, currL+1, currR-1) + "0";
        ls.add(subString);
        currL = currR + 1;
        sum = 0;
      }
    }

    Collections.sort(ls, Collections.reverseOrder());
    return String.join("", ls);
  }
}
```

## Complexity Analysis
### Time Complexity
I think the time complexity of this is O(n^2)... though I have yet to actually prove it.

### Space Complexity
I think also O(n)?
