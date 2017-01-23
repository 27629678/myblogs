## 0x01 EXC\_BAD\_INSTRUCTION

`EXC_BAD_INSTRUCTION` is usually caused by two things:

- You’ve jumped (via a function pointer, or method dispatch with a corrupt object, or by smashing the stack, or whatever) to invalid code;
- You’ve hid an invalid opcode (typically ud2) inserted by the compiler as a trap mechanism;


When writing Swift code the latter is by far the most common.  In this case it typically means that one of the Swift safety checks has failed, for example:

- Accessing a nil optional
- Accessing out of bounds on an array
- Integer truncation, like trying to put an out of range value into a UInt8

It’s hard to say what’s actually failing without a better backtrace.  In Xcode, go to the Debug Navigator, find the thread that crashed, and look at its full backtrace.