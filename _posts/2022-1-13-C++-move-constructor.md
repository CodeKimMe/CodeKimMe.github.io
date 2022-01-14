---
published: true
---
### This post is about C++ move constructor.

(This post is a note from reading "Effective Modern C++" by Scott Meyers, all credits to Scott Meyers)

I felt like there's a lot of features that C++ provides behind the scenes, so I created a String class to manually move an object.

Version above C++11.

String.h file.
```cpp
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

String class has a pointer to the actual string and a size as member variables. For the sake of simplicity, I omitted a lot of functionalities the std::string has.

Here's what I want to achieve. 
```cpp
int main(int argc, char** argv)
{
	String strName = "Kyle";
	String strCopy = std::move(strName);
	strName.printString();
	strCopy.printString();
}
```
I want to create a String with content "Kyle", and then create a copy of that string without memory copy. Since std::move is just a static_cast to rvalue reference, I have to define move constructor in order to move the String(I can also do this by overriding operator=()(String&&) function, but I'll save that for later).

Since String(const char* strInput) is defined the way you'd expect it to be, I'll leave the definition at the bottom of the page with the rest obvious functions. Now let's look at the move constructor's definition.

```cpp
String::String(String&& strInput) noexcept
{
	m_nSize = std::move(strInput.m_nSize);
	m_strContent = strInput.m_strContent;
	strInput.m_strContent = nullptr;
	strInput.m_nSize = 0;
	fprintf(stderr, "move constructor called\n");
}
```
If you want to use default move constructor by the way, you can just set the move constructor equal to default, but it would not work the way we want in this case since we free the memory in the destructor. 
As we can see from the move constructor's definition, we literally move the content without copying memory. 

To sum up, move constructor can literally move the content without memory copy as above. Below is the rest of the code.

```cpp
String::String(const char* strInput) :
	m_strContent{},
	m_nSize{}
{
	const char* pRunner = strInput;
	while (*pRunner != '\0')
	{
		m_nSize++;
		pRunner++;
	}
	m_strContent = (char*)malloc((size_t)(m_nSize + 1));
	for (int i = 0; i < m_nSize; i++)
	{
		m_strContent[i] = strInput[i];
	}
	m_strContent[m_nSize] = '\0';
	fprintf(stderr, "constructor called\n");
}

String::~String()
{
	if (m_strContent)
	{
		delete[]m_strContent;
		m_strContent = nullptr;
		m_nSize = 0;
		fprintf(stderr, "destructor called for String with content\n");
	}
	else
		fprintf(stderr, "destructor called for String without content\n");
}

void String::printString() const
{
	for (int i = 0; i < m_nSize; i++)
	{
		fprintf(stderr, "%c", m_strContent[i]);
	}
	fprintf(stderr, "\n");
}
```

Thanks, I will definitely find a way to open up a comment section to interact with whoever wants to leave a comment!
