#x86_64 #calling-convention

> Integer valued arguments in the leftmost four positions are passed in left-to-right order in RCX, RDX, R8, and R9, respectively. The fifth and higher arguments are passed on the stack as previously described. All integer arguments in registers are right-justified, so the callee can ignore the upper bits of the register and access only the portion of the register necessary.

[1]: https://learn.microsoft.com/en-us/cpp/build/x64-calling-convention?view=msvc-170
