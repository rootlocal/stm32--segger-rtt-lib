# stm32-segger-rtt-lib

* https://wiki.segger.com/RTT

~~~ c
#ifdef REDIRECT_TO_RTT

#include "segger_rtt.h"

struct _reent;

_ssize_t _write(int file, const void *ptr, size_t len);

_ssize_t _write_r(struct _reent *r, int file, const void *ptr, size_t len);

int _read(int file, char *ptr, int len);

#endif

uint32_t counter = 0;

int main(void)
{

// ...

#ifdef REDIRECT_TO_RTT
    SEGGER_RTT_Init();
#endif

// ...

    while(1) {
        SEGGER_RTT_printf(0, "RTT printf counter: %lu\r\n", counter);
        printf("printf counter: %lu\r\n", counter);
        counter++;
        HAL_Delay(2000);
    }
}

#ifdef REDIRECT_TO_RTT

/// Use RTT redirection for scanf
int _read(int file, char *ptr, _ssize_t len) {
    *ptr = SEGGER_RTT_WaitKey();
    return 1;
}

/*********************************************************************
*
*       _write()
*
* Function description
*   Low-level write function.
*   libc subroutines will use this system routine for output to all files,
*   including stdout.
*   Write data via RTT.
*/
_ssize_t _write(int file, const void *ptr, size_t len) {
    (void) file;  /* Not used, avoid warning */
    SEGGER_RTT_Write(0, ptr, len);
    return len;
}

/*********************************************************************
*
*       _write_r()
*
* Function description
*   Low-level reentrant write function.
*   libc subroutines will use this system routine for output to all files,
*   including stdout.
*   Write data via RTT.
*/
_ssize_t _write_r(struct _reent *r, int file, const void *ptr, size_t len) {
    (void) file;  /* Not used, avoid warning */
    (void) r;     /* Not used, avoid warning */
    SEGGER_RTT_Write(0, ptr, len);
    return len;
}

#endif
~~~