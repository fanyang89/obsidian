```
==================
WARNING: ThreadSanitizer: double lock of a mutex (pid=1228066)
    #0 pthread_mutex_lock <null> (libtsan.so.0+0x00000003bd9e)
    #1 __gthread_mutex_lock /usr/include/c++/7.3.0/x86_64-linux-gnu/bits/gthr-default.h:748 (zbs_test+0x000004d1ec29)
    #2 std::mutex::lock() /usr/include/c++/7.3.0/bits/std_mutex.h:103 (zbs_test+0x000004d1ec29)
    #3 std::lock_guard<std::mutex>::lock_guard(std::mutex&) /usr/include/c++/7.3.0/bits/std_mutex.h:162 (zbs_test+0x000004d1ec29)
    #4 AsyncWriteLog ../glog/src/logging.cc:1173 (zbs_test+0x000004d1ec29)
    #5 __invoke_impl<void, void (google::(anonymous namespace)::LogFileObject::*)(), google::(anonymous namespace)::LogFileObject*> /usr/include/c++/7.3.0/bits/invoke.h:73 (zbs_test+0x000004d0fefa)
    #6 __invoke<void (google::(anonymous namespace)::LogFileObject::*)(), google::(anonymous namespace)::LogFileObject*> /usr/include/c++/7.3.0/bits/invoke.h:95 (zbs_test+0x000004d0fefa)
    #7 _M_invoke<0, 1> /usr/include/c++/7.3.0/thread:234 (zbs_test+0x000004d0fefa)
    #8 operator() /usr/include/c++/7.3.0/thread:243 (zbs_test+0x000004d0fefa)
    #9 _M_run /usr/include/c++/7.3.0/thread:186 (zbs_test+0x000004d0fefa)
    #10 <null> <null> (libstdc++.so.6+0x0000000bc5fe)

  Location is heap block of size 608 at 0x7b5400000f00 allocated by main thread:
    #0 operator new(unsigned long) <null> (libtsan.so.0+0x00000006fed6)
    #1 google::LogDestination::log_destination(int) ../glog/src/logging.cc:996 (zbs_test+0x000004d16f47)
    #2 google::LogDestination::SetLogDestination(int, char const*) ../glog/src/logging.cc:786 (zbs_test+0x000004d16f47)
    #3 google::SetLogDestination(int, char const*) ../glog/src/logging.cc:1869 (zbs_test+0x000004d16f47)
    #4 InitLogging ../src/common/main.cc:103 (zbs_test+0x00000215b20d)
    #5 zbs::InitCommonRuntime(char**) ../src/common/main.cc:211 (zbs_test+0x00000215b20d)
    #6 main ../src/tests/test_main.cc:130 (zbs_test+0x000004433f40)

  Mutex M338749997690392880 is already destroyed.

SUMMARY: ThreadSanitizer: double lock of a mutex (/usr/lib64/libtsan.so.0+0x3bd9e) in __interceptor_pthread_mutex_lock
==================
```
