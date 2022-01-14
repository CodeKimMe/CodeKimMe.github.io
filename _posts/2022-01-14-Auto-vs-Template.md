---
published: false
---
## How Auto differs from template type deduction.

(This post is a note from reading "Effective Modern C++" by Scott Meyers, all credits to Scott Meyers)

There's only one difference between auto and template type deduction. Let's look at the code below.

```cpp
#include <iostream>

template <class T>
void f1(T listParam)
{
	fprintf(stderr, "f1 called\n");
}

template <class T>
void f2(std::initializer_list<T> listParam)
{
	fprintf(stderr, "f2 called\n");
}

int main()
{
	auto listData = { 1,2,3,4 };
	f1({ 1,2,3,4 });
	f2({ 1,2,3,4 });
}
```