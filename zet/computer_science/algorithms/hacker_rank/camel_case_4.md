# Camel Case 4

> Justin Bender

## Question

![Camel Case Question](./hacker_img/camel_case_4/camel_case_4_question.png)

### Input & Output Format

![Input & output format](./hacker_img/camel_case_4/camel_case_4_input_output_format.png)

### Examples & Key

![Examples and Key](./hacker_img/camel_case_4/camel_case_4_examples_key.png)


![Camel Case 4 Explaination Diagram](./hacker_img/camel_case_4/camel_case_4.png)

I won't retype everything right now. Maybe I'll come back to it. I'm
focused on excalidraw. I will attach the images to the page.

## What is the solution?

We want to do something involving a sliding window. It's a bit expensive
to break apart a string into more strings. What we want to do is peek
inside of the array of letters. Then we keep track of the index at those
points. So when we perform the splits/copy. We know which index to
perform at.

We will break down each loop to be able to create separate utility
functions around each `key`.

### Keys

#### Combine or Split

* `S`: split
* `C`: combine

#### Method, Class or Variable

* `M`: method
* `C`: Class
* `V`: Variable


![Pipe using keys to separate functions](./hacker_img/camel_case_4/camel_case_4_pipeline_character.png)

If you notice there around around 6 different variations possible. All
of them included inside of this pipeline example.

This example is:

* `S;M;plasticCup()`

* `C;V;mobile phone`

* `C;C;coffee machine`

* `S;C;LargeSoftwareBook`

* `C;M;white sheet of paper`

* `S;V;pictureFrame`

We should dive a bit deeper into each of them.

I would first like to bring up the loop that we plan to be using:

```javascript
function processData(input) {
  const lines = input.trim().split('\n');
  for (let i = 0; i < lines.length; i++) {
    console.log(lines[i]);
    console.log(i);
  }
}
```

This is because the data comes in in a chunk.

```
S;V;iPad
C;M;mouse pad
C;C;code swarm
S;C;OrangeHighlighter
Your Output (stdout)
S;V;iPad
0
C;M;mouse pad
1
C;C;code swarm
2
S;C;OrangeHighlighter
3
```

But why would be split the chunk? That's too much work. Can't we just
analyse the string and output values. Almost like we are using a window
into the string.

We could almost imagine that the input is:

```
S;M;plasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

Which makes a bit more sense when we look at it. Then we will be moving
over 1 window block at a time

> Key:
> index #): information
> [letter]: window
>
> Example:
>
> [S];M;theWorld()

### S;M;plasticCup()


0) `[S]`: Split words
  - `const action = (input[0] === 'S') ? 'split' : 'combine'`

```
[S];M;plasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

1) `[;]`: filler/skip

```
S[;]M;plasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

2) `[M]`: The word is a method
  - `const wordType = (input[2] === 'M') ? 'method' : (input[2] === 'C') ? 'class' : 'variable';`

```
S;[M];plasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

3) `[;]`: filler/skip

```
S;M[;]plasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

4) `[p]`: starting first word, index is `4`
  - `let wordStartIndex = 4`

```
S;M;[p]lasticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

5) `[l]`: continue;

```
S;M;p[l]asticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

6) `[a]`: continue;

```
S;M;pl[a]sticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

7) `[s]`: continue;

```
S;M;pla[s]ticCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

8) `[t]`: continue;

```
S;M;plas[t]icCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

9) `[i]`: continue;

```
S;M;plast[i]cCup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

10) `[c]`: continue;


```
S;M;plasti[c]Cup()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

11) `[C]`: Capital Letter indicates a new word. Index 11 is when word 2
    starts

```
S;M;plastic[C]up()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticC[u]p()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticCu[p]()\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticCup[(])\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticCup([)]\nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticCup()[\]nC;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

```
S;M;plasticCup()\[n]C;V;mobile phone\nC;C;coffee machine\nS;C;LargeSoftwareBook\nC;M;white sheet of paper\nS;V;pictureFrame\n
```

![plasticCup](./hacker_img/camel_case_4/camel_case_4_s_m_plasticcup.png)

### C;V;mobile phone

![mobile phone](./hacker_img/camel_case_4/camel_case_4_c_v_mobile_phone.png)

### C;C;coffee machine

![coffee machine](./hacker_img/camel_case_4/camel_case_4_c_c_coffee_machine.png)

### S;C;LargeSoftwareBook

![LargeSoftwareBook](./hacker_img/camel_case_4/camel_case_4_s_c_LargeSoftwareBook.png)

### C;M;white sheet of paper

![white sheet of paper](./hacker_img/camel_case_4/camel_case_4_c_m_white_sheet_of_paper.png)

### S;V;pictureFrame

![pictureFrame](./hacker_img/camel_case_4/camel_case_4_s_v_pictureFrame.png)


