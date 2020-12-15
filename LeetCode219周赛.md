# 5625.比赛中的配对次数



n个队伍中选出一个冠军，所以要淘汰n-1个队伍，总共进行n-1次比赛。如果理解了就直接return n-1;

如果一下子没有看出来，则模拟题目即可。如果是偶数，则直接n/2加到总次数中，如果是奇数，也是n/2加到总数中，但是n=(n-1)/2+1，因为有支队伍直接晋级。

```c++
class Solution {
public:
    int numberOfMatches(int n) {
        return n-1;
    }
};
class Solution {
public:
    int numberOfMatches(int n) {
        int res=0;
        while(n!=1){
            if(n%2==0)
            {
                res+=n/2;
                n>>=1;
            }
            else
            {
                res+=n/2;
                n=(n-1)/2+1;
            }
        }
        return res;
    }
};
```

# 5626.十-二进制数的最少数目



没有前导0，那么就直观想一下，9999那就9个1111，如果是9998，那就是8个1111再加上1个1110，一共是9个，所以很显然，答案就是数中最大值。这个题很友善，给的是字符串，所以直接遍历找到最大值就可以了。

```c++
class Solution {
public:
    int minPartitions(string n) {
		int res=0;
        for(int i=0; i<n.length();i++)
        {
            res=max(res,n[i]);
        }
        return res;
    }
};
```

# 5627.石子游戏VII



这个石子的题目已经有这么多花样了。

这题目很友善，告诉了我们先手的一定能赢，后手的要尽力缩小差值，而先手的要尽力扩大差值，又根据题意，得分是去掉左侧或右侧石子后剩下的总和，而最终要求的是两人总分的差值。

我们设 sum\[i]\[j]为石子从i 到 j的总和；

dp\[i]\[j]是为在i到j区间里先手的总分减去后手的总分，也就是差值。

假设轮到我来选，选择移除最左侧的石子，那么我的得分就是sum\[i+1][j]，那么此时区间sum\[i+1][j]对方的得分是多少呢？我们不知道，但是也不需要知道，因为我们知道dp\[i+1][j]是两者的差值。

那么sum\[i+1]\[j]-dp\[i+1][j]

=  我当前的得分 - （对方的总分 - 我的总分）

= 我当前的得分 + 我的总分 - 对方的总分

= 我的新的总分 - 对方的总分 

那此时，不就恰恰是dp\[i]\[j]嘛。

同理，如果从左侧拿石子则换成\[i][j-1] ，轮到对方就把我和对方换一下。

然后我们来推一下状态转移方程。注意，我们要从只有一堆的时候扩展，扩展到0到n-1

1.最开始的时候：i==j ，dp\[i]\[j]=0，因为拿掉之后没得选了

2.当j-i==1时，也就是说只有两堆石子了，那么我肯定要拿掉较小的，获得最大得分，因为拿掉之后，对方再拿就没有分了，所以dp\[i][j]=max(stones\[i],stones[j]);

3.当j-i>1时，就想刚才上面分析的一样。

dp\[i][j]=max(sum\[i+1]\[j]-dp[i+1]\[j] , sum\[i][j-1]-dp\[i]\[j-1]);

最后返回dp\[0][n-1]即为所需的答案，表示剩下的石子为0到n-1时，能取得的最大价值差

```c++
class Solution {
public:
    int stoneGameVII(vector<int>& stones) {
        int n=stones.size();
        vector<vector<int>> sum(n,vector<int>(n,0));
        for(int i=0;i<n;i++)
        {
            for(int j=i;j<n;j++)
            {
                if(i==j) sum[i][j]=stones[i];
                else sum[i][j]=sum[i][j-1]+stones[j];
            }
        }
        vector<vector<int>> dp(n,vector<int>(n,0));
        for(int i=n-1;i>=0;i--)
        {
            for(int j=i+1;j<n;j++)
            {
                if(j-i==1) dp[i][j]=max(stones[i],stones[j]);
                else dp[i][j]=max(sum[i+1][j]-dp[i+1][j],sum[i][j-1]-dp[i][j-1]);
            }
        }
        return dp[0][n-1];
    }
}
```

# 5245.堆叠长方体的最大高度

因为题目说了长方体可以随意摆放，所以我们可以先排序，也就是说，我们可以让最小的两条边作为底，最大的边做为高，这样就可以找到最高的。

然后我们就依次考虑第1到i-1个长方体，看看是否能够将第i个长方体拼接在它的下方，然后更新当前的最大值。

```c++
class Solution {
public:
    int maxHeight(vector<vector<int>>& cuboids) {
        int n=cuboids.size();
        for(auto &s : cuboids)
        {
            sort(s.begin(),s.end());
		}
        sort(cuboids.begin(),cuboids.end());
        vector<int>dp(n);
        int ans=0;
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<i;i++)
            {
                if(cuboids[i][1]<=cuboids[j][1] && cuboids[i][2]<=cuboids[j][2])
                {
                    dp[i]=max(dp[i],dp[j]);
				}
            }
            dp[i]+=cuboids[i][2];
            ans=max(ans,dp[i]);
        }
        return ans;   
    }
}
```

