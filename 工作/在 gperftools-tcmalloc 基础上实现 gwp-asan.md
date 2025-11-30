#gwp-asan #llvm

# 文档

[[https://google.github.io/tcmalloc/gwp-asan.html]]

# API

```c++
int64_t MallocExtension::GetGuardedSamplingRate();
void MallocExtension::SetGuardedSamplingRate(int64_t rate);
void MallocExtension::ActivateGuardedSampling();
```

# Develop logs

```
In file included from ../abseil-cpp/absl/base/thread_annotations.h:40:0,
                 from ../abseil-cpp/absl/base/internal/spinlock.h:46,
                 from ../gperftools/src/guarded_page_allocator.h:25,
                 from ../gperftools/src/tcmalloc.cc:129:
../abseil-cpp/absl/base/internal/thread_annotations.h:86:0: warning: "PT_GUARDED_BY" redefined
 #define PT_GUARDED_BY(x) THREAD_ANNOTATION_ATTRIBUTE__(pt_guarded_by(x))

In file included from ../gperftools/src/base/spinlock.h:46:0,
                 from ../gperftools/src/tcmalloc.cc:121:
../gperftools/src/base/thread_annotations.h:73:0: note: this is the location of the previous definition
 #define PT_GUARDED_BY(x) \
```

# 集成 llvm/gwp-asan 到 gperftools/tcmalloc

```c++
// gperftools/src/libc_override_glibc.h
#define ALIAS(tc_fn)   __attribute__ ((alias (#tc_fn)))
extern "C" {
  void* __libc_malloc(size_t size)                ALIAS(tc_malloc);  // return malloc_fast_path<tcmalloc::malloc_oom>(size);
  void __libc_free(void* ptr)                     ALIAS(tc_free);    // free_fast_path
  void* __libc_realloc(void* ptr, size_t size)    ALIAS(tc_realloc); // do_free do_realloc
  void* __libc_calloc(size_t n, size_t size)      ALIAS(tc_calloc);  //
  void __libc_cfree(void* ptr)                    ALIAS(tc_cfree);
  void* __libc_memalign(size_t align, size_t s)   ALIAS(tc_memalign);
  void* __libc_valloc(size_t size)                ALIAS(tc_valloc);
  void* __libc_pvalloc(size_t size)               ALIAS(tc_pvalloc);
  int __posix_memalign(void** r, size_t a, size_t s)  ALIAS(tc_posix_memalign);
}   // extern "C"
#undef ALIAS

void* operator new(size_t size) CPP_BADALLOC  ALIAS(tc_new);
void operator delete(void* p) CPP_NOTHROW     ALIAS(tc_delete);
void* operator new[](size_t size) CPP_BADALLOC ALIAS(tc_newarray);
void operator delete[](void* p) CPP_NOTHROW   ALIAS(tc_deletearray);
void* operator new(size_t size, const std::nothrow_t& nt) CPP_NOTHROW ALIAS(tc_new_nothrow);
void* operator new[](size_t size, const std::nothrow_t& nt) CPP_NOTHROW ALIAS(tc_newarray_nothrow);
void operator delete(void* p, const std::nothrow_t& nt) CPP_NOTHROW ALIAS(tc_delete_nothrow);
void operator delete[](void* p, const std::nothrow_t& nt) CPP_NOTHROW ALIAS(tc_deletearray_nothrow);
```
