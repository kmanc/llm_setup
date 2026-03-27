# Python Coding Standards


## Dictionaries
- Prefer `.get()` to direct key access
- Use `defualtdict`, `Counter`, and `OrderedDict` from collections liberaly; do not rely on dictionary ordering from the standard library


## File access
- Use `with open()` when accessing files


## Memory management
- Generators and lazy loading save RAM, take advantage of this where it makes sense


## Code Quality
- List and dictionary comprehensions are ok, but don't try to do too much in one line; lambdas are usually not the best way to solve anything
- The zen of python is a fantastic set of guiding principals

#### Zen of Python
```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```
