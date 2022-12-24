# 205. Isomorphic Strings

Given two strings `s` and `t`, determine if they are isomorphic.

Two strings `s` and `t` are isomorphic if the characters in `s` can be
replaced to get `t`.

All occurrences of a character must be  replaced with another character
while preserving the order of characters. No two characters may map to
the same character, but a character may map to itself.

Example 1:

```
Input: s = "egg", t = "add"
Output: true
```

Example 2:

```
Input: s = "foo", t = "bar"
Output: false
```

Example 3:

```
Input: s = "paper", t = "title"
Output: true
```

Constraints:

* `1 <= s.length <= 5 * 10^4`
* `t.length === s.length`
* `s` and `t` consist of any valid `ascii` character.

Now lets start on the problem.

```
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */

const isIsomorphic = (s, t) => {

};

// Example 1
console.log(isIsomorphic('egg', 'add')) // true
console.log(isIsomorphic('egg', 'add') === true);
// Example 2
console.log(isIsomorphic('foo', 'bar'))
console.log(isIsomorphic('foo', 'bar') === false);
// Example 3
console.log(isIsomorphic('paper', 'title'))
console.log(isIsomorphic('paper', 'title') === true);
```

What does this mean?

All occurrences of a character must be  replaced with another character
while preserving the order of characters. No two characters may map to
the same character, but a character may map to itself.

```
if (s.length === t.length) {

}
// or return false
return false;
```

We can loop over all the characters using a for loop:

```
for(let i = 0; i < s.length; i++) {

}
```

One solution is:

```
const isIsomorphic = (s, t) => {
    if (s.length !== t.length) {
        return false;
    }

    const dict1 = new Map();
    const dict2 = new Map();
    for (let i = 0; i < s.length; i++) {
        const c1 = s[i];
        const c2 = t[i];
        if (!dict1.has(c1)) {
            dict1.set(c1, c2);
        } else if (dict1.get(c1) !== c2) {
            return false;
        }

        if (!dict2.has(c2)) {
            dict2.set(c2, c1);
        } else if (dict2.get(c2) !== c1) {
            return false;
        }
    }
    return true;
}
```

Now let's speed it up. 

This seems to be faster on leetcode:

```
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
function isIsomorphic(s, t) {
    if (s.length !== t.length) {
        return false;
    }

    const dict1 = {};
    const dict2 = {};
    for (let i = 0; i < s.length; i++) {
        const c1 = s[i];
        const c2 = t[i];
        if (!dict1.hasOwnProperty(c1)) {
            dict1[c1] = c2;
        } else if (dict1[c1] !== c2) {
            return false;
        }

        if (!dict2.hasOwnProperty(c2)) {
            dict2[c2] = c1;
        } else if (dict2[c2] !== c1) {
            return false;
        }
    }

    return true;
}
```

This is using an object instead of the above Map.

This is a function that determines whether two strings, `s` and `t`, are isomorphic to each other. Isomorphic strings are strings that have a one-to-one correspondence between their characters. This means that each character in one string corresponds to a unique character in the other string, and vice versa.

The function first checks if the two strings have the same length. If not, it returns `false` since it is not possible for the strings to be isomorphic if they have different lengths. 

Then, it creates two maps, `dict1` and `dict2`, which will be used to store the one-to-one correspondences between the characters of the two strings.

It then iterates through each character in the strings and checks the following:

1. If the current character in `s` is not already in `dict1`, it adds an entry to `dict1` that maps the character to the corresponding character in `t`.
2. If the current character in `s` is already in `dict1`, it checks if the corresponding character in `t` is the same as the one mapped to it in `dict1`. If not, it returns `false` as the strings are not isomorphic.
3. If the current character in `t` is not already in `dict2`, it adds an entry to `dict2` that maps the character to the corresponding character in `s`.
4. If the current character in `t` is already in `dict2`, it checks if the corresponding character in `s` is the same as the one mapping to it in `dict2`. If not, it returns `false` as the strings are not isomorphic. 

If the loop completes without returning `false`, it means that the strings are isomorphic, so the function returns `true`


It require the checks on both dictionaries you cannot do with just one. This is to maintain consistency. 

