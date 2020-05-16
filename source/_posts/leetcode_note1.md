# leetcode 刷题之路（数组和字符串专题）

## 数组篇（leetcode724,747,66）

### leetcode724 寻找数组中心索引

```
思路：数组nums的中心索引为idx，nums各元素之和为sum，则满足如下条件：
2 x (nums[0] + nums[1] + ... + nums[idx -1]) = sum - nums[idx]
则可以根据此条件， 从左至右遍历判断
```

```c++
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        if (nums.size() < 2) {
            return -1;
        }
        int leftIdx = 0;
        int sum = 0;
        int leftSum = 0;
        for (int i = 0; i < nums.size(); i++) {
            sum += nums.at(i);
        }
        for (int i = 0; i < nums.size(); i++) {
            if (leftSum * 2 + nums.at(i) == sum) {
                return i;
            }
            leftSum += nums.at(i);
        }
        return -1;
    }
};
```

### leetcode747 至少是其他数字两倍的最大数

```
思路：先按大小进行排序，如果最大的至少是第二大的两倍，则满足条件，注意排序之前，要将每个数转换为数值+原始idx的结构体，sort依据的是数值。
```

```c++
typedef struct
{
    int x, y;
} Point;
bool cmp(Point a, Point b)
{
    return a.y > b.y;
}
class Solution {
public:

    int dominantIndex(vector<int>& nums) {
        if (nums.size() == 1) {
            return 0;
        }
        vector<Point> numsVec(nums.size());
        for (int i = 0; i < nums.size(); i++) {
            numsVec.at(i).x = i;
            numsVec.at(i).y = nums.at(i);
        }
        sort(numsVec.begin(), numsVec.end(), cmp);
        if (numsVec.at(0).y >= numsVec.at(1).y * 2) {
            return numsVec.at(0).x;
        }
        else {
            return -1;
        }
    }
};
```

### leetcode66 加一

```
思路：模拟竖式加一，从个位开始，加一之后，对其进行模10运算，如果为0，则说明产生进位，再对高一位进行加一运算。如果加到最高位还是为0，则说明最高位原本为9，加1之后多了一位，则可以直接生成一个新的100..0的数组作为输出
```

```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
       for (int i = digits.size() - 1; i >= 0; i--) {
            digits[i]++;
            digits[i] = digits[i] % 10;
            if (digits[i] != 0) {
                return digits;
            }
        }
        vector<int> ans(digits.size() + 1);
        ans.at(0) = 1;
        return ans;
    }
};
```

## 二维数组篇（leetcode498,54,118）

### leetcode498 对角线遍历

```
思路：观察一个对角线矩阵：
matrix[3][4] = 
[
[1, 2,  3,  4 ],
[5, 6,  7,  8 ],
[9, 10, 11, 12]
]

可以发现如下规律：
1.对角线遍历的本质就是先在对该矩阵做分割，每次遍历从左往右取分割的一条线上的所有元素；
2.第k次遍历，所取一条线上的元素a[i][j]满足：i+j=k-1
3.k和row，col有如下关系：k=row+col-1
4.i和j还需要满足条件：0<i<row,0<j<col
5.每次遍历有两个方向：i从0增长的方向，i从最大值减少的方向，当遍历次数为2,4,6,8...时，遍历方向为i从0增长方向；当遍历次数为1,3,5,7...时，遍历方向为i从最大值减少方向
```

```c++
class Solution {
public:
    vector<int> findDiagonalOrder(vector<vector<int>>& matrix) {
        int row = matrix.size();
        // 如果输入矩阵为空矩阵，则直接退出空矩阵
        if (row == 0) {
            vector<int> ans;
            return ans;
        }
        int col = matrix.at(0).size();
        vector<int> ans;
        int sums = row + col - 2;
        int cnt = 0; // 遍历次数记录
        for (int sum = 0; sum <= sums; sum++) {
            vector<int> tmp; // 临时vector，存储此次遍历行的所有元素
            for (int i = 0; i < row; i++) {
                // i + j = sum
                int j = sum - i;
                if (j < col && j >= 0) {
                    // 有效的元素，存入该遍历行中
                    tmp.push_back(matrix[i][j]);
                }
            }
            cnt++;
            // 如果为奇数次遍历，则需要反转方向，如果为偶数次遍历，则按顺序输出
            if (cnt % 2 == 1) {
                reverse(tmp.begin(), tmp.end());
            }
            for (int n = 0; n < tmp.size(); n++) {
                ans.push_back(tmp.at(n));
            }
        }
        return ans;
    }
};
```

### leetcode54 螺旋矩阵

```
思路：考察每一轮迭代的移动方向为：右，下，左，上，朝这四个方向移动均有一个起点和终点的index的limit，朝右移动到limit位置之后，则上侧limit被更新（加一）；接着朝下移动到limit位置后，右侧limit被更新（减一）；接着朝左移动到limit位置后，下侧limit被更新（减一）；接着朝上移动到limit位置后，左侧limit被更新（加一）；更新完limit值之后，如果左右limit交叉，或上下limit交叉，则退出
```

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        if (matrix.empty()) {
            return ans;
        }
        int up = 0;
        int down = matrix.size() - 1;
        int left = 0;
        int right = matrix.at(0).size() - 1;
        while(true) {
            // right
            for (int k = left; k <= right; k++) {
                ans.push_back(matrix.at(up).at(k));
            }
            if (++up > down) {
                break;
            }

            // down
            for (int k = up; k <= down; k++) {
                ans.push_back(matrix.at(k).at(right));
            }
            if (--right < left) {
                break;
            }

            // left
            for (int k = right; k >= left; k--) {
                ans.push_back(matrix.at(down).at(k));
            }
            if (--down < up) {
                break;
            }

            // up
            for (int k = down; k >= up; k--) {
                ans.push_back(matrix.at(k).at(left));
            }
            if (++left > right) {
                break;
            }
        }
        return ans;
    }
};
```

### leetcode118 杨辉三角

```
思路：观察规律，发现除了第一行较特殊外，其余行有如下规律：
1.每行的第一个元素，和最后一个元素为1
2.第i行的中间元素j（非第一个和最后一个），满足matrix[i][j] = matrix[i-1][j-1] + matrix[i-1][j]
```

```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans(numRows);
        if (numRows == 0) {
            return ans;
        }
        ans.at(0).push_back(1);
        for (int i = 1; i < numRows; i++) {
            ans.at(i).push_back(1);
            for (int j = 1; j < i; j++) {
                ans.at(i).push_back(ans.at(i-1).at(j - 1) + ans.at(i-1).at(j));
            }
            ans.at(i).push_back(1);
        }
        return ans;
    }
};
```

## 字符串（leetcode）

### leetcode67 二进制求和

```
思路：模拟竖式加法，a和b相加，
1.先增加最低位，即有四种情况：0+0;0+1;1+0;1+1,每种情况下，还需要考虑是否进位。而且每位相加之后，进位carry标识也会更新；
2.按照之前的规则遍历，a和b可能有一个数先遍历完，则另一个数的每位，也有两种情况：1；0，也需要把前一位的进位考虑进来，每一位处理完，也需要更新carry；
3.最终a和b都遍历完之后，还需要判断carry，若还保存有carry，则需要在最高位再增加1，否则结束。
```

```c++
string addBinary(string a, string b) {
        int alen = a.size();
        int blen = b.size();
        string ans{};
        bool carry = false;
        while(!a.empty() && !b.empty()) {
            if (a.back() == '0' && b.back() == '0') {
                if (carry) {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                } else {
                    ans.insert(ans.begin(), '0');
                    carry = false;
                }
            }
            if ((a.back() == '0' && b.back() == '1') || (a.back() == '1' && b.back() == '0')) {
                if (carry) {
                    ans.insert(ans.begin(), '0');
                    carry = true;
                } else {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                }
            }
            if (a.back() == '1' && b.back() == '1') {
                if (carry) {
                    ans.insert(ans.begin(), '1');
                    carry = true;
                } else {
                    ans.insert(ans.begin(), '0');
                    carry = true;
                }
            }
            a.pop_back();
            b.pop_back();
        }
        while (!a.empty()) {
            if (a.back() == '0') {
                if (carry) {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                } else {
                    ans.insert(ans.begin(), '0');
                    carry = false;
                }
            }
            if (a.back() == '1') {
                if (carry) {
                    ans.insert(ans.begin(), '0');
                    carry = true;
                } else {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                }
            }
            a.pop_back();
        }
        while (!b.empty()) {
            if (b.back() == '0') {
                if (carry) {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                } else {
                    ans.insert(ans.begin(), '0');
                    carry = false;
                }
            }
            if (b.back() == '1') {
                if (carry) {
                    ans.insert(ans.begin(), '0');
                    carry = true;
                } else {
                    ans.insert(ans.begin(), '1');
                    carry = false;
                }
            }
            b.pop_back();
        }
        if (carry) {
            ans.insert(ans.begin(), '1');
        }
        return ans;
    }
```

### leetcode28 实现strStr()

```
思路：
```

