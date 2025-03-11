---
sidebar_position: 7
---

# 矩阵

## [36. 有效的数独](https://leetcode.cn/problems/valid-sudoku/)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashMap<Integer, Integer> map  = new HashMap<>();
        // 检查行
        for(int i = 0; i < 9; i++){
            for(int j = 0; j < 8; j++){
                if(board[i][j] == '.') continue;
                for(int k = j+1; k < 9; k++){
                    if(board[i][k] == '.') continue;
                    if(board[i][j] == board[i][k]) return false;
                }
            }
        }
        // 检查列
        for(int j = 0; j < 9; j++){
            for(int i = 0; i < 8; i++){
                if(board[i][j] == '.') continue;
                for(int k = i+1; k < 9; k++){
                    if(board[k][j] == '.') continue;
                    if(board[i][j] == board[k][j]) return false;
                }
            }
        }
        // 检查3*3格子
        for(int sign = 0; sign < 9; sign ++){
            Set<Character> setUnitBoard = new HashSet<>();
            int unitRow = (sign % 3) * 3;
            int unitCol = (sign / 3) * 3;
            for (int i = unitRow; i < unitRow + 3; i++) {
                for (int j = unitCol; j < unitCol + 3; j++) {
                    if (board[i][j] != '.') {
                        if (!setUnitBoard.add(board[i][j])) {
                            return false;
                        }
                    }
                }
            }
        }
        return true;
    }
}
```

## [54. 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)

解法一：

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        if(matrix.length == 0) return new ArrayList<>();

        int l = 0, r = matrix[0].length - 1, t = 0, b = matrix.length - 1, x = 0;
        List<Integer> res = new ArrayList<>();

        while(true){
            for (int i = l; i <= r; i++) res.add(matrix[t][i]) ; // left to right
            if (++t > b) break;
            for (int i = t; i <= b; i++) res.add(matrix[i][r]); // top to bottom
            if (l > --r) break;
            for (int i = r; i >= l; i--) res.add(matrix[b][i]); // right to left
            if (t > --b) break;
            for (int i = b; i >= t; i--) res.add(matrix[i][l]); // bottom to top
            if (++l > r) break;
        }
        return res;
    }
}
```

解法二：

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();

        int up = 0, down = matrix.length - 1, left = 0, right = matrix[0].length - 1;
        int direction = 0; // 0 右 1 下 2 左 3 上
        int x = 0, y = 0;  // 坐标

        while(true){
            res.add(matrix[x][y]);
            // 集合元素达到一定数量退出
            if(res.size() == matrix.length * matrix[0].length)
                break;
            // 到达边界 ，改变方向，缩小边界
            if(direction == 0 && y == right){
                direction=1;
                up++;
            }else if(direction == 1 && x == down){
                direction = 2;
                right--;
            }else if(direction == 2 && y == left){
                direction=3;
                down--;
            }else if(direction == 3 && x==up){
                direction=0;
                left++;
            }
            // 沿着方向走
            if(direction == 0) y++;
            else if(direction == 1) x++;
            else if(direction == 2) y--;
            else if(direction == 3) x--;
        }
        return res;
    }
}
```

## [73. 矩阵置零](https://leetcode.cn/problems/set-matrix-zeroes/)

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        List<Integer> row = new ArrayList<>();
        List<Integer> col = new ArrayList<>();

        for(int i = 0; i < matrix.length; i++){
            for(int j = 0; j < matrix[0].length; j++){
                if(matrix[i][j] == 0){
                    row.add(i);
                    col.add(j);
                }
            }
        }

        for(int i = 0; i < row.size(); i ++){
            for(int j = 0; j < matrix[0].length; j++){
                matrix[row.get(i)][j] = 0;
            }
        }
        for(int j = 0; j < col.size(); j ++){
            for(int i = 0; i < matrix.length; i++){
                matrix[i][col.get(j)] = 0;
            }
        }
    }
}
```

## [48. 旋转图像](https://leetcode.cn/problems/rotate-image/)

```java
class Solution {
    public void rotate(int[][] matrix) {
        int[][] matrixCopy = new int[matrix.length][matrix[0].length];
        for(int i = 0; i < matrix.length; i++)
            for(int j = 0; j < matrix[0].length; j++)
                matrixCopy[i][j] = matrix[i][j];
            
        for(int i = 0; i< matrix.length; i++){
            for(int j = 0; j < matrix[0].length; j ++){
                matrix[j][matrix[0].length-1 - i] = matrixCopy[i][j];
            }
        }

    }
}
```

## [289. 生命游戏](https://leetcode.cn/problems/game-of-life/)

解法一：

```java
class Solution {
    public void gameOfLife(int[][] board) {
        int[][] boardCopy = new int[board.length][board[0].length];
        for(int i = 0; i < board.length; i++)
            for(int j = 0; j < board[0].length; j++)
                boardCopy[i][j] = board[i][j];

        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                int count = 0;
                if(i-1 >= 0 && j-1 >= 0){
                    if(boardCopy[i-1][j-1] == 1){
                        count ++;
                    }
                }
                if(i-1 >= 0){
                    if(boardCopy[i-1][j] == 1){
                        count ++;
                    }
                }
                if(i-1 >= 0 && j+1 < board[0].length){
                    if(boardCopy[i-1][j+1] == 1){
                        count ++;
                    }
                }
                if(j-1 >= 0){
                    if(boardCopy[i][j-1] == 1){
                        count ++;
                    }
                }
                if(j+1 < board[0].length){
                    if(boardCopy[i][j+1] == 1){
                        count ++;
                    }
                }
                if(i+1 < board.length && j-1 >= 0){
                    if(boardCopy[i+1][j-1] == 1){
                        count ++;
                    }
                }
                if(i+1 < board.length){
                    if(boardCopy[i+1][j] == 1){
                        count ++;
                    }
                }
                if(i+1 < board.length && j+1 < board[0].length){
                    if(boardCopy[i+1][j+1] == 1){
                        count ++;
                    }
                }

                if(count < 2 && boardCopy[i][j] == 1){
                    board[i][j] = 0;
                } else if((count == 2 || count == 3)&& boardCopy[i][j] == 1 ){
                    board[i][j] = 1;
                } else if(count >  3&& boardCopy[i][j] == 1 ){
                    board[i][j] = 0;
                } else if(count ==  3&& boardCopy[i][j] == 0 ){
                    board[i][j] = 1;
                }
            }
        }
    }
}
```

解法二：

```java
class Solution {
    public void gameOfLife(int[][] board) {
        int[][] boardCopy = new int[board.length + 2][board[0].length + 2];
        for(int i = 0; i < board.length; i++)
            for(int j = 0; j < board[0].length; j++)
                boardCopy[i+1][j+1] = board[i][j];

        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                int count = 0;
                int x = i+1, y = j+1;
                count = boardCopy[x-1][y-1] + boardCopy[x-1][y] + boardCopy[x-1][y+1] + boardCopy[x][y-1] + boardCopy[x][y+1] + boardCopy[x+1][y-1] + boardCopy[x+1][y] + boardCopy[x+1][y+1]; 

                if(count < 2 && boardCopy[x][y] == 1){
                    board[i][j] = 0;
                } else if((count == 2 || count == 3)&& boardCopy[x][y] == 1 ){
                    board[i][j] = 1;
                } else if(count >  3&& boardCopy[x][y] == 1 ){
                    board[i][j] = 0;
                } else if(count ==  3&& boardCopy[x][y] == 0 ){
                    board[i][j] = 1;
                }
            }
        }
    }
}
```

