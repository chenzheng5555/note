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
    for (; i < k; ++i) {
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

        if (i == numsSize)
            break;
        n.v = nums[i], n.i = i;
        m.v = nums[j], m.i = j;
        if (x.v == y.v) {
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

