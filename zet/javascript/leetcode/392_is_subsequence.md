# 392. Is Subsequence

Given two strings `s` and `t`, return `true` if `s` is a `subsequence` of `t`, of `false` otherwise.

A subsequence of a string is a new string that is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., `ace` is a subsequence of `abcde` while `aec` is not).

Example 1:

```
Input: s = "abc", t = "ahbgdc"
Output: true
```

Example 2:

```
Input: s = "axc", t = "ahbgdc"
Output: false 
```

Constraints:

* `0 <= s.length <= 100`
* `0 <= t.length <= 10^4`
* `s` and `t` consist only of lowercase English letters.

__Follow up:__

Suppose there are lots of incoming `s`, say `s1, s2, ..., sk` where `k >= 10^9`, and you want to check one by one to see if `t` has its subsequence. In this scenario, how would you change your code?

Let's start the code:

```
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
const isSubsequence = (s, t) => {

}

console.log(isSubsequence("abc", "ahbgdc"));
console.log(isSubsequence("abc", "ahbgdc") === true);

console.log(isSubsequence("axc", "ahbgdc"));
console.log(isSubsequence("axc", "ahbgdc") === false);
```

What's going on? 

This one seems pretty simple. The string must be in order.

1. Loop over `t`
2. Check off letters in `s`
3. If it clears `s` before the loop over `t` stops.
4. Return true or false.

* `const tSize = t.length;`: Length of `t` string into `tSize` variable
* `const sSize = s.length`: Length of `s` string into `sSize` variable
* Loop over `tSize`:

```
for(let i = 0; i < tSize; i++) {
    console.log(t[i]);
}
```

I would like to use another variable called `let window = 0;`

This would allow us to increment window. Once window is greater than `sSize`. We can return true.

```
if(window > sSize) {
  return true
}
```

We need to check if the letters in `t` are equal to the first letter of `s`

If it is equal we can increment the window up.

```
if(t[i] === s[window]) {
  window++;
}
```

My first answer is:

```
const isSubsequence = (s, t) => {
  const tSize = t.length;
  const sSize = s.length - 1;
  let window = 0;

  for(let i = 0; i < tSize; i++) {
    if(t[i] === s[window]) window++;
    if(window > sSize) return true;
  }
  return false;
}
```

That failed because it didn't check for `s = "", t = ""`

```
const isSubsequence = (s, t) => {
  const tSize = t.length;
  const sSize = s.length - 1;
  let window = 0;
  if(tSize === 0) return true;

  for(let i = 0; i < tSize; i++) {
    if(t[i] === s[window]) window++;
    if(window > sSize) return true;
  }
  return false;
}
```

That still failed on a test. 

```
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
const isSubsequence = (s, t) => {
  const tSize = t.length;
  const sSize = s.length;
  let window = 0;
  if(tSize === 0 && sSize === 0) return true;

  for(let i = 0; i < tSize; i++) {
    if(t[i] === s[window]) window++;
    if(window > sSize - 1) return true;
  }
  return false;
}
```

I also like this solution as well. Honorable mention:

```
const isSubsequence = (s, t) => {
  if (s.length > t.length) return false;

  let subsequence = 0;
  for (let i = 0; i < t.length; i++) {
    if (s[subsequence] === t[i]) {
      subsequence++;
    }
  }
  return subsequence === s.length
};
```

Another good option is:

```
function isSubsequence(s, t) {
  // Edge case: return true if s is empty
  if(!s) return true;
  // Edge case: return false if t is empty
  if(!t) return false;
  // Initialize variables to track the current position in s and t
  let sPos = 0,
      tPos = 0;

  // Iterate over t
  while(tPos < t.length) {
    // Check if the current character in t matches the current character in s
    if(s[sPos] === t[tPos]) {
      // If it does, move to the next character in s
      sPos += 1;
      // If we reach the end of s, return true
      if(sPos === s.length) return true;
    }
    // Move to the next character in t
    tPos += 1;
  }
  // If we reach the end of t without finding all characters in s, return false
  return false;
}
```

There are a few ways to do the same thing. I hope that makes a little bit more sense now.





