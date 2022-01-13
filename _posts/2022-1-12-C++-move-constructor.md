---
published: false
---
### This post is about C++ move constructor.

I felt like there's a lot of features that C++ provides behind the scenes, so I created a String class to manually move an object.

```C++
/*String class declaration*/
#pragma once

class String
{
private:
	char* m_strContent;
	int m_nSize;
public:
	String(const char* strInput);
	~String();
	String(String&& strInput) noexcept;
	void printString() const;
};
```
