# 典型题

最长回文子串：

* 动态规划(时间复杂度为$O(n^2)$)

  状态转移方程：

  $\operatorname{dp}[j][i]=\left\{\begin{array}{cl}{\text {true},} & {j=i} \\ {\operatorname{str}[i]=} & {\operatorname{str}[j], \quad i-j=1} \\ {\operatorname{str}[i]=} & {\operatorname{str}[j] \& \& d p[j+1][i-1], \quad i-j>1}\end{array}\right.$

  ```c++
  int n=s.size();
  vector<vector<bool>> dp( n,vector<bool>(n,false) );
  int max=1; 
  for(int i=0;i<n;i++){
      for(int j=0;j<=i;j++){
          /*确定dp[j][i]的值*/
          if(i==j) dp[j][i]=true;
          else if(i-j==1) dp[j][i]=(s[i]==s[j]);
          else dp[j][i]=( s[j]==s[i] && dp[j+1][i-1] );
          /*若s.substr(j,i-j+1)是回文子串，更新当前最长回文子串的长度*/
          if(dp[j][i] && max<i-j+1) max=i-j+1;
      }
  }
  ```

* manacher法 (时间复杂度为$O(n)$)

  举个例子：`s="abbahopxpo"`，转换为`s_new="$#a#b#b#a#h#o#p#x#p#o#"`（这里的字符 $ 只是为了防止越界，下面代码会有说明），如此，s 里起初有一个偶回文`abba`和一个奇回文`opxpo`，被转换为`#a#b#b#a#`和`#o#p#x#p#o#`，长度都转换成了**奇数**。

  定义一个辅助数组`int p[]`，其中`p[i]`表示以 i 为中心的最长回文的半径，可以看出，`p[i] - 1`正好是原字符串中最长回文串的长度。设置两个变量，mx 和 id 。mx 代表以 id 为中心的最长回文的右边界，也就是`mx = id + p[id]`。求`p[i]`，也就是以 i 为中心的最长回文半径，如果`i < mx`，那么：
  
  ```c++
  if (i < mx)  
    p[i] = min(p[2 * id - i], mx - i);
  ```
  
  `2 * id - i`为 i 关于 id 的对称点，即上图的 j 点，而**p[j]表示以 j 为中心的最长回文半径**，因此可以利用`p[j]`来加快查找。
  
  ```c++
  int manacher(string s){
      int maxlen=1;
      string s1="$#";
      for(int i=0;i<s.size();i++)
      {
          s1+=s[i];
          s1+='#';
      }
      int id ,mx=0;
      vector<int> p(s1.size()-1);
      for(int i=0;i<s1.size()-1;i++)
      {
          /*1. 初步确定p[i]的值*/
          if(i<mx) p[i]=min(p[2*id-i],mx-i);
          else p[i]=1;
          /*2. 看p[i]有无扩大的可能*/
          while(s1[ i-p[i] ]==s1[ i+p[i] ]) 
            p[i]++;
          /*3. 更新id和mx*/
          if(i+p[i]>mx)
          {
              id=i;
              mx=i+p[i];
          }
          /*4. 更新maxlen*/
          maxlen=max(maxlen,p[i]-1);
      }
      return maxlen;
  }
  ```
  
  
