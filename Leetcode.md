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
//方法：堆栈使用的典型案例，左括号压入，遇到右括号弹出。其他情况无效。
#define ISLEFT(c) (c == '(' || c == '{' || c == '[')
#define ISMATCH(a, b) (a == '(' && b == ')' || a == '{' && b == '}' || a == '[' && b == ']')
#define MAXSIZE 10000
struct stack {
    char data[MAXSIZE/2 + 1];
    int top;
};
// 输入s [1,10000]
bool isValid(char *s) 
{
    struct stack st;
    st.top = -1;
    while (*s){
        if (ISLEFT(*s) && st.top < MAXSIZE) //输入为左括号，且小于栈的大小
            st.data[++st.top] = *s;
        else if (st.top != -1 && ISMATCH(st.data[st.top], *s)) //输入右括号且与栈顶匹配
            st.top--;
        else //在字符串未读完的情况下，不可能匹配成功。
            return false; //break;
        s++;
    }
    return st.top == -1; //栈清空，字符串全匹配
}

```

#### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```c
//方法：依次比较两个链表的头，指向值小的链表，该链表指针往后移一位，直到有个链表遍历完。
#include <stdio.h>
#include <stdlib.h>
struct ListNode {
    int val;
    struct ListNode *next;
};

struct ListNode *mergeTwoLists(struct ListNode *l1, struct ListNode *l2)
{
    //增加头节点，不需要单独处理开始节点。
    struct ListNode *head = (struct ListNode *)malloc(sizeof(struct ListNode));
    head->next = NULL;
    struct ListNode *p = head;

    while (l1 && l2){
        if (l1->val > l2->val){
            p->next = l2;
            l2 = l2->next;
        }else{
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
    if (l1->val < l2->val){ //当前l1小，则头应该为l1,让接下来的l1和l2比较。
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
    while (i < size){
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
    while (p){
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
    while (scanf("%d %d",&N[0],&N[1]) != EOF) {
        int arr[2][30];
        for (int i = 0; i < 2; ++i){
            int j = 0;
            while (j < N[i])
                scanf("%d", &arr[i][j++]);
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
//方法：只有当当前字符串满足(左括号数量>=右括号数量)，才有可能有效，当左右括号都用完了就得到一条有效字符串。
void generate_str(int i, int j, int *k, int n, char *stack, char **result)
{
    if(i<j) return;//当前字符串，左括号数量少于右括号数量，则这个字符串肯定不满足要求；
    if (i < n){
        stack[i + j] = '(';
        generate_str(i + 1, j, k, n, stack, result);
    }
    if (j < n){ //if(i<j)可以合并进来，if(i>j) 表示当左括号多余右括号，才可以添加右括号。
        stack[i + j] = ')';
        generate_str(i, j + 1, k, n, stack, result);
    }
    if (i == j&&i==n){ //所有括号添加完毕,
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
//方法：由各个链表的当前指针构成最小堆，然后在推根节点的链表指向下一位(不为空)或用堆最后一个节点的链表代替(为空)，然后调整堆。直到堆为空。
//还可以用桶排序。分而治之，递归，依次合并两个
#define MAXSIZE 10000
#define MAXVALUE 10001
//调整第i个节点为根的子堆，
void heapAdjust(struct ListNode** heap, int size, int i)
{
    heap[0] = heap[i];
    int son = i * 2;
    while (son <= size) {
        if (son < size && heap[son + 1]->val < heap[son]->val) //比较两个子节点的大小
            son++;
        if (heap[0]->val > heap[son]->val) { //比较最小子节点和父节点的大小
            heap[son / 2] = heap[son];
            son *= 2;
        } else //子树满足最小堆的要求
            break;
    }
    heap[son / 2] = heap[0];
}

struct ListNode* mergeKLists(struct ListNode** lists, int listsSize)
{
    //int *p[10] 指针数组，10个int指针构成的数组，int a[3][10]; p[1]=a[1],p++指向a[1][1]
    //int (*p)[10] 数组指针，指向数组长度为10的指针，p=a;p++指向a[1]
    struct ListNode* heap[MAXSIZE + 1] = { NULL }; //初始化指针，
    struct ListNode* head = (struct ListNode*)malloc(sizeof(struct ListNode));
    struct ListNode* p = head;
    int size = 0;
    for (int i = 0; i < listsSize; ++i) {
        if (lists[i])
            heap[++size] = lists[i];
    }
    //从有子节点的点开始建最小堆
    for (int i = size / 2; i > 0; --i)
        heapAdjust(heap, size, i);

    while (size > 1) {
        p->next = heap[1];
        p = heap[1];
        if (!heap[1]->next) { //链表遍历完，由堆里最后一个链表替代。堆大小减一
            heap[1] = heap[size];
            size--;
        } else
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
//方法：先前指针q、当前指针p、下一个指针p->next,修改三者的指向即可。如1-2-3-4
struct ListNode* swapPairs(struct ListNode* head)
{
    struct ListNode* p = head;
    struct ListNode* q = (struct ListNode*)malloc(sizeof(struct ListNode));
    q->next = head;
    head = q;
    while (p) {
        if (p->next) { //head(q)-1(p)-2-3-4
            q->next = p->next; //head(q)-2-3-4  1(p)-2-3-4
            p->next = p->next->next; //1(p)-3-4  head(q)-2-3-4
            q->next->next = p; //head(q)-2-1(p)-3-4
            q = p;
            p = p->next;
        } else
            break;
    }
    p = head->next;
    free(head);
    return p;
}
```

#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```c
//方法：之前指针、当前指针，边遍历边反转。当满足k步后，连接上一段和该段；不满k步，再反转回去。
//另外可以先得长度为k的链表，再进行反转。
struct ListNode* reverseKGroup(struct ListNode* head, int k)
{
    if (k < 2 || !head || !head->next)
        return head;
    struct ListNode* p = (struct ListNode*)malloc(sizeof(struct ListNode));
    p->next = head;
    struct ListNode* start = p;
    while (head) {
        struct ListNode *pre = NULL, *begin = head;
        int i = 0;
        while (i < k && head){ // 每前进一步都进行反转
            struct ListNode* tmp = head->next;
            head->next = pre;
            pre = head;
            head = tmp;
            ++i;
        }
        if (i == k) { //长度满足，更新前起始节点
            start->next = pre;
            start = begin;
        } else if (!head) { //长度不满足，再次反转最后一段。
            head = pre;
            pre = NULL;
            while (head) {
                struct ListNode* tmp = head->next;
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

#### [119. 杨辉三角 II](https://leetcode-cn.com/problems/pascals-triangle-ii/)

```c
//方法：杨辉三角为二项式的系数，有规律，组合。
//也可以一层一层的算。
int *getRow(int rowIndex, int *returnSize)
{
    int *ans = (int *)malloc(sizeof(int) * (rowIndex + 1));
    ans[0] = ans[rowIndex] = 1;
    for (int i = 1; i < rowIndex; ++i){
        if (i <= rowIndex / 2)
            ans[i] = (long long)ans[i - 1] * (rowIndex - i + 1) / i; //注意运算可能溢出。
        else
            ans[i] = ans[rowIndex - i];
    }
    *returnSize = rowIndex + 1;
    return ans;
}
```

#### [424. 替换后的最长重复字符](https://leetcode-cn.com/problems/longest-repeating-character-replacement/)

```c
//方法：滑动窗口，要求的字符串是连续的，替换的字符是子串中出现次数较少的字符，且这些字符的总数不大于k。
//给定一个窗口，找里面字符出现最多的字符作为重复字符。窗口长度-其个数<=k，所以只有最大出现次数变大，窗口才可能变大。
//依次往窗口里添加字符，在满足要求的情况下，窗口不断增大，当不满足时，因为是连续的，去掉最左边。
int characterReplacement(char* s, int k)
{
    int num[26]; //满足要求的区间内各字符的个数
    memset(num, 0, sizeof(num));
    int n = strlen(s);
    int maxn = 0;
    int left = 0, right = 0;
    while (right < n) {
        num[s[right] - 'A']++; //字符数加一
        maxn = max(maxn, num[s[right] - 'A']); //区间内出现的最大字符个数
        if (right - left + 1 - maxn > k) {      //区间内需要替换的字符个数>最大替换次数，不满足要求，去掉最左边的字符。
            num[s[left] - 'A']--;               //则左边字符的个数减一
            left++;
        }
        right++;
    }
    return right - left;
}
```

#### [448. 找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)

```c
//方法：基本方法是申请一个大小为n的数组，用来标记下标为i的数是否出现。因为值全部小于n，所以可以利用原数组来表示，如果下标为i的数出现，则i位置上的数就加n。
//或者可以进行排序，排第i个位置时，往后查找为值为i的地址，并交互，则缺失的数会找不到。
int *findDisappearedNumbers(int *nums, int numsSize, int *returnSize)
{
    for (int i = 0; i < numsSize; ++i)
        nums[(nums[i] - 1) % numsSize] += numsSize;
    int *ans = (int *)malloc(sizeof(int) * numsSize);
    *returnSize = 0;
    for (int i = 0; i < numsSize; ++i){
        if (nums[i] <= numsSize)
            ans[(*returnSize)++] = i + 1;
    }
    return ans;
}
```



#### [480. 滑动窗口中位数](https://leetcode-cn.com/problems/sliding-window-median/)

```c
//方法：构建两个堆，一个最大堆存储前半部分，一个最小堆存储后半部分，每次只需比较两个堆顶的值和新加入的值，并删掉窗口去掉的值，然后不断维护两个堆。
//窗口长度为奇数时，最大堆存储的个数比最小堆多一个，则大堆堆顶的值即为中位数。
#define MAXSIZE 100000
typedef struct node {
    int i, v;
} node;

void maxHeapPush(node* heap, int* s, node k)
{
    int son = ++(*s);
    while (son / 2 > 0 && heap[son / 2].v < k.v) {
        heap[son] = heap[son / 2];
        son /= 2;
    }
    heap[son] = k;
}
void maxHeapPop(node* heap, int* s)
{
    if (*s == 0)
        return;
    //node k=heap[1];
    node v = heap[(*s)--];
    int son = 2;
    while (son <= *s) { //只需要与子节点中最大的比较，大于最大子节点，跳出，其他情况都需要交换。
        if (son + 1 <= *s && heap[son + 1].v > heap[son].v)
            son++;
        if (v.v < heap[son].v)
            heap[son / 2] = heap[son], son *= 2;
        else
            break;
    }
    heap[son / 2] = v;
}

void minHeapPush(node* heap, int* s, node k)
{
    int son = ++(*s);
    while (son / 2 > 0 && heap[son / 2].v > k.v) {
        heap[son] = heap[son / 2];
        son /= 2;
    }
    heap[son] = k;
}
void minHeapPop(node* heap, int* s)
{
    if (*s == 0)
        return;
    //node k=heap[1];
    node v = heap[(*s)--];
    int son = 2;
    while (son <= *s) {
        if (son + 1 <= *s && heap[son + 1].v < heap[son].v)
            son++;
        if (v.v > heap[son].v)
            heap[son / 2] = heap[son], son *= 2;
        else
            break;
    }
    heap[son / 2] = v;
}

void adjust(node* maxHeap, node* minHeap, int* maxCnt, int* minCnt, node k)
{
    node x = maxHeap[1], y = minHeap[1];
    if (*maxCnt <= *minCnt) {
        if (*maxCnt == 0 || k.v <= y.v) //最开始时，大堆个数为0，小堆个数也为0，直接将元素添加到大堆中。当k=2且，大堆里的元素删除了，此时maxCnt==0,也可直接添加元素，因为中位数为两个堆顶元素的平均值。
            maxHeapPush(maxHeap, maxCnt, k);
        else {
            minHeapPop(minHeap, minCnt);
            minHeapPush(minHeap, minCnt, k);
            maxHeapPush(maxHeap, maxCnt, y);
        }
    } else {  
        if (k.v >= x.v)//向小堆里添加元素时，大堆一定不为空，需要比较大堆顶和插入元素的大小
            minHeapPush(minHeap, minCnt, k);
        else {
            maxHeapPop(maxHeap, maxCnt);
            maxHeapPush(maxHeap, maxCnt, k);
            minHeapPush(minHeap, minCnt, x);
        }
    }
}

int maxHeapDeletek(node* heap, int* s, node k)
{
    int i = 1;
    while (i <= *s && k.i != heap[i].i)
        i++;
    if (i > *s)
        return 0;

    node x = heap[(*s)--];
    int son = i, p = son / 2;
    if (p > 0 && x.v > heap[p].v) {
        while (p > 0 && x.v > heap[p].v) {
            heap[son] = heap[p];
            son /= 2;
            p = son / 2;
        }
        heap[son] = x;
    } else {
        son = i * 2;
        while (son <= *s) {
            if (son + 1 <= *s && heap[son + 1].v > heap[son].v)
                son++;
            if (heap[son].v > x.v)
                heap[son / 2] = heap[son], son *= 2;
            else
                break;
        }
        heap[son / 2] = x;
    }
    return 1;
}
int minHeapDeletek(node* heap, int* s, node k)
{
    int i = 1;
    while (i <= *s && k.i != heap[i].i)
        i++;
    if (i > *s)
        return 0;

    node x = heap[(*s)--];
    int son = i, p = son / 2;
    if (p > 0 && x.v < heap[p].v) {
        while (p > 0 && x.v < heap[p].v) {
            heap[son] = heap[p];
            son /= 2;
            p = son / 2;
        }
        heap[son] = x;  //不能用heap[p * 2] = x;因为可能p*2和 p/2之前的值不一样,
    } else {
        son = i * 2;
        while (son <= *s) {
            if (son + 1 <= *s && heap[son + 1].v < heap[son].v)
                son++;
            if (heap[son].v < x.v)
                heap[son / 2] = heap[son], son *= 2;
            else
                break;
        }
        heap[son / 2] = x;
    }
    return 1;
}
// void print(node* heap, int cnt, char* s)
// {
//     printf("%s:[", s);
//     for (int i = 1; i <= cnt; ++i) {
//         printf("(i=%d,v=%d),", heap[i].i, heap[i].v);
//         printf("];");
//     }
// }
// void minCheck(node* heap, int cnt)
// {
//     for (int i = 1; i < cnt; ++i) {
//         if (i * 2 <= cnt && heap[i].v > heap[i * 2].v) {
//             printf("i=%d", i);
//             break;
//         }
//         if (i * 2 + 1 <= cnt && heap[i].v > heap[i * 2 + 1].v) {
//             printf("i=%d", i);
//             break;
//         }
//     }
// }
double* medianSlidingWindow(int* nums, int numsSize, int k, int* returnSize)
{
    *returnSize = numsSize - k + 1;
    double* ans = (double*)malloc(sizeof(double) * (*returnSize));

    node maxHeap[MAXSIZE], minHeap[MAXSIZE];
    int minCnt = 0, maxCnt = 0, i = 0;
    node n, m;
    for (; i < k; ++i) { //由第一个窗口构建堆
        n.v = nums[i], n.i = i;
        adjust(maxHeap, minHeap, &maxCnt, &minCnt, n);
    }

    //print(maxHeap, maxCnt, "max");
    //print(minHeap, minCnt, "min");
    //maxCheck(maxHeap, maxCnt), minCheck(minHeap, minCnt);
    for (int j = 0; i <= numsSize; ++i, ++j) {
        node x = maxHeap[1], y = minHeap[1];
        if (k % 2)
            ans[j] = (double)x.v;
        else
            ans[j] = ((double)x.v + (double)y.v) / 2;//两个相加可能超过int的范围

        if (i == numsSize)  //判断是否继续移动窗口
            break;
        n.v = nums[i], n.i = i;  
        m.v = nums[j], m.i = j;
        if (x.v == y.v) {  //找到被窗口移除的元素，并从堆中删除
            int f = maxHeapDeletek(maxHeap, &maxCnt, m);
            if (f == 0)
                minHeapDeletek(minHeap, &minCnt, m);
        } else if (m.v <= x.v) {
            maxHeapDeletek(maxHeap, &maxCnt, m);
        } else {
            minHeapDeletek(minHeap, &minCnt, m);
        }
        //maxCheck(maxHeap, maxCnt), minCheck(minHeap, minCnt);
        // print(maxHeap,maxCnt,"max");
        // print(minHeap,minCnt,"min");
        adjust(maxHeap, minHeap, &maxCnt, &minCnt, n);
        //maxCheck(maxHeap, maxCnt), minCheck(minHeap, minCnt);
        // print(maxHeap,maxCnt,"max");
        // print(minHeap,minCnt,"min");
    }
    return ans;
}
```

#### [485. 最大连续1的个数](https://leetcode-cn.com/problems/max-consecutive-ones/)

```c
//方法：遍历，如果出现0则计数清零，否则加一
int findMaxConsecutiveOnes(int *nums, int numsSize){
    int maxCnt = 0;
    for (int i = 0; i < numsSize; ++i){
        int cnt = 0;
        while (i < numsSize && nums[i])
            i++, cnt++;
        if (maxCnt < cnt)
            maxCnt = cnt;
    }
    return maxCnt;
}
```

#### [561. 数组拆分 I](https://leetcode-cn.com/problems/array-partition-i/)

```c
//方法：要使最小值的和最大，则需最小值尽可能大，所以为每个最大值配第二大值构成一对，则和最大。
int cmp(const void *a, const void *b){
    return *(int *)a - *(int *)b;
}
int arrayPairSum(int *nums, int numsSize){
    qsort(nums, numsSize, sizeof(int), cmp);
    int sum = 0;
    for (int i = 0; i < numsSize; i = i + 2)
        sum += nums[i];
    return sum;
}
```

#### [566. 重塑矩阵](https://leetcode-cn.com/problems/reshape-the-matrix/)

```c
int **matrixReshape(int **nums, int numsSize, int *numsColSize, int r, int c, int *returnSize, int **returnColumnSizes)
{//使用 matrixReshape(*nums[],numSize,numsColSize[],r,c,&returnSize,&*returnColumnSizes);后面两个因为要修改，所以需要再使用一层指针
    if (numsColSize[0] * numsSize != r * c || r == numsSize){
        *returnColumnSizes = numsColSize;
        *returnSize = numsSize;
        return nums;
    }
    int **ans = (int **)malloc(sizeof(int *) * r);
    int i = 0, k = 0;
    *returnColumnSizes = (int *)malloc(sizeof(int) * r); //加*表示修改其值。
    while (i < r * c){
        int j = 0;
        int *col = (int *)malloc(sizeof(int) * c);
        while (j < c){
            col[j++] = nums[i / numsColSize[0]][i % numsColSize[0]];
            i++;
        }
        ans[k] = col;
        (*returnColumnSizes)[k++] = c;  //*returnColumnSizes为一个数组地址，[]的优先级高于*
    }
    *returnSize = r;
    return ans;
}
```



#### [567. 字符串的排列](https://leetcode-cn.com/problems/permutation-in-string/)

```c
//方法：用双指针，统计s2两个指针中间的字符串各字符出现的次数是否等于s1中各字符的出现次数，且长度恰好等于s1的长度。
//另外可以用给定固定长度，判断字符出现次数是否满足要求。
bool checkInclusion(char *s1, char *s2)
{
    int len1 = 0;
    int cnt[26] = {0};
    while (*s1)  //统计s1字符串各字符出现的次数和字符串长度
        cnt[*s1 - 'a']++, s1++, len1++;

    int win = 0;
    char *left = s2, *right = s2;
    while (*right) {
        int i = *right - 'a';
        cnt[i]--, win++, right++;  //右指针向右移动，耗费一个字符c，长度加一
        while (cnt[i] < 0)         //耗费c的字符数多于s1中的c字符的出现次数，左指针右移，直到将超出耗费的字符c补回。
            cnt[*left - 'a']++, left++, win--;
        if (win == len1)
            return true;
    }
    return false;
}
```

#### [643. 子数组最大平均数 I](https://leetcode-cn.com/problems/maximum-average-subarray-i/)

```c
//因为长度确定，平均值最大，也即和最大,
double findMaxAverage(int* nums, int numsSize, int k)
{
    int sum = 0, i;
    for (i = 0; i < k; ++i) {
        sum += nums[i];
    }
    int j = 0, maxSum = sum;
    while (i < numsSize) {
        int x = nums[i++] - nums[j++]; //和变化的值
        if (x > 0 && x + sum > maxSum) { //最大和出现的情况
            maxSum = x + sum;
        }
        sum += x; //更新和
    }
    return (double)maxSum / k;
}
```

#### [665. 非递减数列](https://leetcode-cn.com/problems/non-decreasing-array/)

```c
//方法：每轮比较相连的三个数，nums[i-1],nums[i],nums[i+1]；
//1.如果两端满足要求（nums[i-1]<=nums[i+1]），如果中间的数不满足要求，修改中间的数；
//2.如果两端不满足要求，前两个数满足要求，修改最后一个数；
//3.如果后两个数满足要求，修改第一个数（该情况只可能出现在数组最前面。数组前面放一个最小的数，则变为第一种情况）；
//4.其他情况则至少修改两个数。一步一步移动，则该情况不会出现。
#define MIN -100000
bool checkPossibility(int *nums, int numsSize)
{
    int pre = MIN, k = 0;
    for (int i = 0; i < numsSize - 1; ++i){
        if (pre <= nums[i + 1]){
            if (nums[i] < pre || nums[i] > nums[i + 1]) //中间的数不满足要求
                k++, nums[i] = pre;
        }else if (nums[i] >= pre)//前两个数满足要求
            k++, nums[i + 1] = nums[i];
        else   //其他情况不会出现
            return false;
        if (k == 2)
            return false;
        pre = nums[i];
    }
    return true;
}
//leetcode上的方法，当不满足非递减要求时，可以：1）将nums[i]修改为nums[i+1]，或2）将nums[i+1]修改为nums[i]；
//情况1，降低前面的值（i==0），用掉一次修改机会，继续看后边是否满足；
//情况2，提升后面的值（i>0），还需要看nums[i+2]和nums[i]的关系，如果提升后的值大于随后的值，则不满足要求
bool checkPossibility(int* nums, int numsSize) {
    int cnt = 0;
    for (int i = 0; i < numsSize - 1; ++i) {
        int x = nums[i], y = nums[i + 1];
        if (x > y) {
            cnt++;
            if (cnt > 1) {
                return false;
            }
            if (i > 0 && y < nums[i - 1]) {
                nums[i + 1] = x;
            }
        }
    }
    return true;
}
```

#### [697. 数组的度](https://leetcode-cn.com/problems/degree-of-an-array/)

```c
//需要一个哈希表来存储各元素出现的次数，要求该元素出现次数最多且长度最小的子串，则该子串两边恰好为该元素，所以可以再加两个哈希表，用来存储元素的起始下标和终止下标。
#define M 50000
int findShortestSubArray(int *nums, int numsSize){
    int cnt[M], startIndex[M], endIndex[M];
    for (int i = 0; i < M; ++i)
        startIndex[i] = -1;
    memset(cnt, 0, sizeof(int) * M);
    int maxCnt = 0, minLen = M;
    for (int i = 0; i < numsSize; ++i){
        if (startIndex[nums[i]] == -1)
            startIndex[nums[i]] = i;
        endIndex[nums[i]] = i;
        cnt[nums[i]]++;
        int len = endIndex[nums[i]] - startIndex[nums[i]] + 1;
        if (cnt[nums[i]] > maxCnt || (cnt[nums[i]] == maxCnt && minLen > len)){
            minLen = len;
            maxCnt = cnt[nums[i]];
        }
    }
    return minLen;
}
```

#### [703. 数据流中的第 K 大元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

```c
//方法：使用一个最大堆和最小堆，大的数放在最小堆中，小的数放在最大堆里。
//大堆没有必要，因为构建好小堆后，如果新加的数大于堆顶，则将堆顶元素和替换为新元素，并调整。否则堆无需变化。
#define MAX 10001
typedef struct{
    int minHeap[MAX], maxHeap[MAX];
    int minSize, maxSize, k;
} KthLargest;
int less(int a, int b) { return a < b; }
int greater(int a, int b) { return a > b; }
void heapAdd(int *heap, int *size, int val, int (*cmp)(int, int)){//从堆末尾增加元素，并调整。
    int son = ++*size;
    while (son / 2 > 0 && cmp(val, heap[son / 2]))
        heap[son] = heap[son / 2], son /= 2;
    heap[son] = val;
}
void heapReplace(int *heap, int size, int *val, int (*cmp)(int, int)){ //出现较大的数，替换最小堆里最小的数
    int k = heap[1];
    int son = 2;
    while (son <= size){
        if (son + 1 <= size && cmp(heap[son + 1], heap[son]))
            son += 1;
        if (cmp(heap[son], *val))
            heap[son / 2] = heap[son];
        else
            break;
        son *= 2;
    }
    heap[son / 2] = *val;
    *val = k;
}
KthLargest *kthLargestCreate(int k, int *nums, int numsSize){
    KthLargest *heap = (KthLargest *)malloc(sizeof(KthLargest));
    heap->minSize = 0, heap->maxSize = 0, heap->k = k;
    int i;
    for (i = 0; i < numsSize && i < k; ++i)//构建小堆
        heapAdd(heap->minHeap, &heap->minSize, nums[i], less);
    for (i; i < numsSize; ++i){//调整两个堆
        if (nums[i] > heap->minHeap[1])
            heapReplace(heap->minHeap, heap->minSize, &nums[i], less);
        heapAdd(heap->maxHeap, &heap->maxSize, nums[i], greater);
    }
    return heap;
}
int kthLargestAdd(KthLargest *obj, int val){
    if (obj->k > obj->minSize) //可能最开始通过的元素个数小于k。
        heapAdd(obj->minHeap, &obj->minSize, val, less);
    else{
        if (obj->minHeap[1] < val)
            heapReplace(obj->minHeap, obj->minSize, &val, less);
        heapAdd(obj->maxHeap, &obj->maxSize, val, greater);
    }
    return obj->minHeap[1];
}
void kthLargestFree(KthLargest *obj){
    free(obj);
}
```

#### [724. 寻找数组的中心索引](https://leetcode-cn.com/problems/find-pivot-index/)

```c
//方法：总的和减去右边的和==左边的和。
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
```

#### [765. 情侣牵手](https://leetcode-cn.com/problems/couples-holding-hands/)

```c
//方法：所有情侣牵手成功，则必须要求在偶数位i与下一位奇数位i+1坐的是一对情侣。这样的位子对有rowsize/2对，如果某对位子上坐的不是一对情侣，只需交互其中一个即可。
//还可以使用并查集，连通分量
int minSwapsCouples(int *row, int rowSize){
    int index[rowSize];
    for (int i = 0; i < rowSize; ++i)
        index[row[i]] = i;
    int cnt = 0;
    for (int i = 0; i < rowSize; i = i + 2){//下一对位子
        if (abs(row[i] - row[i + 1]) != 1 || (row[i] + row[i + 1]) % 4 != 1){ //一对位子上坐的不是一对情侣
            if (row[i] % 2){   //为靠左边的人找到对应的情侣，将右边的人替换到左边情侣的位子上去，修改右边人的位子下标
                row[index[row[i] - 1]] = row[i + 1];
                index[row[i + 1]] = index[row[i] - 1];
            }else{   //同上，只是左边人的情侣取决于左边人的奇偶性；可以用异或运算
                row[index[row[i] + 1]] = row[i + 1];
                index[row[i + 1]] = index[row[i] + 1];
            }
            //row[index[row[i]^1]]=row[i+1];  
            //index[row[i+1]]=index[row[i]^1];
            cnt++;
        }
    }
    return cnt;
}
```

#### [766. 托普利茨矩阵](https://leetcode-cn.com/problems/toeplitz-matrix/)

```c
//方法：依次比较相邻两行（前一行0到n-1，后一行1到n）的元素即可。
bool isToeplitzMatrix(int **matrix, int matrixSize, int *matrixColSize){
    for (int i = 1; i < matrixSize; ++i){
        for (int j = 1; j < matrixColSize[i]; ++j){
            if (matrix[i][j] - matrix[i - 1][j - 1])
                return false;
        }
    }
    return true;
}
```



#### [888. 公平的糖果棒交换](https://leetcode-cn.com/problems/fair-candy-swap/)

```c
//方法：是否存在两者总和之差==两个元素之差的2倍；其他方法可以以空间换时间，遍历A时，查找B中是否存在abs(Asum-Bsum)/2-A[i]的值
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

#### [907. 子数组的最小值之和](https://leetcode-cn.com/problems/sum-of-subarray-minimums/)

```c
//时间复杂度O(n^2),超时。依次获取每个字串和其最小值。
#define MOD 1000000007
int sumSubarrayMins(int *arr, int arrSize)
{
    int sum = 0;
    for (int i = 0; i < arrSize; ++i){
        int min = arr[i];
        for (int j = i; j < arrSize; ++j){//依次得到从第j个位置开始的子集和相应的最小值。
            if (min > arr[j])
                min = arr[j];
            sum += min;
            sum %= MOD; //和超出范围，取模缩小值
        }
    }
    return sum;
}
//leetcode方法：找出当前元素作为最小值的左边界（最小值不唯一）和右边界（唯一最小值）。该区间以当前元素为最小值的子串个数为(i-prev[i])*(next[i]-i)。
#define MOD 1000000007
int sumSubarrayMins(int *arr, int arrSize){
    int prev[arrSize], next[arrSize];
    int minQue[arrSize], right = 0;//存储当前位置之前的最小值和前一位置的元素（下标）
    for (int i = 0; i < arrSize; ++i){ //小于当前元素的左边界
        while (right > 0 && arr[i] <= arr[minQue[right - 1]]) 
            right--;
        if (right == 0)
            prev[i] = -1;
        else
            prev[i] = minQue[right - 1];
        minQue[right++] = i;
    }
    right = 0;
    for (int i = arrSize - 1; i >= 0; --i){//小于等于当前元素的左边界
        while (right > 0 && arr[i] < arr[minQue[right - 1]])
            right--;
        if (right == 0)
            next[i] = arrSize;
        else
            next[i] = minQue[right - 1];
        minQue[right++] = i;
    }
    int sum = 0;
    for (int i = 0; i < arrSize; ++i){
        sum += (long)arr[i] * (i - prev[i]) * (next[i] - i) % MOD;
        sum %= MOD;
    }
    return sum;
}
```

#### [978. 最长湍流子数组](https://leetcode-cn.com/problems/longest-turbulent-subarray/)

```c
//方法：判断arr[i]和arr[i-1]的大小连续反转的次数，计数终止情况：1.当出现等于时，需要向后移动直到出现不等于；2.反转未出现时。
//其他方法用双指针和动态规划。
int maxTurbulenceSize(int *arr, int arrSize)
{
    if (arrSize < 2)
        return arrSize;
    int cnt = 1, i = 1, maxCnt = 1;
    while (i < arrSize){
        while (i < arrSize && arr[i] == arr[i - 1])  //找到不等于
            ++i;
        if (i == arrSize)
            break;
        bool pre = arr[i] > arr[i - 1]; //前一个大小比较
        cnt = 2, i++;
        while (i < arrSize){
            if (arr[i] == arr[i - 1])  //出现等于，终止
                break;
            bool cur = arr[i] > arr[i - 1];  //后一个大小比较
            if ((pre || cur) && !(pre && cur)){//异或
                i++, pre = cur, cnt++;
            }
            else
                break;
        }
        if (cnt > maxCnt)
            maxCnt = cnt;
    }
    return maxCnt;
}
```

#### [992. K 个不同整数的子数组](https://leetcode-cn.com/problems/subarrays-with-k-different-integers/)

```c
//方法：统计窗口里各数字出现的次数cnt[A[i]]，不同数字的个数num，如果num==k（条件，出现一个新的数）,则找到一个合适窗口，然后从左往右缩小窗口，看看还有多少窗口满足num==k（终止条件为有一个数的出现次数变为0），只考虑包含新加入数字在内的子窗口。
//双指针：
//关系：给定一个窗口，我们可以先求当前窗口最多包含k个不同数的子窗口个数（=包含1到k个不同数的子窗口数之和），减去最多包含k-1个不同数的子窗口的个数，即得包含k个不同数的子窗口数。
//统计新加一个数，会导致多少个新子窗口出现？，答案使L+1,L为之前窗口的大小，从右往左依次选不同长度0-L的子窗口加上新加入的数构成新的子窗口。
//新加一个数，如果不同数的个数小于k，统计新的子窗口个数；
//如果不同个数大于K,则应该缩小之前窗口，使其不同数个数变为k-1，即将左指针右移，直到有一个数的统计次数变为0；
int currentWindow(int *A, int *cnt, int left, int right) //当前窗口满足K==num,从左往右缩小窗口，有多少个窗口满足。
{
    int ans = 0;
    int i = left;
    while (i < right){
        if (--cnt[A[i++]])
            ans++, print(A, i, right);
        else
            break;
    }
    for (int j = left; j < i; ++j) //复原出现频率。
        cnt[A[j]]++;
    return ans;
}
int subarraysWithKDistinct(int *A, int ASize, int K)
{
    int cnt[ASize + 1];
    memset(cnt, 0, sizeof(int) * (ASize + 1));
    int right = 0, left = 0, num = 0, ans = 0;
    while (right < ASize){
        if (cnt[A[right]] == 0)
            num++;
        cnt[A[right]]++;
        if (num == K){  //满足要求的新窗口，
            ans++;
            ans += currentWindow(A, cnt, left, right);//包含最右端数的子窗口满足要求的个数。
        } else if (num > K) {  //新窗口不满足要求，
            while (left < right && --cnt[A[left++]]); //删掉一个数，使其出现次数为0
            ans++, num--;
            ans += currentWindow(A, cnt, left, right); //新满足的窗口
        }
        right++;
    }
    return ans;
}
```

#### [995. K 连续位的最小翻转次数](https://leetcode-cn.com/problems/minimum-number-of-k-consecutive-bit-flips/)

```c
//方法：1.一个字串翻转两次，相当于没有翻转；2.各子串的翻转顺序对结果没有影响。遇到0就翻转接下来的K个数，则时间复杂度O(NK),超时。
int minKBitFlips(int *A, int ASize, int K){
    int cnt = 0;
    for (int i = 0; i < ASize; ++i){
        if (A[i] == 0){
            if (i + K <= ASize){
                for (int j = 0; j < K; ++j)
                    A[i + j] ^= 1;
                cnt++;
            }else
                return -1;
        }
    }
    return cnt;
}
```



#### [1004. 最大连续1的个数 III](https://leetcode-cn.com/problems/max-consecutive-ones-iii/)

```c
//方法：统计窗口内0出现的次数，如次数大于k，则左右边界一起移动，否则，只移动右边界。
//leetcode使用求和后的差得到窗口间0的次数。
int longestOnes(int *A, int ASize, int K){
    int cnt[2] = {0, 0};
    int right = 0, left = 0, maxLen = 0;
    while (right < ASize){
        cnt[A[right++]]++;
        if (cnt[0] > K)
            cnt[A[left++]]--;
    }
    return right - left;
}
```

#### [1208. 尽可能使字符串相等](https://leetcode-cn.com/problems/get-equal-substrings-within-budget/)

```c
//方法：利用双指针，当差值小于剩余的maxCost后，右指针右移；大于，左指针右移，窗口只会越来越大，添加和删除元素后的窗口如果不满足要求，左右指针都会右移。
//类似424.替换后的最长重复字符
int equalSubstring(char* s, char* t, int maxCost)
{
    int left = 0, right = 0;
    while (*(s + right)) {
        maxCost -= abs(*(s + right) - *(t + right)); //减去新加入字符的消耗
        if (maxCost < 0) {
            maxCost += abs(*(s + left) - *(t + left));//去掉最左边的消耗，左指针右移
            left++;
        }
        right++;
    }
    return right - left;
}
```

#### [1423. 可获得的最大点数](https://leetcode-cn.com/problems/maximum-points-you-can-obtain-from-cards/)

```c
//先求靠前的k个值和，然后去掉最左边的元素，依次加入尾部的数，直到前考前的k个全部去掉。
//两边k个元素的和=总和-中间窗口的和
int maxScore(int *cardPoints, int cardPointsSize, int k)
{
    int sum = 0, i;
    for (i = 0; i < cardPointsSize && i < k; ++i)
        sum += cardPoints[i];
    if (k == cardPointsSize)
        return sum;
    int left = i - 1, right = cardPointsSize - 1, maxSum = sum;
    while (i > 0){
        sum -= cardPoints[--i];
        sum += cardPoints[right--];
        if (sum > maxSum)
            maxSum = sum;
    }
    return maxSum;
}
```

#### [1438. 绝对差不超过限制的最长连续子数组](https://leetcode-cn.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/)

```c
//使用堆来存储最大值和最小值，超时
typedef struct{
    int val, index;
} node;

int less(int a, int b){return a < b;}
int greater(int a,int b){return a>b;}
void heapPush(node *heap, int size, node k, int (*cmp)(int, int)){
    int son = size;
    while (son / 2 > 0){
        if (cmp(k.val, heap[son / 2].val))
            heap[son] = heap[son / 2];
        else
            break;
        son /= 2;
    }
    heap[son] = k;
}
void heapDelete(node *heap, int *size, int index, int (*cmp)(int, int)){
    int i = 1;
    while (i <= *size && heap[i].index != index)
        ++i;
    if (i == *size){
        (*size)--;
        return;
    }
    node k = heap[(*size)--];
    if (i / 2 > 0 && cmp(k.val, heap[i / 2].val)){
        heapPush(heap, *size, k, cmp);
    }else{
        int son = i * 2;
        while (son <= *size){
            if (son + 1 <= *size && cmp(heap[son + 1].val, heap[son].val))
                son++;
            if (cmp(heap[son].val, k.val))
                heap[son / 2] = heap[son];
            else
                break;
            son *= 2;
        }
        heap[son / 2] = k;
    }
}

void print(node *heap,int size){
    for(int i=1;i<=size;++i)printf("%d(%d)>",heap[i].val,heap[i].index);
    printf("\n");
}

int longestSubarray(int *nums, int numsSize, int limit)
{
    if (numsSize < 2)
        return 1;
    int left = 0, right = 0, maxLen = 0, maxSize = 0, minSize = 0;
    node maxHeap[numsSize+1], minHeap[numsSize+1];
    while (right < numsSize){
        node k = {nums[right], right};
        maxSize++, minSize++;
        heapPush(minHeap, minSize, k, less);
        heapPush(maxHeap, maxSize, k, greater);
        //print(maxHeap, maxSize), print(minHeap, minSize);
        while (maxHeap[1].val - minHeap[1].val > limit){
            heapDelete(minHeap, &minSize, left, less);
            heapDelete(maxHeap, &maxSize, left, greater);
            //print(maxHeap, maxSize), print(minHeap, minSize);
            left++;
        }
        right++;
        int len = right - left;
        if (len > maxLen)
            maxLen = len;
    }
    return maxLen;
}
//leetcode的方法：两个队列，分别存储到当前位置的窗口中可能成为最大值和最小值的元素。两个队列的头表示当前窗口内的最大值和最小值，当窗口内的最大值与最小值差不满足要求时，窗口左边界往右移动，直到删掉最大值或最小值。使得新窗口满足要求。
int longestSubarray(int *nums, int numsSize, int limit){
    int maxQue[numsSize], minQue[numsSize];
    int maxRight = 0, maxLeft = 0;
    int minRight = 0, minLeft = 0;
    int right = 0, left = 0;
    int maxLen = 0;
    while (right < numsSize){
        //新加入一个元素后，删除那些到当前位置的窗口中不可能成为最大值（最小值）的元素
        while (minLeft < minRight && minQue[minRight - 1] > nums[right])
            minRight--;
        while (maxLeft < maxRight && maxQue[maxRight - 1] < nums[right])
            maxRight--;
        //新加入的可能成为最大值（最小值）的元素
        maxQue[maxRight++] = nums[right];
        minQue[minRight++] = nums[right];
        //到当前位置的窗口内，最大值与最小值的差大于limit，不满足要求，移动左边界，直到删除最大值或最小值，使窗口满足要求。
        while (minLeft < minRight && maxLeft < maxRight && maxQue[maxLeft] - minQue[minLeft] > limit){
            if (nums[left] == minQue[minLeft])
                minLeft++;
            if (nums[left] == maxQue[maxLeft])
                maxLeft++;
            left++;
        }
        right++;
        maxLen = fmax(maxLen, right - left);
    }
    return maxLen;
}
```



#### [1631. 最小体力消耗路径](https://leetcode-cn.com/problems/path-with-minimum-effort/)

+ 思路
  1. 二分法；给定一个值当作最小值，看看是否可以连通左上角和右下角（深度搜索或广度搜索）。或者BFS+DP；BFS遍历图，并储存起始节点到当前节点的最小高度，
  
  2. 并查集；根据边的高度对边递增排序，然后依次合并边连接两个集合，并判断左上角和右下角是否在一个集合里。为每个节点编号，则表示一条边只需两个值。
  
  3. dijkstra：查找队列里的最小值，则起始点到该点的最小距离可以确定下来，
  
     ```c
     #define MAXSIZE 10001
     #define MAXHEIGHT 1000000
     typedef struct node {
         int k, v;
     } node;
     int dir[4][2] = { { 1, 0 }, { 0, 1 }, { -1, 0 }, { 0, -1 } };
     
     void minHeapPop(node* queue, int s, int i)//末尾节点代替根节点
     {
         queue[0] = queue[i];
         int son = i * 2;
         while (son <= s) {
             if (son + 1 <= s && queue[son + 1].v < queue[son].v)
                 son++;
             if (queue[0].v > queue[son].v) { //插入节点还是比最小子节点大，则最小子节点上移
                 queue[son / 2] = queue[son];
                 son *= 2;
             } else     //比子节点都小。
                 break;
         }
         queue[son / 2] = queue[0]; 
     }
     void minHeapPush(node* queue, int s) //在末尾插入节点
     {
         int son = s;
         queue[0] = queue[s];
         while (son / 2 > 0 && queue[0].v < queue[son / 2].v) {//父亲节点大于子节点
             queue[son] = queue[son / 2];//父亲节点下移
             son /= 2;
         }
         queue[son] = queue[0];
     }
     int minimumEffortPath(int** heights, int heightsSize, int* heightsColSize)
     {
         int row = heightsSize, col = heightsColSize[0];
         node queue[MAXSIZE];
         int N = row * col, cnt = 0;
         int visited[N], dis[N];
         memset(visited, 0, sizeof(visited));
         for (int i = 0; i < N; ++i)
             dis[i] = MAXHEIGHT;
     
         dis[0] = 0;
         queue[++cnt].k = 0, queue[cnt].v = 0;
         while (cnt >= 1) {
             int x = queue[1].k;
             queue[1] = queue[cnt--];
             minHeapPop(queue, cnt, 1);
     
             if (visited[x]) //已找到最小的边。
                 continue;
             if (x == N - 1)
                 break;
     
             visited[x] = 1;
             int c = x % col, r = x / col;
             for (int i = 0; i < 4; ++i) {
                 int dc = c + dir[i][1], dr = r + dir[i][0];
                 if (dr >= 0 && dr < row && dc >= 0 && dc < col ) {
                     int dx = dr * col + dc;
                     int h = abs(heights[dr][dc] - heights[r][c]);
                     int m = dis[x] > h ? dis[x] : h;
                     if (dis[dx] > m) {
                         dis[dx] = m;
                         queue[++cnt].k = dx, queue[cnt].v = dis[dx];
                         minHeapPush(queue, cnt);
                     }
                 }
             }
             // printf("push:");
             // for(int a=1;a<=cnt;++a)printf("%d(%d)->",queue[a].k,queue[a].v);
             // printf("\n");
         }
         return dis[N - 1];
     }
     
     ```

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

#### [1743. 从相邻元素对还原数组](https://leetcode-cn.com/problems/restore-the-array-from-adjacent-pairs/)

```c
//方法：哈希，存储节点可以连接的节点，中间节点为2个，两端节点为1个，从其中一个端点出发，遍历一遍即得数组。
#define M 100000
int* restoreArray(int** adjacentPairs, int adjacentPairsSize, int* adjacentPairsColSize, int* returnSize)
{
    *returnSize = adjacentPairsSize + 1;
    int to[2 * M + 1][2];  //在本地笔记本上出错，放在函数外面可以通过。
    int cnt[2 * M + 1];
    memset(cnt, 0, sizeof(cnt));
    for (int i = 0; i < adjacentPairsSize; ++i) {
        int x = adjacentPairs[i][0] + M, y = adjacentPairs[i][1] + M;
        to[x][cnt[x]++] = y;
        to[y][cnt[y]++] = x;
    }
    int* arr = (int*)malloc(sizeof(int) * (adjacentPairsSize + 1));
    int k = 0, i;
    for (i = 0; i < M * 2 + 1; ++i) {
        if (cnt[i] == 1) {
            break;
        }
    }
    memset(cnt,0,sizeof(cnt));  //用来表示节点是否访问过
    while (k <= adjacentPairsSize) {
        arr[k++] = i - M;
        cnt[i]=1;
        int j=to[i][0];
        if(cnt[j])i=to[i][1];
        else i=j;
    }
    return arr;
}

```

#### [1742. 盒子中小球的最大数量](https://leetcode-cn.com/problems/maximum-number-of-balls-in-a-box/)

```c
//方法：不需要每个数字都求一下各位数之和，其和与前一位数字k-1、k/10有关。可以减少一些操作。
int sum(int k)
{
    int s = 0;
    while (k) {
        s += (k % 10);
        k /= 10;
    }
    return s;
}
int countBalls(int lowLimit, int highLimit)
{
    int cnt[46], i = lowLimit, s;
    memset(cnt, 0, sizeof(cnt));
    while (lowLimit <= highLimit) {
        s = sum(lowLimit);
        cnt[s]++;
        int k = (lowLimit / 10 + 1) * 10;  //找到下一次发生进位的地方，即不满足数字加一，和加一规则的位置。
        if (k > highLimit)
            k = highLimit;
        while (++lowLimit < k) {
            cnt[++s]++;
        }
    }
    int m = 0;
    for (int i = 1; i < 46; ++i) {
        if (m < cnt[i])
            m = cnt[i];
    }
    return m;
}
```

