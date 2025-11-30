#Linux #libc

# 枚举

```c
/* for poll() */
#define POLLIN          0x0001
#define POLLPRI         0x0002
#define POLLOUT         0x0004
#define POLLERR         0x0008
#define POLLHUP         0x0010
#define POLLNVAL        0x0020
```

# poll invalid fd

```c
#include <stdio.h>
#include <unistd.h>
#include <poll.h>
#include <errno.h>

int main() {
    struct pollfd fds;
    int ret;

    fds.fd = -1;
    fds.events = POLLIN;

    ret = poll(&fds, 1, 1000);
    if (ret == -1) {
        perror("poll() failed");
    } else {
        printf("poll() returned %d\n", ret);
    }

    return 0;
}
```

```bash
-> % time ./a.out
poll() returned 0
./a.out  0.00s user 0.00s system 0% cpu 1.002 total
```

# poll closed fd

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <poll.h>
#include <errno.h>

int main() {
    int sockfd;
    struct pollfd fds;

    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return 1;
    }

    if (close(sockfd) == -1) {
        perror("close");
        return 1;
    }

    fds.fd = sockfd;
    fds.events = 0; // in zookeeper client, the interest is zero after socket error

    int ret = poll(&fds, 1, 1000);
    if (ret == -1) {
        perror("poll");
        return 1;
    } else {
        printf("poll() returned %d, revents: 0x%x, events: 0x%x\n", ret, fds.revents, fds.events);
    }

    return 0;
}
```

结果

```bash
-> % ./a.out
poll() returned 1, revents: 0x20, events: 0x0
# POLLNVAL
```
