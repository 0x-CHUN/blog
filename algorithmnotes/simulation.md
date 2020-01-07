# 模拟题

## 查找元素

* 对于小范围的直接遍历
* 对于递增或递减的可以二分查找。

二分查找

```C++
int binarySearch(vector<int> v,int val){
    int left = 0;
    int right = v.size()-1;
    while (left <= right){
        int mid = (left + right)/2;
        if(v[mid]==val)
            return mid;
        else if(v[mid] < val)
            left = mid + 1;
        else
            right = mid - 1;
    }
}
```

STL中：

```C++
#include  <algorithm>

lower_bound(起始地址，结束地址，要查找的数值) 返回的是数值 第一个 出现的位置。
upper_bound(起始地址，结束地址，要查找的数值) 返回的是数值 最后一个 出现的位置。
binary_search(起始地址，结束地址，要查找的数值)  返回的是是否存在这么一个数，是一个bool值。
```

## 图形输出

方法：

* 找规律，直接输出
* 定义二维字符数组，通过规律填充，然后输出。

## 日期处理

判断闰年：

```c++
bool isLeap(int year){
    return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
}
```

然后找规律或者直接模拟，一天天加。

基姆拉尔森计算公式：（计算星期几的）

```c++
int y;  //年
int m;  //月
int d;  //日
int w;  //周几
w = (d+2m+3(m+1)/5+y+y/4-y/100+y/400+1)%7
```

## 进制转换

* 任意2~36进制数转化为10进制

  ```C++
  int Atoint(string s,int radix)    //s是给定的radix进制字符串
  {
      int ans=0;
      for(int i=0;i<s.size();i++){
          char t=s[i];
          if(t>='0'&&t<='9') ans=ans*radix+t-'0';
          else ans=ans*radix+t-'a'+10;
      }
          return ans;
  }
  // strol()函数：
  // 函数原型：long int strtol(const char *nptr, char **endptr, int base)
  // 格式：base是要转化的数的进制，非法字符会赋值给endptr，nptr是要转化的字符，
  ```

* 将10进制数转换为任意的n进制数

  ```C++
  string intToA(int n,int radix)    //n是待转数字，radix是指定的进制
  {
      string ans="";
      do{
          int t=n%radix;
          if(t>=0&&t<=9)    ans+=t+'0';
          else ans+=t-10+'a';
          n/=radix;
      }while(n!=0);    //使用do{}while（）以防止输入为0的情况
      reverse(ans.begin(),ans.end());
      return ans;    
  }
  // itoa（）函数（可以将一个10进制数转换为任意的2-36进制字符串）:
  // 函数原型：char*itoa(int value,char*string,int radix);
  // 格式：itoa(num, str, 2); num是一个int型的，是要转化的10进制数，str是转化结果，后面的值为目标进制。
  ```

  指定格式输出：

  ```c++
  #include <bitset>  
  #include<iostream>
  using namespace std;  
  int main()  
  {  
      cout << "35的8进制:" << std::oct << 35<< endl;  
      cout << "35的10进制" << std::dec << 35 << endl;  
      cout << "35的16进制:" << std::hex << 35 << endl;  
      cout << "35的2进制: " << bitset<8>(35) << endl;      //<8>：表示保留8位输出
      return 0;  
  }
  ```

## 字符处理

回文：

```c++
bool judge(string s){
    int len = s.length();
    for (int i = 0; i < len; i++){
        if(s[i] != s[len-1-i])
            return false;
    }
    return true;
}
```

常见处理：

|                     | string                                                       | char*、char[]                                                |
| :-----------------: | ------------------------------------------------------------ | ------------------------------------------------------------ |
|     **头文件**      | #include <string>                                            | 不需要                                                       |
|  **定义与初始化**   | string s1(“abc”); string s2(s1); string s3(4, ‘s’);//初始化为4个’s’ | char* a = “test”;//数据存在静态存储区，不能修改 char a[] = “test”;//开辟数组再存储，可以修改 char* a = new char[10]; memset(a, ‘0’, sizeof(char)*10); |
|    **相互转化**     | char* p = “hello”; string s(p); s = p;                       | string str(“test”); const char* p = str.data();//记得要加const或者强制类型转换成(char*) const char* p = str.c_str(); char p[10]; std::size_t length = str.copy(p,5,0);//从第0个开始复制5个，返回有效复制的数量，需要在p最后添加’\0’ char * cstr = new char [str.length()+1]; std::strcpy (cstr, str.c_str()); 或者逐个复制 |
|    **实际大小**     | str.size()                                                   | std::strlen(p)//#include <cstring>（C++写法）或者<string.h>（C写法） |
|    **容器大小**     | str.capacity()                                               | 数组形式p[]，可以使用sizeof(p)来获得数组大小 指针形式没有容器概念，除非是new的，对指针用sizeof将得到指针本身的大小，由系统位数决定 |
|      **倒置**       | #include <algorithm> // std::reverse std::reverse(str.begin(),str.end()); | char* strrev(char* s);                                       |
| **查找字符&字符串** | find//从头开始找 rfind//从尾开始找 **这四个函数都有四种重载：** size_t find (const string& str, size_t pos = 0);//查找子string，默认从父string的第0个字符开始，如果要查找多个相似的，则可以将pos设置为上次查找到的+1 size_t find (const char* s, size_t pos = 0);//查找字符串，默认从0开始 size_t find (const char* s, size_t pos, size_type n);//同上，但只比较n个 size_t find (char c, size_t pos = 0);//比较字符。 当然，形参初始化的值可能不一样，返回的都是地址索引，需要通过found!=std::string::npos判断是否有效。  find_first_of find_last_of find_first_not_of find_last_not_of 也有上面四种重载，不过这里是返回第一个出现、没有出现在子str的字符的索引。 如 std::string str (“look for non-alphabetic characters…”); std::size_t found = str.find_first_not_of(“abcdefghijklmnopqrstuvwxyz “); 将返回’-‘的索引 | char* strchr(char* s, char c);//查找字符串s中首次出现字符c的位置，返回c位置的指针，如不存在返回NULL  char * strrchr(const char *str, int c);//查找字符倒数第一次出现的位置char \*strstr(const char \*s1,const char \*s2);//查找第一次出现s2的位置，返回s2的位置指针，如不存在返回NULLchar \*strrstr(const char \*s1,const char \*s2);//查找倒数第一个出现s2的位置int strspn(const char* s,const char *accept);//作用同右侧find_first_not_of。返回s中第一个没有在accept出现的字符的索引。通过两个for循环来实现int strcspn(const char* s,const char *reject);//返回s中第一个在reject出现的字符的索引 |
|   **大小写转换**    | 两者都是不提供这个功能的，但是C++有两个库函数，头文件是#include <ctype.h>： int tolower ( int c ); int toupper ( int c ); | 实现也很简单： int tolower(int c) { 　　if ((c >= ‘A’) && (c <= ‘Z’)) 　　　　return c + (‘a’ - ‘A’); 　　return c; } |
| **比较字符串大小**  | 一共五种重载形式 int compare (const string& str); int compare (size_t pos, size_t len, const string& str); int compare (size_t pos, size_t len, const string& str, size_t subpos, size_t sublen); int compare (const char* s) const; int compare (size_t pos, size_t len, const char* s); int compare (size_t pos, size_t len, const char* s, size_t n); 返回与右边一致，其中pos表示从str的第几个元素开始比较，一共比较len个字符，如果不够，则有多少比较多少。subpos、sublen是相对比较字符串而言的  string重载了==、<等符号，可以直接用符号比较 string没有提供不区分大小写的比较，感觉可以读取出数据后通过右侧的函数来进行比较。 | int strcmp(char* s1, char* s2);//区分字母大小写比较 当s1 < s2时，返回值<0； //对于第一个不相等的字符，s1对应的小于s2对应的，或者全相等，但是s1的字符数量少于s2的字符数量 当s1 = s2时，返回值=0； 当s1 > s2时，返回值>0。  int stricmp(char* s1, char* s2);//不区分字母大小写  int strncmp(char* s1, char* s2, int n);//只比较前n个字符，区分大小  int strnicmp(char* s1, char* s2, int n);//之比较前n，不区分大小写 |
|  **数字转字符串**   | 1、stringstream（多次使用需要使用clear()清除） int N = 10; stringstream ss;//#include <sstream> string str; ss « N; ss » str; 2、string = std::to_string(N)方法 只需包含<string>头文件即可 | 1、使用sprintf：#include <stdio.h> char c[100]; int k=255; sprintf(c,”%d”,k);//d是有符号10进制数，u是无符号10进制  double a=123.321; sprintf(c,”%.3lf”,a);  sprintf(c,”%x”,k);//转换成16进制，如果想要大写的话可以用X，8进制是o //c包含”ff” c[0]=’f’ c[1]=’f’  2、itoa貌似跟平台有关，不建议使用。 |
|  **字符串转数字**   | 1、N = stringToNum<int>(str);//需要#include <sstream>  2、在<string>中实现的  int stoi (const string& str, size_t* idx = 0, int base = 10);//idx返回第一个非数字的指针索引，base默认10进制  long stol、unsigned long stoul、long long stoll、unsigned long long stoull、float stof、double stod、long double stold  3、stringstream//#include <sstream> string a = “123.32”; double res; stringstream ss; ss « a; ss » res; | char a[10] = {“255”}; int b; sscanf(a,”%d”,&b);  char a[10] = {“ff”};//十六进制 int b; sscanf(a,”%x”,&b);  char str[]=”123.321”;  double a;  sscanf(str,”%lf”,&a);  另外也可以用atoi(),atol(),atof() |
|   **拷贝与合并**    | char p[10]; std::size_t length = str.copy(p,5,0);//从第0个开始复制5个，返回有效复制的数量，需要在p最后添加’\0’  合并直接用+号即可 或者用.append() string& append (const string& str); string& append (const string& str, size_t subpos, size_t sublen); string& append (const char* s); string& append (const char* s, size_t n); string& append (size_t n, char c); | char* strcpy(char* dest, char* src);//把从src地址开始且含有 ‘\0’结束符的字符串复制到以dest开始的地址空间。返回指向dest的指针。  char* strncpy(char* dest, char* src, int size_tn);//复制n个，这个复制不保证NULL结尾，需要自己初始化。  char* strcat(char* dest, char* src);//把src所指字符串添加到dest结尾处(覆盖dest结尾处的 ‘\0’)并添加 ‘\0’。返回指向dest的指针。且内存不可重叠。  char* strncat(char* dest, char* src, int n);//部分合并  用sprintf也行，但是效率不高。 |
|   **插入与替换**    | string& insert (size_t pos, const string& str); string& insert (size_t pos, const string& str, size_t subpos, size_t sublen); string& insert (size_t pos, const char* s); string& insert (size_t pos, const char* s, size_t n); string& insert (size_t pos,   size_t n, char c);  string& replace (size_t pos,        size_t len,        const string& str); string& replace (size_t pos,        size_t len,        const string& str,                 size_t subpos, size_t sublen); string& replace (size_t pos,        size_t len,        const char* s); string& replace (size_t pos,        size_t len,        const char* s, size_t n); string& replace (size_t pos,        size_t len,        size_t n, char c); | 不存在                                                       |
|   **字符串切割**    | string substr (size_t pos = 0, size_t len = npos)：  std::string str2 = str.substr (3,5);//返回str的第3个开始的5个元素。可以配合find函数来实现针对特殊元素的切割 | char* strtok (char* str,constchar* delimiters  );//以delimiters中的每个字符作为分割符，将str剪切，返回一个个字符串。传入NULL时代表继续切割后续的。下面的程序预计输出hello  boy this is heima。第二个参数可以是”  ,\t\n\\“等。（其实就是将被切割的换成’\0’？待分割字符串的完整性会被破坏，而且线程不安全。 |
|     **IO函数**      | std::getline (std::cin,str,s);//这个不需要固定大小，但是速度慢，生成文件大。默认终止符是换行 | cin.get(arrayname,size,s)//读取size-1个字符，在末尾添加’\0’遇终止符停止，返回是否成功。读取玩指针指向终止符。s默认换行  cin.getline(arrayname,size,s)//同上，只是指针指向终止符之后 |