# leetcode 刷题之路（数组和字符串专题）

## 数组篇

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
思路：遍历被匹配的string haystack，如果第一个字符相等，则遍历下一个，如果遍历完之后，发现正好到达needle的末尾，则说明匹配上，则退出。需要考虑多种边界条件以及多种特殊情况：
1.haystack和needle长度为1
2.haystack长度小于needle
3.haystack或needle为空
```

```c++
int strStr(string haystack, string needle) {
        if (needle.empty()) {
            return 0;
        }
        if (haystack.empty() || (haystack.size() < needle.size())) {
            return -1;
        }
        for (int i = 0; i <= (haystack.size() - needle.size()); i++) {
            int k = i;
            int j = 0;
            while ( k < haystack.size() && j < needle.size() && (haystack.at(k) == needle.at(j)) ) {
                k++;
                j++;
            }
            if (j == needle.size()) {
                return i;
            }
        }
        return -1;
    }
```

### leetcode14 最长公共前缀

```
思路：先将字符串数组数组用sort排序，排好序之后，该字符串数组变为字典序，则要找最长公共前缀，只需要找字典序的数组中的第一个字符串和最后一个字符串的公共前缀，即为最长公共前缀
```

```c++
string longestCommonPrefix(vector<string>& strs) {
        string ans;
        if (strs.empty()) {
            return ans;
        }
        sort(strs.begin(),strs.end());
        if (strs.at(0).empty()) {
            return ans;
        }
        int n = strs.size();
        int i = 0;
        for (i = 0; i < strs.at(0).size(); i++) {
            if (strs.at(0).at(i) != strs.at(n-1).at(i)) {
                return strs.at(0).substr(0,i);
            }
        }
        if (i == strs.at(0).size()) {
            return strs.at(0);
        }
        return ans;
    }
```

### leetcode344 反转字符串

```
思路：使用双指针，分别从前后朝中间方向走，每次交换两个指针所指的空间内容，停止条件：left>=right
```

```c++
void reverseString(vector<char>& s) {
        int begin, end;
        if (s.empty()) {
            return;
        }
        begin = 0;
        end = s.size() - 1;
        while(begin < end) {
            swap(s.at(begin++), s.at(end--));
        }
    }
```

### leetcode561 数组拆分

```
思路：使用数学思想，先将该数组sort变为从小到大排列的数组，然后可以发现，要实现最大的总和，取的必然是第1,3,5,7...奇数个数，分别跟第2,4,6,8...偶数个数作为组合。
证明：对于一个升序数组[n1,n2,...n2m-1,n2m],选第一对min(x,y)时，必然会“牺牲掉”n2m，也就是说n2m必然是作为min(x,y)中较大的那个，那么此时最佳策略就是拿出其余数组中最大的和其搭配，也就是选择n2m-1，那这一对min(x,y)留下的最大值就是n2m-1。剩下的数组，按照相同的方法，以此类推，则最终选出的就是升序数组的奇数个元素
```

```c++
int arrayPairSum(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        sort(nums.begin(), nums.end());
        int ans = 0;
        for (int i = 0; 2 * i < nums.size(); i++) {
            ans += nums.at(2 * i);
        }
        return ans;
    }
```

### leetcode167 两数之和2-输入有序数组

```
思路：前后双指针分别指向头和尾，计算其指向的两个元素之和，如果等于target，则直接输出；如果小于target，则需要更大一点，头指针向后移动一位；如果大于target，则需要更小一点，尾指针向前移动一位
```

```c++
vector<int> twoSum(vector<int>& numbers, int target) {
        vector<int> ans;
        if (numbers.empty() || numbers.size() == 0) {
            return ans;
        }
        int begin = 0;
        int end = numbers.size() - 1;
        int sum = numbers.at(begin) + numbers.at(end);
        while( sum != target && begin < end) {
            if (sum < target) {
                begin++;
                sum = numbers.at(begin) + numbers.at(end);
            } else {
                end--;
                sum = numbers.at(begin) + numbers.at(end);
            }
        }
        if (sum == target) {
            ans.push_back(++begin);
            ans.push_back(++end);
            return ans;
        } else {
            return ans;
        }
    }
```

### leetcode27 移除元素

```
思路：用了两个指针，一个指针j用于遍历原始数组，另一个指针i用于遍历“待存入”的位置，两个指针均从0向后移动，只有当j指向的元素不等于target时，才将其存入i指向的“待存入”位置，否则，j指向下一个，i不动，这样j就比i快或至少和气相等，最终遍历完整个数组，将后面多余的数组删掉。
```

```c++
int removeElement(vector<int>& nums, int val) {
        if (nums.empty()) {
            return 0;
        }
        int ans = nums.size();
        int i = 0;
        for (int j = 0; j < nums.size(); j++) {
            if (nums[j] != val) {
                nums[i] = nums[j];
                i++;
            }
            else {
                ans--;
            }
        }
        while (nums.size() != ans) {
            nums.pop_back();
        }
        return ans;
    }
```

### leetcode485 最大连续1的个数

```
思路：需要一个当前连续1的计数sum，和最大计数maxSum，直接遍历，遇到1则计数值增加1，遇到0，则比较sum和maxSum，将更大的那一个存给maxSum，并且sum清零。
```

```c++
int findMaxConsecutiveOnes(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int sum = 0;
        int maxSum = sum;
        for (int i = 0; i < nums.size(); i++) {
            if (nums.at(i) == 1) {
                sum++;
            } else {
                if (maxSum < sum) {
                    maxSum = sum;
                }
                sum = 0;
            }
        }
        if (maxSum < sum) {
            maxSum = sum;
        }
        return maxSum;
    }
```

### leetcode209 长度最小的子数组

```
思路：考虑使用一个滑窗。遍历当前的数组，存储下从该滑窗内包含的数组的和，滑窗的起始位置arrayStart为0，终止位置为当前遍历的位置。如果当前滑窗的和sum大于等于s，且当前滑窗的和sum减去滑窗最左侧的元素的值之后，仍然大于等于s，则滑窗向右移动一格，且更新sum和arrayStart，并且比较minLen和tmpLen，取更小的那个赋值给minLen。照此规律遍历一遍，即可得到结果。
```

```c++
int minSubArrayLen(int s, vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int tmpLen = 0;
        int minLen = 0;
        int sum = 0;
        int arrayStart = 0;
        for (int i = 0; i < nums.size(); i++) {
            sum += nums.at(i);
            tmpLen++;
            if (sum >= s && minLen == 0) {
                minLen = tmpLen;
            }
            while ((sum - nums.at(arrayStart)) >= s) {
                sum = sum - nums.at(arrayStart);
                arrayStart++;
                tmpLen--;
                if (minLen > tmpLen || minLen == 0) {
                    minLen = tmpLen;
                }
            }
        }
        return minLen;
    }
```

## 数组综合

### leetcode189 旋转数组

```
思路：循环替换，从第一个idx开始，他要到达的下一个位置是(idx+k)%len，则在下一个位置上的元素被挤出来，那个元素再找他的下一个位置...以此类推，直到挪动的次数等于len结束。需要注意特殊情况：如果挪动是在部分几个元素中循环进行，则需要退出，给起始位置加一，进行下一次循环
```

```c++
void rotate(vector<int>& nums, int k) {
        if (nums.empty() || k <= 0) {
            return;
        }
        int len = nums.size();
        k = k % len;
        int start = 0;
        int tmp = 0;
        for (int cnt = 0; cnt < len; start++) {
            int current = start;
            int pre = nums.at(current);
            do {
                int next = (current + k) % len;
                int tmp = nums.at(next);
                nums.at(next) = pre;
                current = next;
                pre = tmp;
                cnt++;
            } while (current != start);
        }
    }
```

### leetcode119 杨辉三角2

```
思路：输入的rowIdex为i，则实际取得的是杨辉三角的第i+1行(因为i是从0开始的)，且输出的数组长度也为i+1。则需要计算i+1次，每次只需要计算除去两头之外的值，每个值依赖的是上一行的两个值，即ans[j] = ans[j] + ans[j-1]，因为每次修改的值所依赖的是上一行中它本身位置和它本身位置之前的值，故需要后向前计算，否则会影响到下一个元素的计算。
```

```c++
vector<int> getRow(int rowIndex) {
        if (rowIndex < 0) {
            vector<int> ans;
            return ans;
        }
        vector<int> ans(rowIndex + 1);
        if (rowIndex == 0) {
            ans.at(0) = 1;
            return ans;
        }
        if (rowIndex == 1) {
            ans.at(0) = 1;
            ans.at(1) = 1;
            return ans;
        }
        for (int i = 0; i <= rowIndex; i++) {
            ans[i] = 1;
            for (int j = i; j > 1; j--) {
                ans[j-1] = ans[j-2] + ans[j-1];
            }
        }
        return ans;
    }
```

### leetcode151 翻转字符串里的单词

```
思路：首先先将s中的单词一个一个解析出来，存入一个stack中，然后再逐个pop出stack，将其append到答案string中，需要考虑s为空，或空格的特殊情况
```

```c++
string reverseWords(string s) {
        if (s.empty()) {
            string ans;
            return ans;
        }
        stack<string> words;
        string currWord;
        for (int i = 0; i < s.size(); i++) {
            if (s.at(i) == ' ') {
                if (!currWord.empty()) {
                    words.push(currWord);
                    currWord = {};
                }
            } else {
                currWord.push_back(s.at(i));
            }
        }
        if (!currWord.empty()) {
            words.push(currWord);
        }
        string ans;
        if (words.empty()) {
            return ans;
        }
        int wordsNum = words.size();
        for (int i = 0; i < wordsNum - 1; i++) {
            ans.append(words.top());
            ans.append(" ");
            words.pop();
        }
        ans.append(words.top());
        return ans;
    }
```

### leetcode557 反转字符串中的单词3

```
思路：逐个解析s，没遇到一个单词，则执行反转模块，需要begin，end来记录单词的首尾，需要考虑最后一个单词的特殊条件
```

```c++
string reverseWords(string s) {
        if (s.empty()) {
            string ans;
            return ans;
        }
        int begin = 0;
        int end = 0;
        for (int i = 0; i < s.size(); i++) {
            if (s.at(i) == ' ') {
                while (begin < end){
                    char tmp = s.at(begin);
                    s.at(begin) = s.at(end);
                    s.at(end) = tmp;
                    begin++;
                    end--;
                }
                begin = i + 1;
            } else {
                end = i;
            }
        }
        if (s.at(s.size() - 1) != ' '){
            while (begin < end){
                    char tmp = s.at(begin);
                    s.at(begin) = s.at(end);
                    s.at(end) = tmp;
                    begin++;
                    end--;
                }
        }
        return s;
    }
```

### leetcode26 删除排序数组中的重复项

```
思路：快慢指针，一个指针i用于遍历整个数组，另一个指针j用于指向待存入的位置，当i当前值的值和i-1不同时，则存入，i，j均+1，否则，i+1，j不变
```

```c++
int removeDuplicates(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        if (nums.size() == 1) {
            return 1;
        }
        int ans = 1;
        int j = 1;
        for (int i = 1; i < nums.size(); i++) {
            if (nums.at(i-1) != nums.at(i)) {
                nums.at(j++) = nums.at(i);
                ans++;
            }
        }
        return ans;
    }
```

### leetcode283 移动零

```
思路：快慢指针，指针j为慢指针，用于指向存储的非0元素的位置，快指针i用于遍历整个数组，如果当前i所指向的元素非0，则将其存入j指向的位置，并且记录下长度，根据记录的长度，将最后几位补为0
```

```c++
void moveZeroes(vector<int>& nums) {
        if (nums.empty()) {
            return;
        }
        int j = 0;
        int cnt = 0;
        for (int i = 0; i < nums.size(); i++) {
            if(nums.at(i) != 0) {
                nums.at(j++) = nums.at(i);
                cnt++;
            }
        }
        for (int i = cnt; i < nums.size(); i++) {
            nums.at(i) = 0;
        }
    }
```

