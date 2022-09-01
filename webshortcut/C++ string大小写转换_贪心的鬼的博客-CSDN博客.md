# C++ string大小写转换_贪心的鬼的博客-CSDN博客
```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

int main(){
    string str = "ancdANDG";
    cout << "转换前的字符串: " << str << endl;
    
    for(auto &i : str){
        i = toupper(i);
    }    
    cout << "转换后的字符串: " << str << endl;
    
    
    for(int i = 0;i < str.size();++i){
		str[i] = toupper(s[i]);
	}
	cout << "转换后的字符串： " << str << endl;
	
	return 0;
}

```

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;


int main(){
    string str = "helloWORLD";
    cout << "转换前：" << str << endl;
    
    
    transform(str.begin(), str.end(), str.begin(), ::toupper);    
    cout << "转换为大写：" << str << endl;    
    
    
    transform(str.begin(), str.end(), str.begin(), ::tolower);    
    cout << "转换为小写：" << str << endl; 
    
    
    transform(str.begin(), str.begin()+5, str.begin(), ::toupper);
    cout << "前五个字符转换为大写：" << str << endl; 
    
    
    transform(str.begin()+5, str.end(), str.begin()+5, ::toupper);
    cout << "前五个字符转换为大写：" << str << endl; 
    
    return 0;
}

```

 [https://blog.csdn.net/weixin_44515978/article/details/126578581](https://blog.csdn.net/weixin_44515978/article/details/126578581)
