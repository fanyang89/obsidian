#cpp

[Managing the lifetime of member functions bound by `std::bind`](https://stackoverflow.com/questions/29765940/managing-the-lifetime-of-member-functions-bound-by-stdbind)

```cpp
class A
{
public:
  void handle();
};

class B { ... };

// Later on, somewhere else...
std::vector< std::function< void() > functions;

A a;
B b;

functions.push_back( std::bind( &A::handle, &a ) );
functions.push_back( std::bind( &B::handle, &b ) );

// Even later:
for( auto&& f : functions )
  f(); // <--- How do I know whether f is still "valid"?
```

# Answer
You've to make a sort of framework, where the object dctor notify the container when it is destroyed, so the container could delete it from its "observers", the vector containing all the objects. To do that, the object must memorize in its instance the ptr to the container.