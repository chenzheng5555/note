#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

#### [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

#### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

#### [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

#### [6. Z 字形变换](https://leetcode-cn.com/problems/zigzag-conversion/)

#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

#### [8. 字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

#### [9. 回文数](https://leetcode-cn.com/problems/palindrome-number/)

#### [10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

#### [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

```c
#define ISLEFT(c) (c == '(' || c == '{' || c == '[')
#define ISMATCH(a, b) (a == '(' && b == ')' || a == '{' && b == '}' || a == '[' && b == ']')
#define MAXSIZE 10000
struct stack 
{
    char data[MAXSIZE/2 + 1];
    int top;
};
// 输入s [1,10000]
bool isValid(char *s) 
{
    struct stack st;
    st.top = -1;
    while (*s)
    {
        if (ISLEFT(*s) && st.top < MAXSIZE) //输入为左括号，且小于栈的大小
        {
            st.data[++st.top] = *s;
        }
        else if (st.top != -1 && ISMATCH(st.data[st.top], *s)) //输入右括号且与栈顶匹配
        {
            st.top--;
        }
        else //在字符串未读完的情况下，不可能匹配成功。
            return false; //break;
        s++;
    }
    return st.top == -1; //栈清空，字符串全匹配
}

```

#### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```c
#include <stdio.h>
#include <stdlib.h>
//Definition for singly-linked list.
struct ListNode
{
    int val;
    struct ListNode *next;
};

struct ListNode *mergeTwoLists(struct ListNode *l1, struct ListNode *l2)
{
    struct ListNode *head = (struct ListNode *)malloc(sizeof(struct ListNode));
    head->next = NULL;
    struct ListNode *p = head;

    while (l1 && l2)
    {
        if (l1->val > l2->val)
        {
            p->next = l2;
            l2 = l2->next;
        }
        else
        {
            p->next = l1;
            l1 = l1->next;
        }
        p = p->next;
    }

    if (l1)
        p->next = l1;
    else
        p->next = l2;
    
    p=head->next;
    free(head);
    return p;
}

//递归方法
struct ListNode *mergeTwoLists(struct ListNode *l1, struct ListNode *l2)
{
    if (!l1)
        return l2;
    if (!l2)
        return l1;
    if (l1->val < l2->val)//当前l1小，则头应该为l1,让接下来的l1和l2比较。
    {
        l1->next = mergeTwoLists(l1->next, l2);
        return l1;
    }
    l2->next = mergeTwoLists(l1, l2->next);
    return l2;
}
/*
//根据数组创建无头链表
struct ListNode *creat(int *array, int size)
{
    struct ListNode *q, *head = NULL;
    int i = 0;
    while (i < size)
    {
        struct ListNode *p = (struct ListNode *)malloc(sizeof(struct ListNode));
        p->val = array[i++];
        p->next = NULL;
        if (!head)
            head = p, q = p;
        else
            q->next = p;
        q = p;
    }
    return head;
}

void print(struct ListNode *p)
{
    printf("p=[");
    while (p)
    {
        printf("%d,", p->val);
        p = p->next;
    }
    printf("]\n");
}

int main()
{
    freopen("data.txt", "r", stdin);
    struct ListNode *p[2];
    int N[2];
    //vscode的launch.json中的cwd不是当前工作目录，"D:\\mingw64\\bin"，使得freopen("data.txt","r",stdin)出错。使用gcc test.c;a.exe却不会出错。
    while (scanf("%d %d",&N[0],&N[1]) != EOF) 
    {
        int arr[2][30];
        for (int i = 0; i < 2; ++i)
        {
            int j = 0;
            while (j < N[i])
            {
                scanf("%d", &arr[i][j++]);
            }
            p[i] = creat(arr[i], N[i]);
            print(p[i]);
        }
        print(mergeTwoLists(p[0], p[1]));
    }
    fclose(stdin);
    return 0;
}*/
```

#### [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```c
void generate_str(int i, int j, int *k, int n, char *stack, char **result)
{
    if(i<j)return;//当前字符串，左括号数量少于右括号数量，则这个字符串肯定不满足要求；
    if (i < n)
    {
        stack[i + j] = '(';
        generate_str(i + 1, j, k, n, stack, result);
    }
    if (j < n) //if(i<j)可以合并进来，if(i>j) 表示当左括号多余右括号，才可以添加右括号。
    {
        stack[i + j] = ')';
        generate_str(i, j + 1, k, n, stack, result);
    }
    if (i == j&&i==n)//所有括号添加完毕,
    {
        char *s = (char *)malloc(sizeof(char) * n * 2 + 1);
        strcpy(s, stack);
        result[*k] = s;
        *k = *k + 1;
    }
}

char **generateParenthesis(int n, int *returnSize)
{
    int left = 0, right = 0;
    char *s = (char *)malloc(sizeof(char) * n * 2 + 1);
    char **result = (char **)malloc(sizeof(char *) * n);
    s[n * 2] = 0;
    generate_str(0, 0, returnSize, n, s, result);
    free(s);
    return result;
}
```

#### [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

```c
//还可以用桶排序。分而治之，递归，依次合并两个
#define MAXSIZE 10000
#define MAXVALUE 10001
//调整第i个节点为根的子堆，
void heapAdjust(struct ListNode **heap, int size, int i)
{
    heap[0] = heap[i];
    int son = i * 2;
    while (son <= size)
    {
        if (son < size && heap[son + 1]->val < heap[son]->val)//比较两个子节点的大小
            son++;
        if (heap[0]->val > heap[son]->val)//比较最小子节点和父节点的大小
        {
            heap[son / 2] = heap[son];
            son *= 2;
        }
        else//子树满足最小堆的要求
            break;
    }
    heap[son / 2] = heap[0];
}

struct ListNode *mergeKLists(struct ListNode **lists, int listsSize)
{
    //int *p[10] 指针数组，10个int指针构成的数组，int a[3][10]; p[1]=a[1],p++指向a[1][1]
    //int (*p)[10] 数组指针，指向数组长度为10的指针，p=a;p++指向a[1]
    struct ListNode *heap[MAXSIZE + 1] = {NULL}; //初始化指针，
    struct ListNode *head = (struct ListNode *)malloc(sizeof(struct ListNode));
    struct ListNode *p = head;
    int size = 0;
    for (int i = 0; i < listsSize; ++i)
    {
        if (lists[i])
            heap[++size] = lists[i];
    }
    //从有子节点的点开始建最小堆
    for (int i = size / 2; i > 0; --i)
    {
        heapAdjust(heap, size, i);
    }

    while (size > 1)
    {
        p->next = heap[1];
        p = heap[1];
        if (!heap[1]->next)//链表遍历完，由堆里最后一个链表替代。堆大小减一
        {
            heap[1] = heap[size];
            size--;
        }
        else
            heap[1] = heap[1]->next;
        heapAdjust(heap, size, 1);
    }
    p->next = heap[1];
    p = head->next;
    free(head);
    return p;
}

```

#### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

```c
// 1-2-3-4     
struct ListNode* swapPairs(struct ListNode* head){
    struct ListNode *p=head;
    struct ListNode *q=(struct ListNode *)malloc(sizeof(struct ListNode));
    q->next=head;
    head=q;
    while(p){           
        if(p->next){               //head(q)-1(p)-2-3-4
            q->next=p->next;       //head(q)-2-3-4  1(p)-2-3-4
            p->next=p->next->next; //1(p)-3-4  head(q)-2-3-4
            q->next->next=p;       //head(q)-2-1(p)-3-4 
            q=p;
            p=p->next;
        }else break;
    }
    p=head->next;
    free(head);
    return p;
}
```

#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```c
//另外可以先得长度为k的链表，再进行反转。
struct ListNode *reverseKGroup(struct ListNode *head, int k)
{
    if (k < 2 || !head || !head->next)
        return head;
    struct ListNode *p = (struct ListNode *)malloc(sizeof(struct ListNode));
    p->next = head;
    struct ListNode *start = p;
    while (head)
    {
        struct ListNode *pre = NULL, *begin = head;
        int i = 0;
        while (i < k && head)// 每前进一步都进行反转
        {
            struct ListNode *tmp = head->next;
            head->next = pre;
            pre = head;
            head = tmp;
            ++i;
        }
        if (i == k)//长度满足，更新前起始节点
        {
            start->next = pre;
            start = begin;
        }
        else if (!head)//长度不满足，再次反转最后一段。
        {
            head = pre;
            pre = NULL;
            while (head)
            {
                struct ListNode *tmp = head->next;
                head->next = pre;
                pre = head;
                head = tmp;
            }
            start->next = pre;
        }
    }
    head = p->next;
    free(p);
    return head;
}

```

#### [724. 寻找数组的中心索引](https://leetcode-cn.com/problems/find-pivot-index/)

```c
//失败案例，
int pivotIndex(int *nums, int numsSize)
{
    int *rsum = (int *)malloc(sizeof(int) * (numsSize + 1));
    int *lsum = (int *)malloc(sizeof(int) * (numsSize + 1));
    rsum[numsSize] = lsum[0] = 0;
    for (int i = 0; i < numsSize; ++i)  //从左往右求和，i左边的和
        lsum[i + 1] = lsum[i] + nums[i];
    for (int i = numsSize - 1; i >= 0; --i) //从右往左求和，i右边的和
        rsum[i] = rsum[i + 1] + nums[i];
    for (int i = 0; i < numsSize; ++i)
    {
        if (lsum[i] == rsum[i + 1]) 
        {
            free(rsum);
            free(lsum);
            return i;
        }
    }
    free(rsum);
    free(lsum);
    return -1;
}
//总的和减去右边的和得左边的和。
int pivotIndex(int *nums, int numsSize)
{
    int sum = 0, leftSum = 0;
    for (int i = 0; i < numsSize; ++i)
        sum += nums[i];
    for (int i = 0; i < numsSize; ++i)
    {
        sum -= nums[i];
        if (leftSum == sum)
            return i;
        leftSum += nums[i];
    }
    return -1;
}
```

#### [888. 公平的糖果棒交换](https://leetcode-cn.com/problems/fair-candy-swap/)

```c
//是否存在两者总和之差==两个元素之差的2倍；其他方法可以以空间换时间，
int* fairCandySwap(int* A, int ASize, int* B, int BSize, int* returnSize)
{
    int ASum = 0, BSum = 0;
    for (int i = 0; i < ASize; ++i)
        ASum += A[i];
    for (int i = 0; i < BSize; ++i)
        BSum += B[i];

    int k = ASum - BSum;
    int* ans = (int*)malloc(sizeof(int) * 2);
    for (int i = 0; i < ASize; ++i) {
        for (int j = 0; j < BSize; ++j) {
            if (2 * (A[i] - B[j]) == k) {
                ans[0] = A[i], ans[1] = B[j];
                *returnSize = 2;
                return ans;
            }
        }
    }
    return ans;
}
```



#### [1631. 最小体力消耗路径](https://leetcode-cn.com/problems/path-with-minimum-effort/)

+ 思路
  1. 二分法；给定一个值当作最小值，看看是否可以连通左上角和右下角（深度搜索或广度搜索）。
  2. 

```c
//超时，时间为3^(m*n)
//方法：随便先连通一条路径，然后修改路径中最大高度的前一位置的方向，看看能否找到更小的路径
#define MAXHEIGHT 10e6
#define MAXCOLSIZE 101
#define min(a, b) (a > b ? b : a)
#define max(a, b) (a > b ? a : b)

void print(int **heights, int row, int col)
{
    system("cls");
    for (int i = 0; i <= row; ++i){
        for (int j = 0; j <= col; ++j){
            if (heights[i][j])
                printf("%1d", heights[i][j] % 10);
            else
                printf(" ");
        }
        printf("\n");
    }
    Sleep(500);
}


// priorMax 从起始点沿着路径到当前节点的高度；
int gonext(int r, int c, int row, int col, int **heights, int priorMax, int *globle)
{
    print(heights, row, col);
    if (r == row && c == col){
        *globle = priorMax;
        return 0;
    }

    int t = heights[r][c];
    heights[r][c] = 0;
    int nextMin = MAXHEIGHT;
    for (int k = 0; k < 4; ++k){
        if (*globle == priorMax){ //返回到最大高度的前一位置
            print(heights,row,col);
            heights[r][c] = t;
            return nextMin;
        }
        int flag = 0;
        // 往下、往右、往上、往左
        if (k == 0 && ++r <= row || k == 1 && ++c <= col ||
            k == 2 && --r >= 0 && c < col && c > 0 ||
            k == 3 && --c >= 0 && r < row && r > 0)
            flag = 1;
        if (flag && heights[r][c]){         //下一节点可达
            int i = abs(t - heights[r][c]); //当前节点到下一节点的高度
            if (i < *globle){               //可能存在更小高度的路径。
                int m = max(priorMax, i);
                int j = gonext(r, c, row, col, heights, m, globle); //下一节点到终点的最小高度
                m = max(i, j);
                nextMin = min(m, nextMin);
            }
        }
        if (k == 0 && --r || k == 1 && --c || k == 2 && ++r || k == 3 && ++c);
    }

    heights[r][c] = t;
    return nextMin;
}
int minimumEffortPath(int **heights, int heightsSize, int *heightsColSize)
{
    int minColSize = MAXCOLSIZE;
    for (int i = 0; i < heightsSize; ++i){
        if (minColSize > heightsColSize[i])
            minColSize = heightsColSize[i];
    }
    if (heightsSize == 1 && minColSize == 1)
        return 0;
    int globle = MAXHEIGHT;
    gonext(0, 0, heightsSize - 1, minColSize - 1, heights, 0, &globle);
    return globle;
}
//转化为循环
char x[5][3] = { "*", "||", "->", "||", "<-" };
int dirs[4][2] = { { 1, 0 }, { 0, 1 }, { -1, 0 }, { 0, -1 } }; //下，右，上，左
void print(int* arr, int row, int col)
{
    system("cls");
    for (int i = 0; i < col; ++i)
        printf("%2d", i);
    printf("\n");
    for (int i = 0; i < row; ++i) {
        printf("%2d ", i);
        for (int j = 0; j < col; ++j) {
            int k = i * col + j;
            if (arr[k])
                printf("%s", x[arr[k]]);
            else
                printf("  ");
        }
        printf("\n");
    }
    // Sleep(100);
}
void HideCursor()
{
    CONSOLE_CURSOR_INFO cursor_info = {1, 0};
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &cursor_info);
}
int minimumEffortPath(int **heights, int heightsSize, int *heightsColSize)
{
    int row = heightsSize;
    int col = heightsColSize[0];
    if (row == 1 && col == 1)
        return 0;
    int globle = MAXHEIGHT;
    int q[row * col][2];
    int cnt = 0;
    q[cnt][0] = 0, q[cnt][1] = 0;
    int seen[row * col], priorMax[row * col];
    memset(seen, 0, sizeof(seen));
    memset(priorMax, 0, sizeof(priorMax));
    seen[0] = 1;

    int x, y;
    while (cnt >= 0){
        int k = 0;
        while (k < 4){  //依次访问四个方向
            x = q[cnt][0], y = q[cnt][1];
            int nx = x + dirs[k][0];
            int ny = y + dirs[k][1];
            if ((k == 0 && nx < row ||
                 k == 1 && ny < col ||
                 k == 2 && nx >= 0 && ny < col - 1 && ny > 0 ||
                 k == 3 && ny >= 0 && nx < row - 1 && nx > 0) &&
                !seen[nx * col + ny]){
                int t = abs(heights[nx][ny] - heights[x][y]);
                if (t < globle){
                    cnt++;
                    priorMax[cnt] = priorMax[cnt - 1] > t ? priorMax[cnt - 1] : t;
                    q[cnt][0] = nx, q[cnt][1] = ny;
                    printf("(%d,%d)[%d-%d]->", nx, ny, t, globle);
                    seen[nx * col + ny] = k + 1;
                    break;
                }
            }
            k++;
            if (x == row - 1 && y == col - 1) //到达终点
                globle = priorMax[cnt];
            
            while (cnt >= 0 && (k == 4 || globle == priorMax[cnt])){ //方向访问完，或最大值出现在前面，回退一步
                //print(seen, row, col);
                seen[q[cnt][0] * col + q[cnt][1]] = 0;
                cnt--;

                if (cnt < 0)
                    break;

                x = q[cnt][0], y = q[cnt][1];
                if (q[cnt + 1][0] > x){ //以前往下，现在往右
                    k = 1;
                }else if (q[cnt + 1][1] > y){ //以前往右，现在往上
                    k = 2;
                }else if (q[cnt + 1][0] < x){ //以前往上，现在往左
                    k = 3;
                }
            }
        }
    }
    return globle;
}
```

