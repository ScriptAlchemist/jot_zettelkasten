# Camel Case 4

> Justin Bender

![Camel Case 4 Explaination Diagram](./hacker_img/camel_case_4.png)

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

* `S`: split
* `C`: combine

* `M`: method
* `C`: Class
* `V`: Variable


![Pipe using keys to separate functions](./hacker_img/camel_case_4_pipeline_1.png)

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

### `S;M;plasticCup()`

![plasticCup](./hacker_img/camel_case_4_s_m_plasticcup.png)

### `C;V;mobile phone`

![mobile phone](./hacker_img/camel_case_4_c_v_mobile_phone.png)

### `C;C;coffee machine`

![coffee machine](./hacker_img/camel_case_4_c_c_coffee_machine.png)

### `S;C;LargeSoftwareBook`

![LargeSoftwareBook](./hacker_img/camel_case_4_s_c_LargeSoftwareBook.png)

### `C;M;white sheet of paper`

![white sheet of paper](./hacker_img/camel_case_4_c_m_white_sheet_of_paper.png)

### `S;V;pictureFrame`

![pictureFrame](./hacker_img/camel_case_4_s_v_pictureFrame.png)


