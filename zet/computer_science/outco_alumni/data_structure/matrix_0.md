Find the top left an bottom right coordinates of a rectangle of 0's
within a matrix of 1's. It's essentially a modified version of the
finding the number of island problem where you only need to dfs to the
right and down.

```
Ex.
[[ 1, 1, 1, 1],
[ 1, 0, 0, 1],
[ 1, 0, 0, 1],
[ 1, 1, 1, 1]]

results = [[1,1], [2,2]]

Ex.
[[ 1, 1, 1, 1],
[ 1, 0, 0, 0],
[ 1, 0, 0, 0],
[ 1, 1, 1, 1]]

results = [[1,1], [2,3]] [1-2, 1-3]

[[0, 1, 1, 0],
[ 1, 0, 0, 1],
[ 1, 0, 0, 1],
[ 1, 1, 1, 1]]

[[0,0], [0,0]]
```



```javascript
function find_square(matrix) {
  const result = [];
  let found = false;

  for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; i < matrix[i].length; j++) {
      if (matrix[i][j] === 0) {
        result.push([i,j]);
        found = true;
        break;
      }
    }
    if (found) break;
  }

  return result;
}
```

This is just looping through the whole matrix and stopping on the first
0 it finds on the top left. Now we need to figure out the bottom right. 

Now let's try something more:

```javascript
function find_square(matrix) {
  const result = [];
  let found = false;

  for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; i < matrix[i].length; j++) {
      if (matrix[i][j] === 0 && validate_new(i, j, result)) {
        const end = find_end(matrix, i, j);

        result.push([[i,j], end]);
        found = true;
        break;
      }
    }
    if (found) break;
  }

  return result;
}

function validate_new(i, j, result) {
  for (let x = 0; x < results.length; x++) {
    if (i >= result[x][0][0] && i <= result[x][1][0] &&
      j >= result[x][0][1] && j <= result[x][1][1]) {
        return false;
    }
  }
  return true;
}

function find_end(matrix, i, j) {
  if (i >= matrix.lenght || j >= matrix[i].length || matrix[i][j] !== 0) {
    return [i, j];
  }
  [end_i, _ ] = find_end(matrix, i + 1, j);
  [_, end_j] = find_end(matrix, i, j + 1);

  return [end_i, end_j]
}
```

validate_new(0,0, [[0,0], [0,0]])








