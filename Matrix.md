## Matrix
#### 48. Rotate Image
You are given an n x n 2D matrix representing an image. Rotate the image by 90 degrees (clockwise).

顺时针旋转90度 = 逆时针转90度再水平翻转；逆时针转90度就是求转置矩阵
~~~~
public void rotate(int[][] matrix) {
    for (int i = 0; i<matrix.length; i++)
        for (int j = i; j<matrix[i].length; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    for (int i = 0; i<matrix.length; i++){
        for (int j = 0; j<matrix[i].length/2; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[i][matrix.length - 1 - j];
            matrix[i][matrix.length - 1 - j] = temp;
        }
    }
}
~~~~
#### 54. Spiral Matrix
Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

这个题不要想太深，维持四个变量记录接下来的最大行数和列数，然后按顺序每计数完一行（列）就更新这四个变量即可。
~~~~
public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> res = new ArrayList<Integer>();
    if (matrix.length == 0)
        return res;
    int rowBegin = 0;
    int rowEnd = matrix.length-1;
    int colBegin = 0;
    int colEnd = matrix[0].length - 1;
    while (rowBegin <= rowEnd && colBegin <= colEnd) {
        for (int j = colBegin; j <= colEnd; j ++)
            res.add(matrix[rowBegin][j]);
        rowBegin++;
        for (int j = rowBegin; j <= rowEnd; j ++)
            res.add(matrix[j][colEnd]);
        colEnd--;
        if (rowBegin <= rowEnd)
            for (int j = colEnd; j >= colBegin; j --)
                res.add(matrix[rowEnd][j]);
        rowEnd--;
        if (colBegin <= colEnd)
            for (int j = rowEnd; j >= rowBegin; j --) 
                res.add(matrix[j][colBegin]);
        colBegin++;
    }
    return res;
}
~~~~
