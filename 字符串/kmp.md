# 字符串匹配问题   
简单来说就是要在字符串s中查找某个字符串p是否出现。  
# 时间复杂度  
假设字符串长为n，需要找的字符串长为m，时间复杂度大约为O(n+m)  
# 方法简介  
在kmp算法中，对于s（从这里边找）中每个位置i，我们要找到最大的j满足s[i-j+1]...s[i]满足与p[1]...p[j]相同（p为目标字符串）  
如果s[i+1]和p[j+1]相等，且j!=m则i和j都向后移动   
如果不相等，则j应该回到满足串s[i-k+1]...s[i]和p[1]...p[k]完全相等，且k最大的位置，然后继续判断。   
如果s[i+1]和p[j+1]仍然不相等，我们就接着回退，直到相等或j=0为止。  
问题就来到了怎么求k。  
事实上k和s无关，我们要求的是最大的k满足k小于j，使得p[1]......p[k]和p[j-k+1]......p[j]相同  
所以可以用next数组来维护每个j对应的k  
求next数组，本质就是求每个前缀的最长相等真前缀和真后缀的长度  
设j=next[i-1]，表示p[0,1,,,i-1]的最长相等前后缀长度。则p[0,,,j-1]为最长前缀，p[i-j,,,,i-1]为最长后缀。  
如果p[i]==p[j]说明前后缀还可以延长，则有
```c++
next[i]=j+1;
```  
如果p[i]!=p[j]，则不能直接延长，应该找更短的相等前后缀
```c++ 
j=next[j-1];
```
如果j已经退到零仍然不相等，则
```c++
next[i]=0;
```

# 板子  
```c++
int n,m;//n为被查找s的长度，m为p的长度
int nxt[M+1],f[N+1];//N，M为最大可能值,f为匹配的长度
char s[N+2],p[M+2];

void kmp(){
    n = strlen(s+1);
    m = strlen(p+1);
    int j=0;
    nxt[1]=0;
    for(int i=2;i<=m;i++)//处理next
    {
        while(j>0&&p[j+1]!=p[i]){
            j=nxt[j];
        }
        if(p[j+1]==p[j]){
            j++;
        }
        nxt[i]=j;
    }

    j=0;
    for(int i =1;i<=n;i++){
        while((j==m)||(j>0&&p[j+1]!=s[i])){
            j=nxt[j];
        }
        if(p[j+1]==s[i]){
            j++;
        }
        f[i]=j;
    }
}
```
```c++
//deepseek版本
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;   // 按题目数据范围调整

int nxt[N];               // nxt[i] = p[1..i] 的最长相等真前后缀长度（1-based）
string s, p;              // 1-based 存储：s[1..n]，p[1..m]，s[0]/p[0] 是哨兵
int n, m;

// 预处理模式串 p 的 nxt 数组
void buildNext() {
    
    nxt[1] = 0;
    for (int i = 2, j = 0; i <= m; i++) {
        while (j && p[i] != p[j + 1]) j = nxt[j];
        if (p[i] == p[j + 1]) j++;
        nxt[i] = j;
    }
}

// 在 s 中匹配 p，返回所有匹配的起始位置（1-based）
vector<int> kmp() {
    vector<int> res;
    for (int i = 1, j = 0; i <= n; i++) {
        while (j && s[i] != p[j + 1]) j = nxt[j];
        if (s[i] == p[j + 1]) j++;
        if (j == m) {
            res.push_back(i - m + 1);
            j = nxt[j];              // 继续找重叠匹配
        }
    }
    return res;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    string a, b;
    cin >> a >> b;
    s = " " + a;                     // 前面补一个哨兵，实现 1-based
    p = " " + b;
    n = a.size();
    m = b.size();

    buildNext();

    // 输出 nxt 数组（按需）
    // for (int i = 1; i <= m; i++) cout << nxt[i] << ' ';
    // cout << '\n';

    for (int pos : kmp()) cout << pos << ' ';
    cout << '\n';

    return 0;
}

```
# 最小循环覆盖  
![](/images/字符串/最小循环覆盖.png)  
结论：长度=n-next[n]    
举例：对于abcabcabc来说，其next数组为000123456    
则将其向右平移9-6位后，重叠部分重合(这也是最小循环节)。则可易知前三位为最小循环覆盖    

